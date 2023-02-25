# Reconnaissance

## Passive Reconnaissance
### whois
- Registrar: Via which registrar was the domain name registered?
- Contact info of registrant: Name, organization, address, phone, among other things. (unless made hidden via a privacy service)
- Creation, update, and expiration dates: When was the domain name first registered? When was it last updated? And when does it need to be renewed?
- Name Server: Which server to ask to resolve the domain name?

### nslookup and dig
`nslookup OPTIONS DOMAIN_NAME SERVER`  
|Query type|	Result|
|:--------:|----------|
|A	|IPv4 Addresses|
|AAAA|	IPv6 Addresses|
|CNAME|	Canonical Name|
|MX|	Mail Servers|
|SOA|	Start of Authority|
|TXT|	TXT Record|
