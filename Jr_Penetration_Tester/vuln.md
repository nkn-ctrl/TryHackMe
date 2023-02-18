# Vulnerability

## Authentication Bypass
### Username Enumeration
```
user@tryhackme$ ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.217.0/customers/signup -mr "username already exists"
```  
`-w` : usernamelist, `-X` : request method, `-d` : sending data, `FUZZ` : contents from wordlist, `-H` : additonal header, `-u` : target url, `-mr` : the text on the page we are looking for to validate we've found a valid username.  



