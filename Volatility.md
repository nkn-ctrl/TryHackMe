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
- VMWare - .vmem
- Hyper-V - .bin
- Parallels - .mem
- VirtualBox - .sav file *this is only a partial memory file  

## Identifying Image Info and Profiles
OS profiles have since been deprecated with Volatility3, so we will only need to worry about identifying the profile if using Volatility2; this makes life much easier for analyzing memory dumps.  
If we are still looking to get information about what the host is running from the memory dump, we can use the following three plugins `windows.info` `linux.info` `mac.info`.  
<br>

`python3 vol.py -f <file> windows.info`

## Listing Processes and Connections
### `pslist`


