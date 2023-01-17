# Core Windows Process

## Task Manager
- Datails tab
Add some additional columns to see more information about these processes. Good columns to add are ```Image path name``` and ```Command line```.<br>
- running processes on a Windows system:<br>
    ```tasklist```, ```Get-Process``` or ```ps``` (PowerShell), and ```wmic```.<br>

## System
The PID for System is always 4.<br>
*The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread.*<br>
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/CWP_system2.png" width="400" ><br>
What is unusual behaviour for this process?
- A parent process (aside from System Idle Process (0))
- Multiple instances of System. (Should only be one instance) 
- A different PID. (Remember that the PID will always be PID 4)
- Not running in Session 0

## System > smss.exe
This process, also known as the Windows *Session Manager Subsystem*, is responsible for creating new sessions.It is the first user-mode process started by the kernel.<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/smss.png" width="600"><br>
**smss.exe** --> csrss.exe, wininit.exe (session 0, Windows session)<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/smss-session0b.png"><br>
**smss.exe** --> csrss.exe, winlogon.exe (session 1, User session)<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/smss-session1b.png"><br>

Subsystem list<br>
 ```Required``` value of ```HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems```<br>
 <br>
What is unusual?
- A different parent process other than System (4)
- The image path is different from C:\Windows\System32
- More than one running process. (children self-terminate and exit after each new session)
- The running User is not the SYSTEM user
- Unexpected registry entries for Subsystem<br>

## csrss.exe
**csrss.exe (Client Server Runtime Process)** is the user-mode side of the Windows subsystem. This process is responsible for the Win32 console window and process thread creation and deletion, making the Windows API available to other processes, mapping drive letters, and handling the Windows shutdown process.<br>
<br>
Session 0(PID 392)<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/csrss-session0.png" width="600"><br>
Session 1(PID 512)<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/csrss-session1.png" width="600"><br>
<br>
What is unusual?
- An actual parent process. (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes masquerading as csrss.exe in plain sight
- The user is not the SYSTEM user.

## wininit.exe
The **Windows Initialization Process, wininit.exe**, is responsible for launching services.exe (Service Control Manager), lsass.exe (Local Security Authority), and lsaiso.exe within Session 0. <br>
<img src="https://assets.tryhackme.com/additional/windows-processes/wininit.png" width="600"><br>
<br>
What is unusual?
- An actual parent process. (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances
- Not running as SYSTEM

## wininit.exe > services.exe
**Service Control Manager** (SCM) or **services.exe** has responsibility to handle system services: loading services, interacting with services and starting or ending services.Information regarding services is stored in the registry, ```HKLM\System\CurrentControlSet\Services```. <br>
This process also loads device drivers marked as auto-start into memory. When a user logs into a machine successfully, this process is responsible for setting the value of the Last Known Good control set (Last Known Good Configuration), ```HKLM\System\Select\LastKnownGood```, to that of the CurrentControlSet. <br>
<img src="https://assets.tryhackme.com/additional/windows-processes/services.png" width="600"><br>
<br>
What is unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances (Number of instances is ONE)
- Not running as SYSTEM

## wininit.exe > services.exe > svchost.exe
The **Service Host** (Host Process for Windows Services), or **svchost.exe**, is responsible for hosting and managing Windows services. The services running in this process are implemented as DLLs. The DLL to implement is stored in the registry for the service under the ```Parameters``` subkey in ```ServiceDLL```. The full path is ```HKLM\SYSTEM\CurrentControlSet\Services\SERVICE NAME\Parameters```.  
There is a key identifier in the binary path, and that identifier is ```-k``` . This is how a legitimate svchost.exe process is called.<br>
<br>
<img src="https://assets.tryhackme.com/additional/windows-processes/svchost.png" width="400"><br>
<br>
What is unusual?
- A parent process other than services.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- The absence of the -k parameter




