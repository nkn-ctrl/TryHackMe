# Linux Forensics

## OS and account information
### OS release information
`/etc/os-release`

### User accounts
`/etc/passwd`  
username : password information : user id (uid) : group id (gid) : description : home directory information : default shell  
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
The username is ubuntu, its password information field shows `x`, which signifies that the password information is stored in the `/etc/shadow` file.   

### Group Information
`/etc/group`  
```
user@machine$ cat /etc/group
.
.
adm:x:4:syslog,ubuntu
```
We can see that the user `ubuntu` belongs to the `adm` group, which has a password stored in the `/etc/shadow` file, signified by the `x` character. The gid is 4, and the group contains 2 users, Syslog, and ubuntu.

### Sudoers List
A Linux host allows only those users to elevate privileges to `sudo`, which are present in the Sudoers list.  
`/etc/sudoers`

### Login information
- `/var/log/btmp` : information about failed logins  
- `/var/log/wtmp` : historical data of logins  
They are binary file, which havet to be read using the `last` utility.
```
user@machine$ sudo last -f /var/log/wtmp
reboot   system boot  5.4.0-1029-aws   Tue Mar 29 17:28   still running
reboot   system boot  5.4.0-1029-aws   Tue Mar 29 04:46 - 15:52  (11:05)
reboot   system boot  5.4.0-1029-aws   Mon Mar 28 01:35 - 01:51 (1+00:16)

wtmp begins Mon Mar 28 01:35:10 2022
```
### Authentication logs
`/var/log/auth.log`  
We can use `tail`, `head`, `more` or `less` utilities to make it easier to read.
```
user@machine$ cat /var/log/auth.log |tail
Mar 29 17:28:48 tryhackme gnome-keyring-daemon[989]: The PKCS#11 component was already initialized
Mar 29 17:28:48 tryhackme gnome-keyring-daemon[989]: The SSH agent was already initialized
```

## System Configuration
### Hostname
`/etc/hostname`

### Timezone
`/etc/timezone`

### Network Configuration
`/etc/network/interfaces`
```
user@machine$ ip address show
```

### Active network connections
`netstat`
```
user@machine$ netstat -natp
.
.
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:5901          0.0.0.0:*               LISTEN      829/Xtigervnc
```

