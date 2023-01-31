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

## Analysing Logs and Alerts
### Dashboard
Dashboards are the most important components of any SIEM. SIEM presents the data for analysis after being normalized and ingested. Some of the information that can be found in a dashboard are:  
- Alert Highlights
- System Notification
- Health Alert
- List of Failed Login Attempts
- Events Ingested Count
- Rules triggered
- Top Domains Visited

## ELK
Investigate VPN logs through ELK.  
### Elastic stack
Elastic stack is the collection of different open source components linked together to help users take the data from any source and in any format and perform a search, analyze and visualize the data in real-time.  
<img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5e8dd9a4a45e18443162feab/room-content/ed8750288e179609301f57c45bdfda43.png" width="700">  

### Logstash
- Input plugins https://www.elastic.co/guide/en/logstash/8.1/input-plugins.html
- Filter plugins https://www.elastic.co/guide/en/logstash/8.1/filter-plugins.html
- Output plugins https://www.elastic.co/guide/en/logstash/8.1/filter-plugins.html

### KQL (Kibana Query Language)
https://www.elastic.co/guide/en/kibana/7.17/kuery-query.html

## Splunk: Basics
Splunk is one of the leading SIEM solutions in the market that provides the ability to collect, analyze and correlate the network and machine logs in real-time.   
- Navigating Splunk https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/NavigatingSplunk  

# Incident handling with Splunk
## Incident Handling Life Cycle
1. Preparation  
Readiness of an organization against an attack.Documenting the requirements, defining the policies, incorporating the security controls to monitor like EDR / SIEM / IDS / IPS, etc. and also hiring/training the staff.
2. Detection and Analysis  
This phase covers getting alerts from the security controls like SIEM/EDR investigating the alert to find the root cause. 
3. Containment, Eradication, and Recovery  
This phase covers the actions needed to prevent the incident from spreading and securing the network.
4. Post-Incident Activity / Lessons Learnt  
This phase includes identifying the loopholes in the organization's security posture, which led to an intrusion, and improving so that the attack does not happen next time. 

## Cyber Kill Chain
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/CyberKillChain.png" width="700">

## Reconnaissance Phase

