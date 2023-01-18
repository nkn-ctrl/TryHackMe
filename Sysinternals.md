# Sysinternals
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/sysinternals_logo.jpg" width="800"><br>

## Using Sysinternals Live
Simply enter a tool's Sysinternals Live path into Windows Explorer or a command prompt as live.```sysinternals.com/<toolname>``` or ```\\live.sysinternals.com\tools\<toolname>```.

## [File and Disk Utilities](https://learn.microsoft.com/en-us/sysinternals/downloads/file-and-disk-utilities)

### [Sigcheck](https://learn.microsoft.com/en-us/sysinternals/downloads/sigcheck)
File version and signature viewer<br>
"Sigcheck is a command-line utility that shows file version number, timestamp information, and digital signature details, including certificate chains.<br>
- Use Case: Check for unsigned files in C:\Windows\System32.<br>
- Command: ```sigcheck -u -e C:\Windows\System32```<br>
- parameter usage:
    - `-u` "If VirusTotal check is enabled, show files that are unknown by VirusTotal or have non-zero detection, otherwise show only unsigned files."
    - `-e` "Scan executable images only (regardless of their extension)"<br>

### [Streams](https://learn.microsoft.com/en-us/sysinternals/downloads/streams)
Reveal NTFS alternate streams.<br>
 - Example: A file downloaded from the Internet.<br>
    <img src="https://assets.tryhackme.com/additional/sysinternals/streams2.png" width="500"><br>

Use `notepad .\file.txt:example.txt` to open the stream file.

### [SDelete](https://learn.microsoft.com/en-us/sysinternals/downloads/sdelete)
SDelete is a command line utility that takes a number of options. In any given use, it allows you to delete one or more files and/or directories, or to cleanse the free space on a logical disk. SDelete accepts wild card characters as part of the directory or file specifier.<br>

## [Networking Utilities](https://docs.microsoft.com/en-us/sysinternals/downloads/networking-utilities)
### [TCPView](https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview)
<img src="https://assets.tryhackme.com/additional/sysinternals/tcpview.png"><br>

## Process Utilities

### Autoruns
`C:\Users\Administrator> autoruns -accepteula`

### ProcDump
ProcDump is a command-line utility whose primary purpose is monitoring an application for CPU spikes and generating crash dumps during a spike that an administrator or developer can use to determine the cause of the spike."<br>
Alternatively, you can use ***Process Explorer*** to do the same.<br>Right-click on the process to *create a Minidump* or *Full Dump* of the process.<br>

### Process Explorer
[CoreWindowsProcess.md](https://github.com/nkn-ctrl/TryHackMe/blob/main/CoreWindowsProcess.md#core-windows-process)

### Process Monitor
Process Monitor is an advanced monitoring tool for Windows that shows real-time file system, Registry and process/thread activity.<br>
[The Ultimate Guide to Procmon](https://adamtheautomator.com/procmon/)<br>

### [PsExec](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec)
PsExec is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software. <br>

## Security Utilities
### Sysmon
System Monitor (Sysmon) is a Windows system service and device driver that, once installed on a system, remains resident across system reboots to monitor and log system activity to the Windows event log. It provides detailed information about process creations, network connections, and changes to file creation time. By collecting the events it generates using Windows Event Collection or SIEM agents and subsequently analyzing them, you can identify malicious or anomalous activity and understand how intruders and malware operate on your network.<br>
<br>

Events within Sysmon are stored in `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`<br>



## Miscellaneous
### BgInfo
It automatically displays relevant information about a Windows computer on the desktop's background, such as the computer name, IP address, service pack version, and more.<br>
`C:> bginfo -accepteula`<br>

### RegJump
Using Regjump will open the Registry Editor and automatically open the editor directly at the path, so one doesn't need to navigate it manually.<br>

<img src="https://assets.tryhackme.com/additional/sysinternals/regjump2.png" width="800">

### Strings
<img src="https://assets.tryhackme.com/additional/sysinternals/strings.png" width="500">

## Conclusion
- Mark's Blog - https://docs.microsoft.com/en-us/archive/blogs/markrussinovich/
- Windows Blog Archive - https://techcommunity.microsoft.com/t5/windows-blog-archive/bg-p/Windows-Blog-Archive/label-name/Mark%20Russinovich
- License to Kill: Malware Hunting with Sysinternals Tools - https://www.youtube.com/watch?v=A_TPZxuTzBU
- Malware Hunting with Mark Russinovich and the Sysinternals Tools - https://www.youtube.com/watch?v=vW8eAqZyWeo



