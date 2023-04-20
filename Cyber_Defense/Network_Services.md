# Network Services

## SMB - Server Message Block Protocol -  
[SMB](https://searchnetworking.techtarget.com/definition/Server-Message-Block-Protocol) is a client-server communication protocol used for sharing access to files, printers, serial ports and other resources on a network.   

### Enumerating SMB
- Port Scanning
    nmap 
- Enum4Linux
    `enum4linux [options] IP_ADDR`  
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

