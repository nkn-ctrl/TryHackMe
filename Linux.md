# Linux Forensics

## OS and account information
### OS release information
`/etc/os-release`

### User accounts
`/etc/passwd`
```
user@machine$cat /etc/passwd| column -t -s :
root                  x  0      0      root                                /root                    /bin/bash
daemon                x  1      1      daemon                              /usr/sbin                /usr/sbin/nologin
.
.
games                 x  5      60     games                               /usr/games               /usr/sbin/nologin
.
.
ubuntu                x  1000   1000   Ubuntu                              /home/ubuntu             /bin/bash
```

