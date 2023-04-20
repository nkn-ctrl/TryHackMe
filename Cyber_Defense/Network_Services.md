# Network Services

## SMB - Server Message Block Protocol -  
[SMB](https://searchnetworking.techtarget.com/definition/Server-Message-Block-Protocol) is a client-server communication protocol used for sharing access to files, printers, serial ports and other resources on a network.   

### Enumerating SMB
- Port Scanning
    nmap 
- Enum4Linux
    `enum4linux [options] [ip]`  
    `-U` get userlist  
    `-M` get machine list  
    `-N` get namelist dump (different from -U and-M)  
    `-S` get sharelist  
    `-P` get password policy information  
    `-G` get group and member list  
    `-a` all of the above (full basic enumeration)  

### Exploiting SMB
While there are vulnerabilities such as [CVE-2017-7494](https://www.cvedetails.com/cve/CVE-2017-7494/) that can allow remote code execution by exploiting SMB, you're more likely to encounter a situation where the best way into a system is due to misconfigurations in the system.  
- SMBClient
    `smbclient //[IP]/[SHARE]`  
    `-U [name]` : to specify the user  
    `-p [port]` : to specify the port  

## Telnet
Telnet is an application protocol which allows you, with the use of a telnet client, to connect to and execute commands on a remote machine that's hosting a telnet server.  
`telnet [ip] [port]`  

### Exploit Telnet


## FTP - File Transfer Protocol -
FTP is a protocol used to allow remote transfer of files over a network.  
A typical FTP session operates using two channels:
- a command (sometimes called the control) channel
- a data channel.  

