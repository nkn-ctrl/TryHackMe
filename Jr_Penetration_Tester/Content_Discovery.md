# Content Discovery
## Manual Discovery - Robots.txt
`http://[target.url].robots.txt`

## Manual Discovery - Favicon
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
## Manual Discovery - Sitemap.xml
**Sitemap.xml:**  The sitemap.xml file gives a list of every file the website owner wishes to be listed on a search engine.  
`http://[target.url]/sitemap.xml`

## Manual Discovery - HTTP Headers
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

## Manual Discovery - Framework Stack
**Framework Stack:** Once you've established the framework of a website, either from the above favicon example or by looking for clues in the page source such as comments, copyright notices or credits, you can then locate the framework's website. 

## OSINT - Google Hacking / Dorking
|Filter|Example|Description|
|:----:|:-----:|-----------|
|site|site:tryhackme.com|returns results only from the specigied website address|
|inurl|inurl:admin|returns results that have the specified word in the URL|
|filetype|filetype:pdf|returns results which are a particular file extension|
|intitle|intitle:admin|returns results that contain the specified word in the title|  
More information about google hacking can be found here: https://en.wikipedia.org/wiki/Google_hacking



