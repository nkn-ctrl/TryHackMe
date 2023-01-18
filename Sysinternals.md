# Sysinternals

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








