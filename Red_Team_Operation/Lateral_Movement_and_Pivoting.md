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