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


