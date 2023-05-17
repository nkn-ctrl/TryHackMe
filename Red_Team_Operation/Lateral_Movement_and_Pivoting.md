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
