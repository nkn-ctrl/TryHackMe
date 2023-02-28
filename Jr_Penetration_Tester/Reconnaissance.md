# Reconnaissance

## Passive Reconnaissance
### Summary  
|Purpose|	Commandline Example|
|-------|----------------------|
|Lookup WHOIS record	|`whois tryhackme.com`|
|Lookup DNS A records	|`nslookup -type=A tryhackme.com`|
|Lookup DNS MX records at DNS server	|`nslookup -type=MX tryhackme.com 1.1.1.1`|
|Lookup DNS TXT records	|`nslookup -type=TXT tryhackme.com`|
|Lookup DNS A records	|`dig tryhackme.com A`|
|Lookup DNS MX records at DNS server	|`dig @1.1.1.1 tryhackme.com MX`|
|Lookup DNS TXT records	|`dig tryhackme.com TXT`|

### whois
- Registrar: Via which registrar was the domain name registered?
- Contact info of registrant: Name, organization, address, phone, among other things. (unless made hidden via a privacy service)
- Creation, update, and expiration dates: When was the domain name first registered? When was it last updated? And when does it need to be renewed?
- Name Server: Which server to ask to resolve the domain name?

### nslookup and dig
- nslookup   
`nslookup OPTIONS DOMAIN_NAME SERVER`  

|Query type|	Result|
|:--------:|----------|
|A	|IPv4 Addresses|
|AAAA|	IPv6 Addresses|
|CNAME|	Canonical Name|
|MX|	Mail Servers|
|SOA|	Start of Authority|
|TXT|	TXT Record|  

`nslookup -type=A tryhackme.com 1.1.1.1`  
`nslookup -type=MX tryhackme.com`  

- dig "Domain Information Groper"  
`dig DOMAIN_NAME`  
`dig DOMAIN_NAME TYPE`   
`dig @SERVER DOMAIN_NAME TYPE`  
    - SERVER is the DNS server that you want to query.
    - DOMAIN_NAME is the domain name you are looking up.
    - TYPE contains the DNS record type, as shown in the table provided earlier.  

`dig @1.1.1.1 tryhackme.com MX`  

### DNSDumpster
[DNSDumpster](https://dnsdumpster.com/)  
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/221383161-245e669c-e295-46c1-8c2f-9dfc5ee5218b.png" width="700">
</p>

### Shodan.io
[Shodan.io](https://www.shodan.io/)  

<img src="https://user-images.githubusercontent.com/73976100/221397062-079cd6bc-64ee-4084-9884-89e2729e8416.png" width="700">  

- IP address
- hosting company
- geographic location
- server type and version  

## Active Reconnaissance
### Ping

```
ping [-aAbBdCDefhLnOqrRUvV46] [-c count] [-F flowlabel] [-i interval] [-I interface] [-l preload] [-m mark]
     [-M pmtudisc_option] [-N nodeinfo_option] [-w deadline] [-W timeout] [-p pattern] [-Q tos]
     [-s packetsize] [-S sndbuf] [-t ttl] [-T timestamp option] [hop...] {destination}
```

### Trace Route
On Linux and macOS, the command to use is `traceroute MACHINE_IP`, and on MS Windows, it is `tracert MACHINE_IP`. traceroute tries to discover the routers across the path from your system to the target system.  
- The number of hops/routers between your system and the target system depends on the time you are running traceroute. There is no guarantee that your packets will always follow the same route, even if you are on the same network or you repeat the traceroute command within a short time.
- Some routers return a public IP address. You might examine a few of these routers based on the scope of the intended penetration testing.
- Some routers don’t return a reply.

### Telnet
The telnet client, with its simplicity, can be used for other purposes. Knowing that telnet client relies on the TCP protocol, you can use Telnet to connect to any service and grab its banner. Using telnet `MACHINE_IP PORT`, you can connect to any service running on TCP and even exchange a few messages unless it uses encryption.  
```
pentester@TryHackMe$ telnet MACHINE_IP 80
Trying MACHINE_IP...
Connected to MACHINE_IP.
Escape character is '^]'.
GET / HTTP/1.1
host: telnet

HTTP/1.1 200 OK
Server: nginx/1.6.2
Date: Tue, 17 Aug 2021 11:13:25 GMT
Content-Type: text/html
Content-Length: 867
Last-Modified: Tue, 17 Aug 2021 11:12:16 GMT
Connection: keep-alive
ETag: "611b9990-363"
Accept-Ranges: bytes
```


## test
hogehoge




