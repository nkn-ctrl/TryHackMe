# Nmap
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/221752594-9bc1f554-c724-4acd-985a-3ee666c30563.png" width="300">
</p>  
<p align="center">--- Nmap Live Host Dsiscovery ---</p>  

|Scan Type|Example Command|
|:----|:----|
|ARP Scan|`sudo nmap -PR -sn MACHINE_IP/24`|
|ICMP Echo Scan|`sudo nmap -PE -sn MACHINE_IP/24`|
|ICMP Timestamp Scan|`sudo nmap -PP -sn MACHINE_IP/24`|
|ICMP Address Mask Scan|`sudo nmap -PM -sn MACHINE_IP/24`|
|TCP SYN Ping Scan|`sudo nmap -PS22,80,443 -sn MACHINE_IP/30`|
|TCP ACK Ping Scan|`sudo nmap -PA22,80,443 -sn MACHINE_IP/30`|
|UDP Ping Scan|`sudo nmap -PU53,161,162 -sn MACHINE_IP/30`|  

|Option|Purpose|
|:----|:----|
|`-n`|no DNS lookup|
|`-R`|reverse-DNS lookup for all hosts|
|`-sn`|host discovery only|

<p align="center">--- Nmap Basic Port Scans ---</p>  

|Port Scan Type|Example Command|
|:----|:----|
|TCP Connect Scan|nmap -sT 10.10.94.114|
|TCP SYN Scan|sudo nmap -sS 10.10.94.114|
|UDP Scan|sudo nmap -sU 10.10.94.114|

|Option|Purpose|
|:----|:----|
|-p-|all ports|
|-p1-1023|scan ports 1 to 1023|
|-F|100 most common ports|
|-r|scan ports in consecutive order|
|-T<0-5>|-T0 being the slowest and T5 the fastest|
|--max-rate 50|rate <= 50 packets/sec|
|--min-rate 15|rate >= 15 packets/sec|
|--min-parallelism 100|at least 100 probes in parallel|





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
    - Privileged users don't need to complete the TCP 3-way handshake even if the port is open.  
    - Unprivileged users have no choice but to complete the 3-way handshake if the port is open.  
<img src="https://user-images.githubusercontent.com/73976100/222007719-95528d21-7903-4c71-94da-698935103ce9.png" width="600">  

- TCP ACK Ping  
`sudo nmap -PA -sn TARGET`  
By default, port 80 is used.  
`-PA21`: target port 21 `-PA21-25`: target ports 21,22,23,24,25 `-PA80,443,8080`: target ports 80,443,8080. If no port is specified, port 80 will be used.  
<img src="https://user-images.githubusercontent.com/73976100/222008801-2a47438d-b5bd-40db-b7a6-a6e6d7c608da.png" width="600">  

- UDP Ping  
`sudo nmap -PU -sn TARGET`  
`-PU`: To use UDP Ping  

    - UDP port is open.  
    <img src="https://user-images.githubusercontent.com/73976100/222009187-2cf464fb-7266-4f69-8d38-d08b84abfdbf.png" width="600">  

    - UDP port is closed.  
    <img src="https://user-images.githubusercontent.com/73976100/222009330-8b4d2a78-82d1-4b28-94a0-cd5bf507268e.png" width="600">  

**Masscan**  
Masscan uses a similar approach to discover the available systems.   
`masscan MACHINE_IP/24 -p443`  
`masscan MACHINE_IP/24 -p80,443`  
`masscan MACHINE_IP/24 -p22-25`  
`masscan MACHINE_IP/24 ‐‐top-ports 100`  

### Using Reverse-DNS Lookup  
Nmap’s default behaviour is to use reverse-DNS online hosts.  
`-n`: no DNS lookup  
`-R`: reverse-DNS lookup for all hosts  
`--dns-servers DNS_SERVER`: To use a specific DNS server   

## Nmap Basic Port Scans
### TCP and UDP Ports  
1. Open: a service is listening on the specified port.
2. Closed: no service is listening on the specified port, although the port is accessible.
3. Filtered: the port is not accessible. 
4. Unfiltered: Nmap cannot determine if the port is open or closed, although the port is accessible. This state is encountered when using an ACK scan -sA.
5. Open|Filtered: Nmap cannot determine whether the port is open or filtered.
6. Closed|Filtered: Nmap cannot decide whether a port is closed or filtered.  

### TCP Flags
<img src="https://user-images.githubusercontent.com/73976100/222037103-e74cd7e5-a435-4c96-ab4d-d1989ea93269.png" width="600">  

1. URG: incoming data is urgent. Send immediately without consideration of having to wait on previously sent TCP segments.
2. ACK: To used to acknowledge the receipt of a TCP segment.
3. PSH: asking TCP to pass the data to the application promptly..
4. RST: reset the connection. Another device. also, there is no service on the receiving end to answer.
5. SYN: initiate a TCP 3-way handshake and synchronize sequence
6. FIN: The sender has no more data to send.  

### TCP Connect Scan
`nmap -sT TARGET`  
`-sT`: The connection is torn as soon as its state is confirmed by sending a RST/ACK.  
`-F`: Fast mode (scan ports from 1000 to 100)  
`-r`: scan the port in consecutive order  
<img src="https://user-images.githubusercontent.com/73976100/222042542-c83034bc-fe35-4daf-997b-fad46c96b29d.png" width="600">  

### TCP SYN Scan  
TCP SYN scan is the default scan mode when running Nmap as a privileged user.    
`sudo nmap -sS TARGET`  
`-sS`: does not need to complete the TCP 3-way handshake  
<img src="https://user-images.githubusercontent.com/73976100/222044115-168089d8-f867-4b9e-a559-22abba285008.png" width="600">  

### UDP Scan  
`sudo nmap -sU TARGET`  
`-sU`: To use UDP scan.  

### Fine-Tuning Scope and Performance
**Port**  
- port list: `-p22,80,443` scan ports22,80,443
- port range: `-p1-1023` scan ports between 1 and 1023
- all ports: `-p-` scan all 65535 ports  
- fast mode: `-F` scan the most common 100 ports
- more fast: `--top-ports 10` check the 10 most common ports  

**Timing**
`-T<0-5>`: `-T0` is the slowest (paranoid), while `-T5` is the fastest.  
To avoid IDS alerts, you might consider `-T0` or `-T1`. If you don’t specify any timing, Nmap uses normal `-T3`.  



