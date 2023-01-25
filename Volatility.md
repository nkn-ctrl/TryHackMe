# Volatility 3

## Installing Volatility
https://github.com/volatilityfoundation/volatility3  
<br>

Download optional dependencies: 
- `yara-python 3.8.0` or `later`     https://github.com/VirusTotal/yara-python
- `capstone 3.0.0` or `later`    https://www.capstone-engine.org/download.html
<br>

Linux or Mac memory files:  https://github.com/volatilityfoundation/volatility3#symbol-tables  
<br>

## Memory Extraction
Dump tool  
- TK Imager
- Redline
- DumpIt.exe
- win32dd.exe / win64dd.exe
- Memoryze
- FastDump  

Out put `a .raw` file  
For virtual machines, gathering a memory file can easily be done by collecting the virtual memory file from the host machine’s drive.  
- VMWare - `.vmem`
- Hyper-V - `.bin`
- Parallels - `.mem`
- VirtualBox - `.sav` file *\*this is only a partial memory file*  

## [Volatility 3 Cheetsheet](https://blog.onfvp.com/post/volatility-cheatsheet/)

## Identifying Image Info and Profiles
OS profiles have since been deprecated with Volatility3, so we will only need to worry about identifying the profile if using Volatility2; this makes life much easier for analyzing memory dumps.  
If we are still looking to get information about what the host is running from the memory dump, we can use the following three plugins `windows.info` `linux.info` `mac.info`.  
<br>

`python3 vol.py -f <file> windows.info`

## Listing Processes and Connections
### `pslist`
The most basic way of listing processes.this plugin will get the list of processes from the doubly-linked list that keeps track of processes in memory, equivalent to the process list in task manager.
`python3 vol.py -f <file> windows.pslist`

### `psscan`
Some malware, typically rootkits, will, in an attempt to hide their processes, `psscan`  will locate processes by finding data structures that match `_EPROCESS`. But it can also cause false positives.
`python3 vol.py -f <file> windows.psscan`  

###  `pstree`
`python3 vol.py -f <file> windows.pstree`

### `netstat`
netstat will attempt to identify all memory structures with a network connection.  
`python3 vol.py -f <file> windows.netstat`

### `dlllist`
This plugin will list all DLLs associated with processes at the time of extraction.  
`python3 vol.py -f <file> windows.dlllist`  

### `memmap`
`vol.py -f “/path/to/file” -o “/path/to/dir” windows.memmap ‑‑dump ‑‑pid <PID>`  

## Volatility Hunting and Detection Capabilities
### `malfind`
Hunting for code injection. The plugin works by scanning the heap and identifying processes that have the executable bit set `RWE` or `RX` and/or no memory-mapped file on disk (file-less malware).  
`python3 vol.py -f <file> windows.malfind`  

### `yarascan`
`yarascan` will search for strings, patterns, and compound rules against a rule set.  
`python3 vol.py -f <file> yarascan.YaraScan --yara-file /path/to/yara/file.yara`  
`python3 vol.py -f <file> windows.vadyarascan.VadYaraScan --yara-file /path/to/yara/file.yara`  
`python3 vol.py -f <file> windows.vadyarascan ‑‑yara-rules <string>`  

## Advanced Memory Forensics
Hooking; there are five methods of hooking employed by adversaries
- SSDT Hooks
- IRP Hooks
- IAT Hooks
- EAT Hooks
- Inline Hooks  
<br>

The `ssdt` plugin will search for hooking and output its results. SSDT hooking is: *SSDT stands for System Service Descriptor Table; the Windows kernel uses this table to look up system functions.* An adversary can hook into this table and modify pointers to point to a location the rootkit controls.  
There can be hundreds of table entries that ssdt will dump. A suggestion is to use this plugin after investigating the initial compromise and working off it as part of your lead investigation.  
`python3 vol.py -f <file> windows.ssdt`  
<br>

Adversaries will also use malicious driver files as part of their evasion.   
The `modules` plugin will dump a list of loaded kernel modules; this can be useful in identifying active malware. However, if a malicious file is idly waiting or hidden, this plugin may miss it.  
This plugin is best used once you have further investigated and found potential indicators to use as input for searching and filtering.  
`python3 vol.py -f <file> windows.modules`  
<br>

The `driverscan` plugin will scan for drivers present on the system at the time of extraction. This plugin can help to identify driver files in the kernel that the `modules` plugin might have missed or were hidden.  
`python3 vol.py -f <file> windows.driverscan`  
<br>

There are also other plugins listed below that can be helpful when attempting to hunt for advanced malware in memory.
- modscan
- driverirp
- callbacks
- idt
- apihooks
- moddump
- handles  

