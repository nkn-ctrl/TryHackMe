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
bin                   x  2      2      bin                                 /bin                     /usr/sbin/nologin
sys                   x  3      3      sys                                 /dev                     /usr/sbin/nologin
sync                  x  4      65534  sync                                /bin                     /bin/sync
games                 x  5      60     games                               /usr/games               /usr/sbin/nologin
```

