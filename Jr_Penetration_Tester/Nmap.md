# Nmap
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/221752594-9bc1f554-c724-4acd-985a-3ee666c30563.png" width="300">
</p>  

<p align="center">--- [Nmap Live Host Dsiscovery](#nmap-host-discovery-using-arp) ---</p>  

|Scan Type|Example Command|
|:---:|----|
|ARP Scan|`sudo nmap -PR -sn MACHINE_IP/24`|
|ICMP Echo Scan|`sudo nmap -PE -sn MACHINE_IP/24`|
|ICMP Timestamp Scan|`sudo nmap -PP -sn MACHINE_IP/24`|
|ICMP Address Mask Scan|`sudo nmap -PM -sn MACHINE_IP/24`|
|TCP SYN Ping Scan|`sudo nmap -PS22,80,443 -sn MACHINE_IP/30`|
|TCP ACK Ping Scan|`sudo nmap -PA22,80,443 -sn MACHINE_IP/30`|
|UDP Ping Scan|`sudo nmap -PU53,161,162 -sn MACHINE_IP/30`|  

|Option|Purpose|
|:---:|----|
|`-n`|no DNS lookup|
|`-R`|reverse-DNS lookup for all hosts|
|`-sn`|host discovery only|

<p align="center">--- Nmap Basic Port Scans ---</p>  

|Port Scan Type|Example Command|
|:---:|----|
|TCP Connect Scan|`nmap -sT 10.10.94.114`|
|TCP SYN Scan|`sudo nmap -sS 10.10.94.114`|
|UDP Scan|`sudo nmap -sU 10.10.94.114`|  

|Option|Purpose|
|:---:|----|
|`-p-`|all ports|
|`-p1-1023`|scan ports 1 to 1023|
|`-F`|100 most common ports|
|`-r`|scan ports in consecutive order|
|`-T<0-5>`|-T0 being the slowest and T5 the fastest|
|`--max-rate 50`|rate <= 50 packets/sec|
|`--min-rate 15`|rate >= 15 packets/sec|
|`--min-parallelism 100`|at least 100 probes in parallel|  

<p align="center">--- Nmap Advanced Port Scans ---</p>  

|Port Scan Type|Example Command|
|:---:|----|
|TCP Null Scan|`sudo nmap -sN 10.10.166.135`|
|TCP FIN Scan|`sudo nmap -sF 10.10.166.135`|
|TCP Xmas Scan|`sudo nmap -sX 10.10.166.135`|
|TCP Maimon Scan|`sudo nmap -sM 10.10.166.135`|
|TCP ACK Scan|`sudo nmap -sA 10.10.166.135`|
|TCP Window Scan|`sudo nmap -sW 10.10.166.135`|
|Custom TCP Scan|`sudo nmap --scanflags URGACKPSHRSTSYNFIN 10.10.166.135`|
|Spoofed Source IP|`sudo nmap -S SPOOFED_IP 10.10.166.135`|
|Spoofed MAC Address|`--spoof-mac SPOOFED_MAC`|
|Decoy Scan|`nmap -D DECOY_IP,ME 10.10.166.135`|
|Idle (Zombie) Scan|`sudo nmap -sI ZOMBIE_IP 10.10.166.135`|
|Fragment IP data into 8 bytes|`-f`|
|Fragment IP data into 16 bytes|`-ff`|  

|Option|Purpose|
|:---:|----|
|`--source-port PORT_NUM`|specify source port number|
|`--data-length NUM`|append random data to reach given length|  

|Option|Purpose|
|:---:|----|
|`--reason`|explains how Nmap made its conclusion|
|`-v`|verbose|
|`-vv`|very verbose|
|`-d`|debugging|
|`-dd`|more details for debugging|  

<p align="center">--- Nmap Post Port Scans ---</p>   

|Option|Meaning|
|:---:|----|
|`-sV`|determine service/version info on open ports|
|`-sV --version-light`|try the most likely probes (2)|
|`-sV --version-all`|try all available probes (9)|
|`-O`|detect OS|
|`--traceroute`|run traceroute to target|
|`--script=SCRIPTS`|Nmap scripts to run|
|`-sC` or `--script=default`|run default scripts|
|`-A`|equivalent to -sV -O -sC --traceroute|
|`-oN`|save output in normal format|
|`-oG`|save output in grepable format|
|`-oX`|save output in XML format|
|`-oA`|save output in normal, XML and Grepable formats|



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

## Nmap Advanced Port Scans  
### TCP Null Scan, FIN Scan, and Xmas Scan
- Null Scan  
    `-sN`: The null scan does not set any flag; all six flag bits are set to zero.  
    If target respond with RST,ACK packet, TCP port is closed.  If target didn't response, TCP port is open or filterd.
    <img src="https://user-images.githubusercontent.com/73976100/222052739-ae3f6e34-8a58-442f-aa67-3567fb662340.png" width="600">  
    <img src="https://user-images.githubusercontent.com/73976100/222052863-9009ca06-8b0c-4a53-a217-e51cd5a3a33b.png" width="600">    

- FIN Scan  
    `-sF`: The FIN scan sends a TCP packet with the FIN flag set.  
    <img src="https://user-images.githubusercontent.com/73976100/222053220-b1b65b5d-05f3-40fe-8c56-87aab1d33c9d.png" width="600">  
    <img src="https://user-images.githubusercontent.com/73976100/222053416-5378374b-7d9a-4475-8cac-ecb584debf49.png" width="600">  

- Xmas Scan  
    `-sX`: The Xmas scan gets its name after Christmas tree lights. An Xmas scan sets the FIN, PSH, and URG flags simultaneously.  
    <img src="https://user-images.githubusercontent.com/73976100/222054522-8e4c8864-1064-402d-8ea3-8bdcb45f617c.png" width="600">  
    <img src="https://user-images.githubusercontent.com/73976100/222054524-b6eb9b46-e9cf-4e6c-a8e0-85e05ba08d9d.png" width="600">  

### TCP ACK, Window, and Custom Scan  
This kind of scan would be helpful if there is a firewall in front of the target.  
- TCP Ack Scan  
    `-sA`: ACK scan will send a TCP packet with the ACK flag set. The target would respond to the ACK with RST regardless of the state of the port. Hence, this scan won’t tell us whether the target port is open in a simple setup.  
    In the following example, we scanned the target VM before installing a firewall on it. As expected, we couldn’t learn which ports were open.  

    ```
    pentester@TryHackMe$ sudo nmap -sA 10.10.255.57

    Starting Nmap 7.60 ( https://nmap.org ) at 2021-08-30 10:37 BST
    Nmap scan report for 10.10.255.57
    Host is up (0.0013s latency).
    All 1000 scanned ports on MACHINE_IP are unfiltered
    MAC Address: 02:45:BF:8A:2D:6B (Unknown)

    Nmap done: 1 IP address (1 host up) scanned in 1.68 seconds
    ```  
    After setting up the target VM 10.10.255.57 with a firewall, we repeated the ACK scan. This time, we received some interesting results. As seen in the console output below, we have three ports that aren't being blocked by the firewall. This result indicates that the firewall is blocking all other ports except for these three ports.  

    ```
    pentester@TryHackMe$ sudo nmap -sA 10.10.255.57

    Starting Nmap 7.60 ( https://nmap.org ) at 2021-09-07 11:34 BST
    Nmap scan report for 10.10.255.57
    Host is up (0.00046s latency).
    Not shown: 997 filtered ports
    PORT    STATE      SERVICE
    22/tcp  unfiltered ssh
    25/tcp  unfiltered smtp
    80/tcp  unfiltered http
    MAC Address: 02:78:C0:D0:4E:E9 (Unknown)

    Nmap done: 1 IP address (1 host up) scanned in 15.45 
    ```  

- Window Scan  
    `-sW`: The TCP window scan is almost the same as the ACK scan; however, it examines the TCP Window field of the RST packets returned. On specific systems, this can reveal that the port is open.  

- Custom Scan  
    `--scanflags`: To experiment with a new TCP flag combination beyond the built-in TCP scan types. To set SYN, RST, and FIN simultaneously, you can do so using `--scanflags RSTSYNFIN`.  
    <img src="https://user-images.githubusercontent.com/73976100/222062015-af103fe3-562a-4156-adb9-5403a21ce888.png" width="600">  

### Spoofing and Decoys
Spoofing the source IP address can be a great approach to scanning stealthily. However, spoofing will only work in specific network setups. It requires you to be in a position where you can monitor the traffic.   
- Spoofing  
In some network setups, you will be able to scan a target system using a spoofed IP address and even a spoofed MAC address. Such a scan is only beneficial in a situation where you can guarantee to capture the response.    
`sudo namp -e NET_INTERFACE -Pn -S SPOOFED_IP TARGET_IP`  
`-e`: To use specify network interface  
`-Pn`: disable ping scan    
`--spoof-mac SPOOFED_MAC`: spoof mac address  

<img src="https://user-images.githubusercontent.com/73976100/222259226-38a941e2-0452-4b1d-8f26-a8a823c9f9c6.png" width="600">

1. Attacker sends a packet with a spoofed source IP address to the target machine.
2. Target machine replies to the spoofed IP address as the destination.
3. Attacker captures the replies to figure out open ports.  
<br>

- Decoy  
Spoofing only works in a minimal number of cases where certain conditions are met. Therefore, the attacker might resort to using decoys to make it more challenging to be pinpointed.  
`sudo nmap -D DECOY1,DECOY2,RND,RND,ME TARGET_IP`  
`-D`: To use decoy  
`DECOY1`: Decoy_ip  
`RND`: Random_IP

<img src="https://user-images.githubusercontent.com/73976100/222261653-5619a502-df76-4683-a966-28820827b60e.png" width="600">  

### Fragmented Packets
Nmap provides the option `-f` to fragment packets. Once chosen, the IP data will be divided into 8 bytes or less. Adding another `-f` (`-f -f` or `-ff`) will split the data into 16 byte-fragments instead of 8. You can change the default value by using the --mtu; however, you should always choose a multiple of 8.  

### Idle/Zombike Scan
The idle scan, or zombie scan, requires an idle system connected to the network that you can communicate with.  
1. Trigger the idle host to respond so that you can record the current IP ID on the idle host.
2. Send a SYN packet to a TCP port on the target. The packet should be spoofed to appear as if it was coming from the idle host (zombie) IP address.
3. Trigger the idle machine again to respond so that you can compare the new IP ID with the one received earlier.

`sudo nmap -sI ZOMBIE_IP TARGET_IP`  

<img src="https://user-images.githubusercontent.com/73976100/222288522-62e0e400-b26c-464b-a3ff-58678374a54c.png" width="600">  

<img src="https://user-images.githubusercontent.com/73976100/222288530-c1c8794b-d78d-41d3-b3e2-ddbd54af2106.png" width="600">  

<img src="https://user-images.githubusercontent.com/73976100/222288538-c961f21d-8385-43ee-b4f2-4741609017d0.png" width="600">  

### Getting More Details  
`--reason` `-v` `-vv` `-d` `-dd`

## Nmap Post Port Scans  
### Service Detection
`sudo nmap -sV TARGET_IP`  
`-sV`: To collect and determine service and version information for the open ports  
`--version-intensity LEVEL`: 0(lightest)-9(most complete)  
`-sV --version-light`: intesity of 2  
`-sV --version-all`: intensity of 9  
Note: stealth SYN scan `-sS` is not possible when `-sV` option is chosen.   

### OS Detection and Traceroute
- OS Dtection
    `sudo nmap -sS -O TARGET_IP`  
    `-O`: to detect the Operating System (OS)  

- Traceroute
    `sudo nmap -sS --traceroute TARGET_IP`  
    `--traceroute`: Nmap appended a traceroute to its scan results.  

### Nmap Scripting Engine (NSE)
`/usr/share/nmap/scripts`  
`sudo nmap -sS -sC TARGET_IP`  
`--scripts=default` or `-sC`: using default scripts  


|Script Category|Description|
|:---:|:----|
|`auth`|Authentication related scripts|
|`broadcast`|Discover hosts by sending broadcast messages|
|`brute`|Performs brute-force password auditing against logins|
|`default`|Default scripts, same as `-sC`|
|`discovery`|Retrieve accessible information, such as database tables and DNS names|
|`dos`|Detects servers vulnerable to Denial of Service (DoS)|
|`exploit`|Attempts to exploit various vulnerable services|
|`external`|Checks using a third-party service, such as Geoplugin and Virustotal|
|`fuzzer`|Launch fuzzing attacks|
|`intrusive`|Intrusive scripts such as brute-force attacks and exploitation|
|`malware`|Scans for backdoors|
|`safe`|Safe scripts that won’t crash the target|
|`version`|Retrieve service versions|
|`vuln`|Checks for vulnerabilities or exploit vulnerable services|

### Saving the Output  
Conveniently enough, you can save the scan output in all three formats using `-oA FILENAME` to combine `-oN`, `-oG`, and `-oX` for normal, grepable, and XML.  
- Normal  
    `-oN FILENAME`: The normal format is similar to the output you get on the screen when scanning a target.  

- Grepable  
    `-oG FILENAME`: It makes filtering the scan output for specific keywords or terms efficient.  

- XML  
    `-oX FILENAME`: The XML format would be most convenient to process the output in other programs. 
