# Reconnaissance

## Passive Reconnaissance
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
<br>

- IP address
- hosting company
- geographic location
- server type and version  








