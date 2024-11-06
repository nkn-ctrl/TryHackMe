# Content Discovery and Subdomain Enumeration

## Content Discovery

### Manual Discovery
- Robots.txt  
`http://[target.url].robots.txt`

- Favicon  
**Favicon:** The favicon is a small icon displayed in the browser's address bar or tab used for branding a website.  
https://wiki.owasp.org/index.php/OWASP_favicon_database  

curl
```
curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico | md5sum
```
PowerShell
```
PS C:\> curl https://static-labs.tryhackme.cloud/sites/favicon/images/favicon.ico -UseBasicParsing -o favicon.ico
PS C:\> Get-FileHash .\favicon.ico -Algorithm MD5 
```  
- Sitemap.xml  
**Sitemap.xml:**  The sitemap.xml file gives a list of every file the website owner wishes to be listed on a search engine.  
`http://[target.url]/sitemap.xml`

- HTTP Headers  
**HTTP Headers:**  can sometimes contain useful information. 
```
user@machine$ curl http://10.10.239.107 -v
*   Trying 10.10.239.107:80...
* TCP_NODELAY set
* Connected to MACHINE_IP (MACHINE_IP) port 80 (#0)
> GET / HTTP/1.1
> Host: MACHINE_IP
> User-Agent: curl/7.68.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.18.0 (Ubuntu)
< X-Powered-By: PHP/7.4.3
< Date: Mon, 19 Jul 2021 14:39:09 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
```
we can see the webserver is NGINX version 1.18.0 and runs PHP version 7.4.3. Using this information, we could find vulnerable versions of software being used.   

- Framework Stack  
**Framework Stack:** Once you've established the framework of a website, either from the above favicon example or by looking for clues in the page source such as comments, copyright notices or credits, you can then locate the framework's website. 

### OSINT
- Google Hacking / Dorking  
    |Filter|Example|Description|
    |:----:|:-----:|-----------|
    |site|site:tryhackme.com|returns results only from the specigied website address|
    |inurl|inurl:admin|returns results that have the specified word in the URL|
    |filetype|filetype:pdf|returns results which are a particular file extension|
    |intitle|intitle:admin|returns results that contain the specified word in the title|  

More information about google hacking can be found here: https://en.wikipedia.org/wiki/Google_hacking

- Wappalyzer  
Wappalyzer (https://www.wappalyzer.com/) is an online tool and browser extension that helps identify what technologies a website uses, such as frameworks, Content Management Systems (CMS), payment processors and much more, and it can even find version numbers as well.  

- Wayback Machine  
The Wayback Machine (https://archive.org/web/) is a historical archive of websites. This service can help uncover old pages that may still be active on the current website.  

- GitHub  
You can use GitHub's search feature to look for company names or website names to try and locate repositories belonging to your target. Once discovered, you may have access to source code, passwords or other content that you hadn't yet found.

- S3 Buckets  
**S3 Buckets:** are a storage service provided by Amazon AWS. The format of the S3 buckets is `http(s)://{name}.s3.amazonaws.com` where `{name}` is decided by the owner, such as `tryhackme-assets.s3.amazonaws.com`.   One common automation method is by using the company name followed by common terms such as `{name}-assets`, `{name}-www`, `{name}-public`, `{name}-private`, etc. 

## Automated Discovery  
Wordlists are just text files that contain a long list of commonly used words.  
Seclists: https://github.com/danielmiessler/SecLists
<br>

**Automation Tools**
- ffuf
```
user@machine$ ffuf -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -u http://10.10.249.195/FUZZ
```
`-replay-proxy http://127.0.0.1:8080`  sending results to Burp proxy  

- dirb
```
user@machine$ dirb http://10.10.249.195/ /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```
- gobuster
```
user@machine$ gobuster dir --url http://10.10.249.195/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```  
|Gobuster flag|Description|
|:----|:----|
|-e|Print the full URLs in your console|
|-u|The target URL|
|-w|Path to your wordlist|
|-U and -P|Username and Password for Basic Auth|
|-p <x>|Proxy to use for requests|
|-c <http cookies>|Specify a cookie for simulating your auth|　　


## Subdomain Enumeration
### OSINT
- SSL/TLS Certificates  
 When an `SSL/TLS` (Secure Sockets Layer/Transport Layer Security) certificate is created for a domain by a `CA` (Certificate Authority), CA's take part in what's called `Certificate Transparency (CT) logs`. These are publicly accessible logs of every SSL/TLS certificate created for a domain name. The purpose of Certificate Transparency logs is to stop malicious and accidentally made certificates from being used. We can use this service to our advantage to discover subdomains belonging to a domain, sites like https://crt.sh and https://ui.ctsearch.entrust.com/ui/ctsearchui offer a searchable database of certificates that shows current and historical results.

- Search Engines  
`-site:www.domain.com site:*.domain.com` would only contain results leading to the domain name `domain.com` but exclude any links to `www.domain.com`; therefore, it shows us only subdomain names belonging to `domain.com`.  

- Automation Using Sublist3r  
To speed up the process of OSINT subdomain discovery, we can automate [Sublist3r](https://github.com/aboul3la/Sublist3r).  
```
user@thm:~$ ./sublist3r.py -d acmeitsupport.thm
.
.
[-] Total Unique Subdomains Found: 2
web55.acmeitsupport.thm
www.acmeitsupport.thm
```

### Bruteforce
- DNS Bruteforce  
Bruteforce DNS (Domain Name System) enumeration is the method of trying tens, hundreds, thousands or even millions of different possible subdomains from a pre-defined list of commonly used subdomains.  
```
user@thm:~$ dnsrecon -t brt -d acmeitsupport.thm
[*] No file was specified with domains to check.
[*] Using file provided with tool: /usr/share/dnsrecon/namelist.txt
[*]     A api.acmeitsupport.thm 10.10.10.10
[*]     A www.acmeitsupport.thm 10.10.10.10
[+] 2 Record Found
```

### Virtual Hosts
Some subdomains aren't always hosted in publically accessible DNS results, such as development versions of a web application or administration portals. Instead, the DNS record could be kept on a private DNS server or recorded on the developer's machines in their `/etc/hosts` file (or `c:\windows\system32\drivers\etc\hosts` file for Windows users) which maps domain names to IP addresses.   
```
user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.212.189
```
Because the above command will always produce a valid result, we need to filter the output. We can do this by using the page size result with the `-fs` switch.  Edit the below command replacing `{size}` with the most occurring size value from the previous result
```
user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.212.189 -fs {size}
```

