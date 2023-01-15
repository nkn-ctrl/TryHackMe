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
|- **"DHCP Request"** packets contain the hostname information<br>- **"DHCP ACK"** packets represent the accepted requests<br>- **"DHCP NAK"** packets represent denied requests|Request: ```dhcp.option.dhcp == 3```<br>ACK: ```dhcp.option.dhcp == 5```<br>NAK: ```dhcp.option.dhcp == 6```|
|"DHCP Request" options for grabbing the low-hanging fruits:<br>**Option 12**: Hostname.<br>- **Option 50**: Requested IP address.<br>- **Option 51**: Requested IP lease time.<br>- **Option 61**: Client's MAC address.|```dhcp.option.hostname contains "keyword"```<br>```dhcp.option.requested_ip_address == "ip_address"```|
|"DHCP ACK" options for grabbing the low-hanging fruits:<br>- **Option 15**: Domain name.<br>-  **Option 51**: Assigned IP lease time.|```dhcp.option.domain_name contains "keyword"```|
|"DHCP NAK" options for grabbing the low-hanging fruits:<br>- **Option 56**: Message (rejection details/reason).|As the message could be unique according to the case/situation.|

#### NetBIOS (NBNS) Analysis
Network Basic Input/Output System  
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search.|```nbns```|
|"NBNS" options for grabbing the low-hanging fruits:<br>- **Queries**: Query details.<br>Query details could contain **"name, Time to live (TTL) and IP address details"**|```nbns.name contains "keyword"```|

#### Kerberos Analysis
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search.	|```kerberos```|
|User account search:<br>**CNameString**: The username.<br>The values end with ```$``` are hostnames, and the ones without it are user names.|```kerberos.CNameString contains "keyword"```<br>```kerberos.CNameString and !(kerberos.CNameString contains "$" )```|
|"Kerberos" options for grabbing the low-hanging fruits:<br>- **pvno**: Protocol version.<br>- **realm**: Domain name for the generated ticket.<br>- **sname**: Service and domain name for the generated ticket.<br>- **addresses**: Client IP address and NetBIOS name|```kerberos.pvno == 5```<br>```kerberos.realm contains ".org"```<br>```kerberos.SNameString == "krbtg"```|

### Tunnelling Traffic: ICMP and DNS 
Traffic tunnelling is (also known as "port forwarding") transferring the data/resources in a secure method to network segments and zones.

#### ICMP Analysis
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search|```icmp```|
|"ICMP" optionsfor grabbing the low-hanging fruits:<br>- Packet length.<br>- ICMP destination addresses.<br>- Encapsulated protocol signs in ICMP payload.|```data.len > 64 and icmp```|

#### DNS Analysis
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search|```dns```|
|"DNS" options for grabbing the low-hanging fruits:<br>- Query length.<br>- Anomalous and non-regular names in DNS addresses.<br>- Long DNS addresses with encoded subdomain addresses.<br>- Known patterns like dnscat and dns2tcp.<br>- Statistical analysis like the anomalous volume of DNS requests for a particular target.<br>!mdns: Disable local link device queries.|```dns contains "dnscat"```<br>```dns.qry.name.len > 15 and !mdns```|

### Cleartext Protocol Analysis: FTP and HTTP

#### FTP Analysis
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search|```ftp```|
|"FTP" options for grabbing the low-hanging fruits:<br>- x1x series: Information request responses.<br>- x2x series: Connection messages.<br>- x3x series: Authentication messages.<br>Note: "200" means command successful.|--|
|"x1x" series options for grabbing the low-hanging fruits:<br>- 211: System status.<br>- 212: Directory status.<br>- 213: File status|```ftp.response.code == 211```|
|"x2x" series options for grabbing the low-hanging fruits:<br>- 220: Service ready.<br>- 227: Entering passive mode.<br>- 228: Long passive mode.<br>- 229: Extended passive mode.|```ftp.response.code == 227```|
|"x3x" series options for grabbing the low-hanging fruits:<br>- 230: User login.<br>- 231: User logout.<br>- 331: Valid username.<br>- 430: Invalid username or password<br>- 530: No login, invalid password.|```ftp.response.code == 230```|
|"FTP" commands for grabbing the low-hanging fruits:<br>- USER: Username.<br>- PASS: Password.<br>- CWD: Current work directory.<br>- LIST: List.|```ftp.request.command == "USER"```<br>```ftp.request.command == "PASS"```<br>```ftp.request.arg == "password"```|
|Advanced usages examples for grabbing low-hanging fruits:<br>- Bruteforce signal: List failed login attempts.<br>- Bruteforce signal: List target username.<br>- Password spray signal: List targets for a static password.|```ftp.response.code == 530```<br>```(ftp.response.code == 530) and (ftp.response.arg contains "username")```<br>```(ftp.request.command == "PASS" ) and (ftp.request.arg == "password")```|

#### HTTP Analysis
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search|```http```<br>```http2```|
|"HTTP Request Methods" for grabbing the low-hanging fruits:<br>- GET<br>- POST<br>- Request: Listing all requests|```http.request.method == "GET"```<br>```http.request.method == "POST"```<br>```http.request```|
|"HTTP Response Status Codes" for grabbing the low-hanging fruits:<br>- 200 OK: Request successful<br>- 301 Moved Permanently: Resource is moved to a new URL/path (permanently).<br>- 302 Moved Temporarily: Resource is moved to a new URL/path (temporarily).<br>- 400 Bad Request: Server didn't understand the request.<br>- 401 Unauthorised: URL needs authorisation (login, etc.).<br>- 403 Forbidden: No access to the requested URL. <br>- 404 Not Found: Server can't find the requested URL.<br>- 405 Method Not Allowed: Used method is not suitable or blocked.<br>- 408 Request Timeout:  Request look longer than server wait time.<br>- 500 Internal Server Error: Request not completed, unexpected error.<br>- 503 Service Unavailable: Request not completed server or service is down.|```http.response.code == 200```<br>```http.response.code == 401```<br>```http.response.code == 403```<br>```http.response.code == 404```<br>```http.response.code == 405```<br>```http.response.code == 503```|
|"HTTP Parameters" for grabbing the low-hanging fruits:<br>- User agent: Browser and operating system identification to a web server application.<br>- Request URI: Points the requested resource from the server.<br>- Full URI: Complete URI information.|```http.user_agent contains "nmap"```<br>```http.request.uri contains "admin"```<br>```http.request.full_uri contains "admin"```|
|"HTTP Parameters" for grabbing the low-hanging fruits:<br>- Server: Server service name.<br>- Host: Hostname of the server<br>- Connection: Connection status.<br>- Line-based text data: Cleartext data provided by the server.<br>- HTML Form URL Encoded: Web form information.|```http.server contains "apache"```<br>```http.host contains "keyword"```<br>```http.host == "keyword"```<br>```http.connection == "Keep-Alive"```<br>```data-text-lines contains "keyword"```|

User Agent Analysis  
[User Agents - Parser and API - Easily decode any user agent](https://developers.whatismybrowser.com/useragents/explore/)<br>
|Notes|	Wireshark Filter|
|-----|-----------------|
|Global search|```http.user_agent```|
|Research outcomes for grabbing the low-hanging fruits:<br>- Different user agent information from the same host in a short time notice.<br>- Non-standard and custom user agent info.<br>- Subtle spelling differences. ("Mozilla" is not the same as  "Mozlilla" or "Mozlila")<br>- Audit tools info like Nmap, Nikto, Wfuzz and sqlmap in the user agent field.<br>- Payload data in the user agent field.|```(http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")```|

Log4j Analysis<br>
|Notes|	Wireshark Filter|
|-----|-----------------|
|Research outcomes for grabbing the low-hanging fruits:<br>- The attack starts with a "POST" request<br>- There are known cleartext patterns:```jndi:ldap``` and ```Exploit.class```.|```http.request.method == "POST"```<br>```(ip contains "jndi") or ( ip contains "Exploit")(frame contains "jndi") or ( frame contains "Exploit")```<br>```(http.user_agent contains "$") or (http.user_agent contains "==")```|
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_log4j.png" width="800">

### Encrypted Protocol Analysis: Decrypting HTTPS
#### Decrypting HTTPS Traffic
|Notes|	Wireshark Filter|
|-----|-----------------|
|"HTTPS Parameters" for grabbing the low-hanging fruits:<br>- Request: Listing all requests<br>- TLS: Global TLS search<br>- TLS Client Request<br>- TLS Server response<br>- Local Simple Service Discovery Protocol (SSDP)<br>Note: SSDP is a network protocol that provides advertisement and discovery of network services.|```http.request```<br>```tls```<br>```tls.handshake.type == 1```<br>```tls.handshake.type == 2```<br>```ssdp```|

TLS protocol has its handshake process. The first two steps contain "Client Hello" and "Server Hello" messages. These filters are helpful to spot which IP addresses are involved in the TLS handshake.<br>
Client Hello: ```(http.request or tls.handshake.type == 1) and !(ssdp)```<br>
Server Hello: ```(http.request or tls.handshake.type == 2) and !(ssdp)```<br>

Encription key log file. SSL/TLS key pairs are created per session at the connection time, so it is important to dump the keys during the traffic capture. "right-click" menu or "Edit --> Preferences --> Protocols --> TLS" menu can add/remove key log files.<br>
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_keylog.png" width="800"><br>
Viewing the traffic with/without the key log files:<br>
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_keylog2.png" width="800">

### Hunt Cleartext Credentials!
Some Wireshark dissectors (FTP, HTTP, IMAP, pop and SMTP) are programmed to extract cleartext passwords from the capture file.<br>
"Tools --> Credentials" menu<br>
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/wireshirk_credentials.png" width="700">





