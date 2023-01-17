# Sysinternals
## Using Sysinternals Live
Simply enter a tool's Sysinternals Live path into Windows Explorer or a command prompt as live.```sysinternals.com/<toolname>``` or ```\\live.sysinternals.com\tools\<toolname>```.

## File and Disk Utilities

### Sigcheck
File version and signature viewer
"Sigcheck is a command-line utility that shows file version number, timestamp information, and digital signature details, including certificate chains.<br>
- Use Case: Check for unsigned files in C:\Windows\System32.<br>
- Command: ```sigcheck -u -e C:\Windows\System32```<br>
- parameter usage:
    - `-u` "If VirusTotal check is enabled, show files that are unknown by VirusTotal or have non-zero detection, otherwise show only unsigned files."
    - `-e` "Scan executable images only (regardless of their extension)"<br>

### Streams
Reveal NTFS alternate streams.<br>
 - Example: A file downloaded from the Internet.<br>
    <img src="https://assets.tryhackme.com/additional/sysinternals/streams2.png" width="500"><br>

### [SDelete](https://learn.microsoft.com/en-us/sysinternals/downloads/sdelete)
SDelete is a command line utility that takes a number of options. In any given use, it allows you to delete one or more files and/or directories, or to cleanse the free space on a logical disk. SDelete accepts wild card characters as part of the directory or file specifier.<br>




<u>Use Case</u>: <br>
<u>Command</u>: <br>

