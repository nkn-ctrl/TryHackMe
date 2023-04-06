# Password Attacks

[top 100 and most common and seen passwords](https://techlabuzz.com/top-100-most-common-passwords/)  

- Password guessing is a technique used to target online protocols and services. Therefore, it's considered time-consuming and opens up the opportunity to generate logs for the failed login attempts. A password guessing attack conducted on a web-based system often requires a new request to be sent for each attempt, which can be easily detected. It may cause an account to be locked out if the system is designed and configured securely.
- Password cracking is a technique performed locally or on systems controlled by the attacker.

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


