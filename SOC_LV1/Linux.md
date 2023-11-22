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

### Running processes
The `ps` utility shows details about the running processes.
- `ps`: see the running processes  
    - PID: The process ID (unique to the process)
    - TTY: Terminal type used by the user
    - Time: Amount of CPU time used by the process (this is NOT the time this process has been running for)
    - CMD: The command or executable running (will NOT display any command line parameter)  
    - `ps -A`: view all running processes
    - `ps axjf`: view process tree 
    - `ps aux`: show processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x)  
```
user@machine$ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         729  0.0  0.0   7352  2212 ttyS0    Ss+  17:28   0:00 /sbin/agetty -o -p -- \u --
```

`lsof -p <PID>`:   
`ls -als /proc/<PID>`: 

`ldd <FILE>`: Show libraries that execute files use

### DNS information
`/etc/hosts`  
`/etc/resolv.conf` :The information about DNS servers  

## Persistence mechanisms
### Cron jobs
`/etc/crontab`
```
user@machine$ cat /etc/crontab 
.
.
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
```

### Service startup
Services can be set up in Linux that will start and run in the background after every system boot.  
`/etc/init.d` : We can check the contents of the directory by using the `ls` utility.  
```
user@machine$ ls /etc/init.d/
acpid       avahi-daemon      cups          hibagent           kmod             networking     pppd-dns                     screen-cleanup     unattended-upgrades
.
.
```

### .Bashrc
When a bash shell is spawned, it runs the commands stored in the `.bashrc` file. This file can be considered as a startup list of actions to be performed. Hence it can prove to be a good place to look for persistence.   
```
user@machine$ cat ~/.bashrc
```
System-wide settings are stored in `/etc/bash.bashrc` and `/etc/profile` files, so it is often a good idea to take a look at these files as well.  

## Evidence of Execution
### Sudo execution history
All the commands that are run on a Linux host using `sudo` are stored in `/var/log/auth.log`.  
We can use the `grep` utility to filter out only the required information from the auth log.
```
user@machine$ cat /var/log/auth.log* |grep -i COMMAND|tail
Mar 29 17:28:58 tryhackme pkexec[1618]: ubuntu: Error executing command as another user: Not authorized [USER=root] [TTY=unknown] [CWD=/home/ubuntu] [COMMAND=/usr/lib/update-notifier/package-system-locked]
Mar 29 17:49:52 tryhackme sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/cat /etc/sudoers
Mar 29 17:55:22 tryhackme sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/cat /var/log/btmp
Mar 29 17:55:39 tryhackme sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/cat /var/log/wtmp
Mar 29 18:00:54 tryhackme sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/tail -f /var/log/btmp
.
.
```

### Bash history
Any commands other than the ones run using sudo are stored in the bash history.Every user's bash history is stored separately in that user's home folder.
```
user@machine$ cat ~/.bash_history 
cd Downloads/
ls
unzip PracticalMalwareAnalysis-Labs-master.zip 
cd PracticalMalwareAnalysis-Labs-master/
```

### Files accessed using vim
The `Vim` text editor stores logs for opened files in Vim in the file named `.viminfo` in the home directory.
```
user@machine$ cat ~/.viminfo
# This viminfo file was generated by Vim 8.1.
# You may edit it if you're careful!
.
.
```

## Log files
Logs are generally found in the `/var/log` directory.

### Syslog
The Syslog contains messages that are recorded by the host about system activity.  
`/var/log/syslog`
```
user@machine$ cat /var/log/syslog* | head
Mar 29 00:00:37 tryhackme systemd-resolved[519]: Server returned error NXDOMAIN, mitigating potential DNS violation DVE-2018-0001, retrying transaction with reduced feature level UDP.
Mar 29 00:00:37 tryhackme rsyslogd: [origin software="rsyslogd" swVersion="8.2001.0" x-pid="635" x-info="https://www.rsyslog.com"] rsyslogd was HUPed
Mar 29 00:00:37 tryhackme systemd[1]: man-db.service: Succeeded.
Mar 29 00:00:37 tryhackme systemd[1]: Finished Daily man-db regeneration.
Mar 29 00:09:01 tryhackme CRON[7713]: (root) CMD (   test -x /etc/cron.daily/popularity-contest && /etc/cron.daily/popularity-contest --crond)
Mar 29 00:17:01 tryhackme CRON[7726]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
```

### Auth logs
```
user@machine$ cat /var/log/auth.log* |head
Feb 27 13:52:33 ip-10-10-238-44 useradd[392]: new group: name=ubuntu, GID=1000
Feb 27 13:52:33 ip-10-10-238-44 useradd[392]: new user: name=ubuntu, UID=1000, GID=1000, home=/home/ubuntu, shell=/bin/bash, from=none
Feb 27 13:52:33 ip-10-10-238-44 useradd[392]: add 'ubuntu' to group 'adm'
Feb 27 13:52:33 ip-10-10-238-44 useradd[392]: add 'ubuntu' to group 'dialout'
```

### Third-party logs
The `/var/log/` directory contains logs for third-party applications such as webserver, database, or file share server logs.  
```
user@machine$ ls /var/log
Xorg.0.log          apt                    cloud-init.log  dmesg.2.gz      gdm3                    kern.log.1         prime-supported.log  syslog.2.gz
Xorg.0.log.old      auth.log               cups            dmesg.3.gz      gpu-manager-switch.log  landscape          private              syslog.3.gz
alternatives.log    auth.log.1             dist-upgrade    dmesg.4.gz      gpu-manager.log         lastlog            samba                syslog.4.gz
```

```
user@machine$  ls /var/log/apache2/
access.log  error.log  other_vhosts_access.log
```  
- Web Servers:
    - Nginx:
        - Access Logs: `/var/log/nginx/access.log`
        - Error Logs: `/var/log/nginx/error.log`
    - Apache:
        - Access Logs: `/var/log/apache2/access.log`
        - Error Logs: `/var/log/apache2/error.log`
- Databases:
    - MySQL:
        - Error Logs: `/var/log/mysql/error.log`
    - PostgreSQL:
        - Error and Activity Logs: `/var/log/postgresql/postgresql-{version}-main.log`
- Web Applications:
    - PHP:
        - Error Logs: `/var/log/php/error.log`
- Operating Systems:
    - Linux:
        - General System Logs: `/var/log/syslog`
        - Authentication Logs: `/var/log/auth.log`
- Firewalls and IDS/IPS:
    - iptables:
        - Firewall Logs: `/var/log/iptables.log`
    - Snort:
        - Snort `Logs: /var/log/snort/`


[Linux Forensics Cheat Sheat](https://github.com/nkn-ctrl/pushtest/blob/main/Linux-Forensics-Cheatsheet.pdf)  

## Grep
`grep -v`
`grep -lr "Search Words" <Directory>`
