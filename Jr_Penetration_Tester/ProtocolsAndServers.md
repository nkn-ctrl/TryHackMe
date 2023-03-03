# Protocols and Servers

### Snifing Attack
Sniffing attack refers to using a network packet capture tool to collect information about the target.   
`sudo tcpdump port 110 -A`  
`-A`: To captured packets in ASCII format  

### Man-in-the-Middle (MITM) Attack
A Man-in-the-Middle (MITM) attack occurs when a victim (A) believes they are communicating with a legitimate destination (B) but is unknowingly communicating with an attacker (E).   

<img src="https://user-images.githubusercontent.com/73976100/222410672-b02a909c-5df5-489f-aeb8-9478074b41e0.png" width="600">  

Any time you browse over HTTP, you are susceptible to a MITM attack, and the scary thing is that you cannot recognize it. Many tools would aid you in carrying out such an attack, such as [Ettercap](https://www.ettercap-project.org/) and [Bettercap](https://www.bettercap.org/).  

### Secure Shell (SSH)
`ssh username@SSH_SERVER_IP`  
`scp`:  SSH to transfer files using SCP (Secure Copy Protocol) based on the SSH protocol  
`scp mark@10.10.105.149:/home/mark/archive.tar.gz ~`  
`scp backup.tar.bz2 mark@10.10.105.149:/home/mark/`  

### Password Attack  
`/usr/share/wordlists/rockyou.txt`: wordlist  
Hydra supports many protocols, including FTP, POP3, IMAP, SMTP, SSH, and all methods related to HTTP.  
`hydra -l username -P wordlist.txt server service`  
- `-l username`: `-l` should precede the `username`, i.e. the login name of the target.
- `-P wordlist.txt`: `-P` precedes the `wordlist.txt` file, which is a text file containing the list of passwords you want to try with the provided username.
- `server` is the hostname or IP address of the target server.
- `service` indicates the service which you are trying to launch the dictionary attack.  

examples:  
- `hydra -l mark -P /usr/share/wordlists/rockyou.txt 10.10.105.149 ftp` will use `mark` as the username as it iterates over the provided passwords against the FTP server.
- `hydra -l mark -P /usr/share/wordlists/rockyou.txt ftp://10.10.105.149` is identical to the previous example. `10.10.105.149 ftp` is the same as `ftp://10.10.105.149`.
- `hydra -l frank -P /usr/share/wordlists/rockyou.txt 10.10.105.149 ssh` will use `frank` as the user name as it tries to login via SSH using the different passwords.  

optional arguments:  




