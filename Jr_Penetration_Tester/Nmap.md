# Nmap
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/221752594-9bc1f554-c724-4acd-985a-3ee666c30563.png" width="300">
</p>

## Nmap Live Host Discovery
1. ARP scan: This scan uses ARP requests to discover live hosts
2. ICMP scan: This scan uses [ICMP](https://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml) requests to identify live hosts.  ICMP ping uses Type 8 (Echo) and Type 0 (Echo Reply).
3. TCP/UDP ping scan: This scan sends packets to TCP ports and UDP ports to determine live hosts. This method is efficient, especially when ICMP Echo is blocked.  
<img src="https://user-images.githubusercontent.com/73976100/221764823-20f9f256-88ab-44f4-9a59-4f15f749804e.png" width="600">  
<br>

1. When a `privileged user` tries to scan targets on a `local network` (Ethernet), Nmap uses `ARP` requests. 
2. When a `privileged user` tries to scan targets `outside the local network`, Nmap uses `ICMP echo` requests, `TCP ACK` (Acknowledge) to port `80`, `TCP SYN` (Synchronize) to port `443`, and `ICMP timestamp` request.
3. When an `unprivileged user` tries to scan targets `outside the local network`, Nmap resorts to a `TCP 3-way handshake` by sending SYN packets to ports `80` and `443`.

### Enumerating Targets
- list: `MACHINE_IP scanme.nmap.org example.com` will scan 3 IP addresses.
- range: `10.11.12.15-20` will scan 6 IP addresses: `10.11.12.15`, `10.11.12.16`,… and `10.11.12.20`.
- subnet: `MACHINE_IP/30` will scan 4 IP addresses.
- list file: `nmap -iL list_of_hosts.txt`  
<br>

`sudo nmap -sL TARGETS`: This option will give you a detailed list of the hosts that Nmap will scan without scanning them; however, Nmap will attempt a reverse-DNS resolution on all the targets to obtain their names.  (If you don’t want Nmap to the DNS server, you can add `-n`.)  

### Nmap Host Discovery Using ARP
ARP scan is possible only if you are on the same subnet as the target systems.  
`sudo nmap -PR -sn TARGETS`: ARP scan without port-scanning  
`-PR`: Only ARP scan  
`-sn`: No port scan  
<img src="https://user-images.githubusercontent.com/73976100/221833308-c25da2f7-9a48-42b8-ad7f-8031f3880e59.png" width="600">  

`sudo arp-scan -I eth0 -l`: Send ARP queries for all valid IP addresses on the `eth0` interface.  
`-I eth0`:  Limit the adapter to be scanned to eth0
`-l`: --localhost  

### Nmap Host Discovery Using ICMP
We can ping every IP address on a target network and see who would respond to our ping (ICMP Type 8/Echo) requests with a ping reply (ICMP Type 0).  
Note:
- Many firewalls and new versions of MS Windows block ICMP echo.
- ARP query will precede the ICMP request if your target is on the same subnet.  

`sudo nmap -PE -sn TARGETS`  
`-PE`: To use ICMP echo request  
`-sn`: No port scan  

<img src="https://user-images.githubusercontent.com/73976100/221835212-f5d2fa5d-0ab3-4799-b014-0029fbe03ae5.png" width="600">  

Because ICMP echo requests tend to be blocked, you might also consider ICMP Timestamp or ICMP Address Mask requests to tell if a system is online.  
- `sudo nmap -PP -sn TARGET`  
    `-PP`: To use ICMP timestamp (ICMP type 13) and timestamp reply (ICMP type 14)  
- `sudo nmap -PM -sn TARGET`  
    `-PM`: To use ICMP address mask  

### Nmap Host Discovery Using TCP and UDP
<img src="https://user-images.githubusercontent.com/73976100/222006479-36459296-6f99-4166-827f-a36787a973be.png" width="600">  

- TCP SYN Ping
`sudo nmap -PS -sn TARGET`  
`-PS`: To use TCP SYN ping  
`-PS21`: target port 21 `-PS21-25`: target ports 21,22,23,24,25 `-PS80,443,8080`: target ports 80,443,8080  
<img src="https://user-images.githubusercontent.com/73976100/222007719-95528d21-7903-4c71-94da-698935103ce9.png" width="600">  

- TCP ACK Ping  
`sudo nmap -PA -sn TARGET`  
By default, port 80 is used.  
`-PA21`: target port 21 `-PA21-25`: target ports 21,22,23,24,25 `-PA80,443,8080`: target ports 80,443,8080. If no port is specified, port 80 will be used.  
<img src="https://user-images.githubusercontent.com/73976100/222008801-2a47438d-b5bd-40db-b7a6-a6e6d7c608da.png" width="600">  

- UDP Ping  
`sudo nmap -PU -sn TARGET`  
`-PU`: To use UDP Ping  
**UDP port is open.**  
<img src="https://user-images.githubusercontent.com/73976100/222009187-2cf464fb-7266-4f69-8d38-d08b84abfdbf.png" width="600">  

**UDP port is closed.**  
<img src="https://user-images.githubusercontent.com/73976100/222009330-8b4d2a78-82d1-4b28-94a0-cd5bf507268e.png" width="600">  







