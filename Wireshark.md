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
|-------|-------|------------|---------|
|eq|==|Equal|```ip.src == 10.10.10.100```|
|ne|!=|Not equal|```ip.src != 10.10.10.100```|
|gt|>|Greater than|```ip.ttl > 250```|
|lt|<|Less Than|```ip.ttl < 10```|
|ge|>=|Greater than or equal to|```ip.ttl >= 0xFA```|
|le|<=|Less than or equal to|```ip.ttl <= 0xA```|
|and|&& Logical AND|```(ip.src == 10.10.10.100) AND (ip.src == 10.10.10.111)```|
|or|\|\| Logical OR|```(ip.src == 10.10.10.100) OR (ip.src == 10.10.10.111)```|
|not|! Logical NOT|```!(ip.src == 10.10.10.222)```|
