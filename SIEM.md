# SIEM
SIEM stands for Security Information and Event Management system.  

## Network Visibility through SIEM
1) Host-Centric Log Sources
- A user accessing a file
- A user attempting to authenticate.
- A process Execution Activity
- A process adding/editing/deleting a registry key or value.
- Powershell execution  

2) Network-Centric Log Sources
- SSH connection
- A file being accessed via FTP
- Web traffic
- A user accessing company's resources through VPN.
- Network file sharing Activity

## Log Sources and Log Ingestion
### Windows Machine
Windows records every event that can be viewed through the `Event Viewer` utility.   

### Linux Workstation
Linux OS stores all the related logs, such as events, errors, warnings, etc.
- `/var/log/httpd` : Contains HTTP Request  / Response and error logs.
- `/var/log/cron`   : Events related to cron jobs are stored in this location.
- `/var/log/auth.log` and `/var/log/secure` : Stores authentication related logs.
- `/var/log/kern` : This file stores kernel related events.

### Web Server
In Linux, common locations to write all apache related logs are `/var/log/apache` or `/var/log/httpd`.


