### OS and account information
- OS release information  
`cat /etc/os-release`  
- User accounts  
`cat /etc/passwd| column -t -s :`  
- Group Information  
`cat /etc/group`


### Persistence mechanisms  
- Cron jobs  
`cat /etc/crontab`  
- Service startup  
`ls /etc/init.d/`  
- .Bashrc  
`cat ~/.bashrc` /etc/bash.bashrc /etc/profile  

### Evidence of Execution
- Sudo execution history  
`cat /var/log/auth.log* |grep -i COMMAND|tail`  
- Bash history  
`cat ~/.bash_history`  
- Files accessed using vim  
` cat ~/.viminfo`  

### Log files
- Syslog  
`cat /var/log/syslog* | head`  
- Auth logs  
`cat /var/log/auth.log* |head`  
- Third-party logs
`ls /var/log`  
`ls /var/log/apache2/`  

