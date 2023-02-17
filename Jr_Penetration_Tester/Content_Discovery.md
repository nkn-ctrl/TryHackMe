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


