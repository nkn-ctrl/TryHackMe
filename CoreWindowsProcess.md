# Core Windows Process

## Task Manager
- Datails tab
Add some additional columns to see more information about these processes. Good columns to add are ```Image path name``` and ```Command line```.<br>
- running processes on a Windows system:<br>
    ```tasklist```, ```Get-Process``` or ```ps``` (PowerShell), and ```wmic```.<br>

## System
The PID for System is always 4.<br>
*The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread.*<br>
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/CWP_system2.png"><br>
What is unusual behaviour for this process?
- A parent process (aside from System Idle Process (0))
- Multiple instances of System. (Should only be one instance) 
- A different PID. (Remember that the PID will always be PID 4)
- Not running in Session 0

## System > smss.exe
This process, also known as the Windows *Session Manager Subsystem*, is responsible for creating new sessions.It is the first user-mode process started by the kernel.<br>
smss.exe --> csrss.exe wininit.exe (session 0, Windows session)<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/smss-session0b.png">
&nbsp;|-> csrss.exe winlogon.exe (session 1, User session)<br>


