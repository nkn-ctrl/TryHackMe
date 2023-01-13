# Wireshark
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshark-logo.png" width="300">

## Statistics
Menu Bar -> \[Statistics\]
### Statistics Summary
- Resolved Addresses  
  <img src="https://miro.medium.com/max/1400/0*gUDMUOL7UpGjkzj8.webp" width="500">

- Protocol Hierarchy  
  right-click and filter the event of interest.  
  <img src="https://miro.medium.com/max/1400/0*-nIkphZ2R73fXfM0.webp" width="600">

- Conversations  
  Conversation represents traffic between two specific endpoints.
  <img src="https://miro.medium.com/max/1400/0*5Qe3lMF4vWsqXI2Q.webp" width="600">

- Endpoints  
  <img src="https://miro.medium.com/max/1400/0*QBJAIXb-QEhtB8RH.webp" width="600">  
  IP and port name resolution options  
  <img src="https://miro.medium.com/max/1400/0*DPyYVChVECsjLwbQ.webp" width="600">

### Statistics Protocol Details
- IPv4 and IPv6  
  <img src="https://miro.medium.com/max/1400/0*D-7xdfvv7sxgMUXj.webp" width="600">

- DNS  
  <img src="https://miro.medium.com/max/1400/0*t9sFS5gsGoC4CT8k.webp" width="600">

- HTTP  
  <img src="https://miro.medium.com/max/1400/0*tfVda1YuPVuAM1gJ.webp" width="600">

## Packet Filtering
### Packet Filtering
| Filtering Types | Description | Syntax |
|-----------------|-------------|--------|
|Capture Filters|Save only. It is set before capturing traffic and not changeable during the capture.|```tcp port 80```|
|Display Filters|Reducing the number of visible packets, and it is changeable during the capture.|```tcp.port == 80```|

### Comparison Operators
|English|	C-Like|	Description|	Example|
|:-----:|:-----:|------------|---------|
|eq|==|Equal|```ip.src == 10.10.10.100```|
|ne|!=|Not equal|```ip.src != 10.10.10.100```|
|gt|>|Greater than|```ip.ttl > 250```|
|lt|<|Less Than|```ip.ttl < 10```|
|ge|>=|Greater than or equal to|```ip.ttl >= 0xFA```|
|le|<=|Less than or equal to|```ip.ttl <= 0xA```|
|and|&&|Logical AND|```(ip.src == 10.10.10.100) AND (ip.src == 10.10.10.111)```|
|or|\|\||Logical OR|```(ip.src == 10.10.10.100) OR (ip.src == 10.10.10.111)```|
|not|!|Logical NOT|```!(ip.src == 10.10.10.222)```|

### Protocol Filters
#### IP Filters(Network Layer)
These filters filter network-level information like IP addresses, version, time to live, type of service, flags, and checksum values.
|Filter|	Description|
|:----:|-------------|
|```ip```|Show all IP packets.|
|```ip.addr == 10.10.10.111```|Show all packets containing IP address 10.10.10.111.|
|```ip.addr == 10.10.10.0/24```|Show all packets containing IP addresses from 10.10.10.0/24 subnet.|
|```ip.src == 10.10.10.111```|Show all packets originated from 10.10.10.111|
|```ip.dst == 10.10.10.111```|Show all packets sent to 10.10.10.111|

#### TCP and UDP Filters(TransPort Layer)
These filters filter transport protocol level information like source and destination ports, sequence number, acknowledgement number, windows size, timestamps, flags, length and protocol errors.
|Filter|	Description|Filter|	Description|
|:----:|-------------|:----:|------------|
|```tcp.port == 80```|Show all TCP packets with port 80 	|```udp.port == 53```|Show all UDP packets with port 53|
|```tcp.srcport == 1234```|Show all TCP packets originating from port 1234	|```udp.srcport == 1234```|Show all UDP packets originating from port 1234|
|```tcp.dstport == 80```|Show all TCP packets sent to port 80	|```udp.dstport == 5353```|Show all UDP packets sent to port 5353|

#### HTTP and DNS(Application Layer)
These filters filter application-specific information, like payload and linked data, depending on the protocol type.
|Filter|	Description|Filter|	Description|
|:----:|-------------|:----:|------------|
|```http```|Show all HTTP packets	|```dns```|Show all DNS packets|
|```http.response.code == 200```|Show all packets with HTTP response code "200"	|```dns.flags.response == 0```|Show all DNS requests|
|```http.request.method == "GET"```|Show all HTTP GET requests|```dns.flags.response == 1```|Show all DNS responses|
|```http.request.method == "POST"```|Show all HTTP POST requests|```dns.qry.type == 1```|Show all DNS "A" records|

#### Display Filter Expressions
Analyse --> Display Filter Expression  
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_fileter.png" width="600">

### Advanced Filtering
#### Fileter: "contains"
```http.server contains "Apache"```  
  Search a value inside packets.   
  Find all "Apache" servers. List all HTTP packets where packets' "server" field contains the "Apache" keyword.  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_contain.png" width="700">

#### Filter: "matches"
```http.host matches "\.(php|html)"```  
  Search a pattern of a regular expression.   
  Find all .php and .html pages. List all HTTP packets where packets' "host" fields match keywords ".php" or ".html".  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_match.png" width="700">  

#### Filter: "in"
```tcp.port in {80 443 8080}```  
  Search a value or field inside of a specific scope/range.  
  Find all packets that use ports 80, 443 or 8080. List all TCP packets 
  where packets' "port" fields have values 80, 443 or 8080.  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_in.png" width="700">

#### Filter: "upper"
```upper(http.server) contains "APACHE"```  
  Convert a string value to uppercase.  
  Find all "APACHE" servers. Convert all HTTP packets' "server" fields to uppercase and list packets that contain the "APACHE" keyword.  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_upper.png" width="700">

#### Filter: "lower"
```lower(http.server) contains "apache"```  
  Convert a string value to lowercase.  
  Find all "apache" servers. Convert all HTTP packets' "server" fields info to lowercase and list packets that contain the "apache" keyword.  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_lower.png" width="700">

#### Filter: "string"
```string(frame.number) matches "[13579]$"```  
  Convert a non-string value to a string.  
  Find all frames with odd numbers.Convert all "frame number" fields to string values, and list frames end with odd values.  
  <img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_string.png" width="700">

## Profiles
Edit --> Configuration Profiles

## Traffic Analysis

### Nmap Scans
  - TCP connect scans
  - SYN scans
  - UDP scans

It is essential to know how Nmap scans work to spot scan activity on the network.  
TCP flags  
|Notes|	Wireshark Filters|
|-----|------------------|
|Global search.| tcp<br> udp|
|Only SYN flag.<br>SYN flag is set. The rest of the bits are not important.|tcp.flags == 2<br>tcp.flags.syn == 1|
|Only ACK flag.<br>ACK flag is set. The rest of the bits are not important.|tcp.flags == 16<br>tcp.flags.ack == 1|
|Only SYN, ACK flags.<br>SYN and ACK are set. The rest of the bits are not important.|tcp.flags == 18<br>(tcp.flags.syn == 1) and (tcp.flags.ack == 1)|
|Only RST flag.<br>RST flag is set. The rest of the bits are not important.|tcp.flags == 4<br>tcp.flags.reset == 1|
|Only RST, ACK flags.<br>RST and ACK are set. The rest of the bits are not important.|tcp.flags == 20<br>(tcp.flags.reset == 1) and (tcp.flags.ack == 1)|
|Only FIN flag<br>FIN flag is set. The rest of the bits are not important.|tcp.flags == 1<br>tcp.flags.fin == 1|

#### TCP Connect Scans
```tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024```  
- Usually conducted with ```nmap -sT``` command.(non-privileged users)  
- Usually has a windows size larger than 1024 bytes.  
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_scan_tcpconnect.png" width="700">

#### SYN Scans
```tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024```  
- Usually conducted with ```nmap -sS``` command.(privileged users)
- Usually have a size less than or equal to 1024 bytes.
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_scan_syn.png" width="700">

#### UDP Scans
```icmp.type==3 and icmp.code==3```  
- No prompt for open ports, ICMP error message for close ports
- Usually conducted with ```nmap -sU``` command.  
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_scan_udp.png" width="700">

### ARP Poisoning/Spoofing (A.K.A. Man In The Middle Attack)

|Notes|	Wireshark filter|
|-----|-----------------|
|Global search	|```arp```|
|Opcode 1: ARP requests.<br>Opcode 2: ARP responses.<br>Hunt: Arp scanning<br>Hunt: Possible ARP poisoning detection<br>Hunt: Possible ARP flooding from detection:|```arp.opcode == 1```<br>```arp.opcode == 2```<br>```arp.dst.hw_mac==00:00:00:00:00:00```<br>```arp.duplicate-address-detected or arp.duplicate-address-frame```<br>```((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)```|  

A possible IP spoofing case  
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wiresirk_arpspoofing.png" width="800">

###  Identifying Hosts: DHCP, NetBIOS and Kerberos
Protocols that can be used in Host and User identification:
- Dynamic Host Configuration Protocol (DHCP) traffic
- NetBIOS (NBNS) traffic 
- Kerberos traffic  

#### DHCP Analysis
Dynamic Host Configuration Protocol (DHCP)  
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search.|```dhcp``` or ```bootp```|
|"DHCP Request" packets contain the hostname information<br>"DHCP ACK" packets represent the accepted requests<br>"DHCP NAK" packets represent denied requests|Request: ```dhcp.option.dhcp == 3```<br>ACK: ```dhcp.option.dhcp == 5```<br>NAK: ```dhcp.option.dhcp == 6```|
|"DHCP Request" options<br>Option 12: Hostname.<br>Option 50: Requested IP address.<br>Option 51: Requested IP lease time.<br>Option 61: Client's MAC address.|```dhcp.option.hostname contains "keyword"```|
|"DHCP ACK" options<br> Option 15: Domain name.<br> Option 51: Assigned IP lease time.|```dhcp.option.domain_name contains "keyword"```|
|"DHCP NAK" options<br> Option 56: Message (rejection details/reason).|As the message could be unique according to the case/situation.|


