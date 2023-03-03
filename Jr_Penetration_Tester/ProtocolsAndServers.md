# Protocols and Servers

|Protocol|TCP Port|Application(s)|Data Security|
|:----:|:----:|:----:|:----:|
|FTP|21|File Transfer|Cleartext|
|FTPS|990|File Transfer|Encrypted|
|HTTP|80|Worldwide Web|Cleartext|
|HTTPS|443|Worldwide Web|Encrypted|
|IMAP|143|Email (MDA)|Cleartext|
|IMAPS|993|Email (MDA)|Encrypted|
|POP3|110|Email (MDA)|Cleartext|
|POP3S|995|Email (MDA)|Encrypted|
|SFTP|22|File Transfer|Encrypted|
|SSH|22|Remote Access and File Transfer|Encrypted|
|SMTP|25|Email (MTA)|Cleartext|
|SMTPS|465|Email (MTA)|Encrypted|
|Telnet|23|Remote Access|Cleartext|  

### Telnet
`telnet MACHINE_IP`  

### Hypertext Transfer Protocol (HTTP)
`telnet MACHINE_IP 80`  
1. First, we connect to port 80 using `telnet MACHINE_IP 80`.
2. Next, we need to type `GET /index.html HTTP/1.1` to retrieve the page `index.html` or `GET / HTTP/1.1` to retrieve the default page.
3. Finally, you need to provide some value for the host like `host: telnet` and hit the Enter/Return key twice.  
```
pentester@TryHackMe$ telnet MACHINE_IP 80
Trying MACHINE_IP...
Connected to MACHINE_IP.
Escape character is '^]'.
GET /index.html HTTP/1.1
host: telnet

HTTP/1.1 200 OK
Server: nginx/1.18.0 (Ubuntu)
Date: Wed, 15 Sep 2021 08:56:20 GMT
Content-Type: text/html
....
```

### File Transfer Protocol (FTP)
`telnet MACHINE_IP 21`  
1. We connected to an FTP server using a Telnet client. Since FTP servers listen on port 21 by default, we had to specify to our Telnet client to attempt connection to port 21 instead of the default Telnet port.
2. We needed to provide the username with the command `USER frank`.
3. Then, we provided the password with the command `PASS D2xc9CgD`.
4. Because we supplied the correct username and password, we got logged in.  
`STAT`: some added information  
`SYST`: System Type of the target  
`PASV`: switches the mode to passive.
- Active: In the active mode, the data is sent over a separate channel originating from the FTP server’s port 20.
- Passive: In the passive mode, the data is sent over a separate channel originating from an FTP client’s port above port number 1023.  
`TYPE A`: switches the file transfer mode to ASCII  
`TYPE I`: switches the file transfer mode to binary  



### Simple Mail Transfer Protocol (SMTP)
`telnet MACHINE_IP 25`  

### Post Office Protocol 3 (POP3)
`telnet MACHINE_IP 110`  

### Internet Message Access Protocol (IMAP)
`telnet MACHINE_IP 143`  

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
- `-s PORT` to specify a non-default port for the service in question.
- `-V` or `-vV`, for verbose, makes Hydra show the username and password combinations that are being tried. This verbosity is very convenient to see the progress, especially if you are still not confident of your command-line syntax.
- `-t n` where n is the number of parallel connections to the target. `-t 16` will create 16 threads used to connect to the target.
- `-d`, for debugging, to get more detailed information about what’s going on. The debugging output can save you much frustration; for instance, if Hydra tries to connect to a closed port and timing out, `-d` will reveal this right away.



