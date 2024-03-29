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
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/elk.png" width="700">  

### Logstash
- Input plugins https://www.elastic.co/guide/en/logstash/8.1/input-plugins.html
- Filter plugins https://www.elastic.co/guide/en/logstash/8.1/filter-plugins.html
- Output plugins https://www.elastic.co/guide/en/logstash/8.1/filter-plugins.html

### KQL (Kibana Query Language)
https://www.elastic.co/guide/en/kibana/7.17/kuery-query.html

## Splunk: Basics
Splunk is one of the leading SIEM solutions in the market that provides the ability to collect, analyze and correlate the network and machine logs in real-time.   
- Navigating Splunk https://docs.splunk.com/Documentation/Splunk/8.1.2/SearchTutorial/NavigatingSplunk  
<br>
<br>

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

### Scenario
A Big corporate organization Wayne Enterprises has recently faced a cyber-attack where the attackers broke into their network, found their way to their web server, and have successfully defaced their website `http://www.imreallynotbatman.com`. Their website is now showing the trademark of the attackers with the message YOUR SITE HAS BEEN DEFACED.
#### Splunk
During our investigation, we will be using Splunk as our SIEM solution. Logs are being ingested from webserver/firewall/Suricata/Sysmon etc. 
#### Interesting log Sources
|Log Sources|Details|
|:---------:|-------|
|wineventlog|It contains Windows Event logs |
|winRegistry|It contains the logs related to registry creation / modification / deletion etc.|
|XmlWinEventLog|It contains the sysmon event logs. It is a very important log source from an investigation point of view.|
|fortigate_utm|It contains Fortinet Firewall logs|
|iis|It contains IIS web server logs|
|Nessus:scan|It contains the results from the Nessus vulnerability scanner.|
|Suricata|It contains the details of the alerts from the Suricata IDS.   This log source shows which alert was triggered and what caused the alert to get triggered— a very important log source for the Investigation. |
|stream:http|It contains the network flow related to http traffic.|
|stream: DNS|It contains the network flow related to DNS traffic.|
|stream:icmp|It contains the network flow related to icmp traffic.|


## Reconnaissance Phase
We will start our analysis by examining any reconnaissance attempt against the webserver `imreallynotbatman.com`.  
Search Query: `index=botsv1 imreallynotbatman.com`  
<br>

Where do we first need to look? If we look at the available log sources, we will find some log sources covering the network traffic.  
Search Query: `index=botsv1 imreallynotbatman.com sourcetype=stream:http`  

### Validate the IP that is scanning
So what do we need to do to validate the scanning attempt? Look into the suricata logs.  
Search Query: `index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata`  

## Exploitation Phase
The attacker needs to exploit the vulnerability to gain access to the system/server.  
In this task, we will look at the potential exploitation attempt from the attacker against our web server and see if the attacker got successful in exploiting or not.  

### Count
Let's use the following search query to see the number of counts by each source IP against the webserver.  
Search Query:`index=botsv1 imreallynotbatman.com sourcetype=stream* | stats count(src_ip) as Requests by src_ip | sort - Requests`  
Query Explanation: This query uses the stats function to display the count of the IP addresses in the field src_ip.  
<br>

Now we will narrow down the result to show requests sent to our web server, which has the IP `192.168.250.70`.  
Search Query: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70"`  
Query Explanation: This query will look for all the inbound traffic towards IP 192.168.250.70.  
<bf>

`form_data` The field contains the requests sent through the form on the admin panel page, which has a login page. We suspect the attacker may have tried multiple credentials in an attempt to gain access to the admin panel.  
Search Query:  
`index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri="/joomla/administrator/index.php" | table _time uri src_ip dest_ip form_data`

### Extracting Username and Passwd Fields using Regex
Search Query:  
`index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST uri="/joomla/administrator/index.php" form_data=*username*passwd* | table _time uri src_ip dest_ip form_data`
<bf>

Now, let's use Regex.  `rex field=form_data "passwd=(?<creds>\w+)"` To extract the passwd values only. This will pick the form_data field and extract all the values found with the field. creds.  
Search Query:  
`index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)"  | table src_ip creds`  
<br>

Search Query:   
`index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)" |table _time src_ip uri http_user_agent creds`

## Installation Phase
Once the attacker has successfully exploited the security of a system, he will try to install a backdoor or an application for persistence or to gain more control of the system. This activity comes under the installation phase.  

Search Query: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" *.exe`  

Search Query: `index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" "part_filename{}"="3791.exe"`  

#### Was this file executed on the server after being uploaded?
We have found that file 3791.exe was uploaded on the server. The question that may come to our mind would be, was this file executed on the server? We need to narrow down our search query to show the logs from the host-centric log sources to answer this question.  

Following the Host-centric log, sources were found to have traces of the executable 3791. exe.
- Sysmon
- WinEventlog
- fortigate_utm  

For the evidence of execution, we can leverage sysmon and look at the EventCode=1 for program execution.  
Search Query: `index=botsv1 "3791.exe" sourcetype="XmlWinEventLog" EventCode=1`

## Action on Objective
As the website was defaced due to a successful attack by the adversary, it would be helpful to understand better what ended up on the website that caused defacement.  

We will start our investigation by examining the Suricata log source and the IP addresses communicating with the webserver 192.168.250.70.  
Search Query: `index=botsv1 dest=192.168.250.70 sourcetype=suricata`  

Let us change the flow direction to see if any communication originates from the server.  
Search Query: `index=botsv1 src=192.168.250.70 sourcetype=suricata`  

Search Query: `index=botsv1 url="/poisonivy-is-coming-for-you-batman.jpeg" dest_ip="192.168.250.70" | table _time src dest_ip http.hostname url`

## Command and Control:
The attacker uploaded the file to the server before defacing it. While doing so, the attacker used a Dynamic DNS to resolve a malicious IP. Our objective would be to find the IP that the attacker decided the DNS.  

We will first pick fortigate_utm to review the firewall logs and then move on to the other log sources.  
Search Query: `index=botsv1 sourcetype=fortigate_utm"poisonivy-is-coming-for-you-batman.jpeg"`　　

Let us verify the answer by looking at another log source.`stream:http`.  
Search Query: `index=botsv1 sourcetype=stream:http dest_ip=23.22.63.114 "poisonivy-is-coming-for-you-batman.jpeg" src_ip=192.168.250.70`

## Weaponization
In the weaponization phase, the adversaries would:
- Create Malware / Malicious document to gain initial access / evade detection etc.
- Establish domains similar to the target domain to trick users.
- Create a Command and Control Server for the post-exploitation communication/activity etc.  

We have found some domains / IP addresses associated with the attacker during the investigations. This task will mainly look into OSINT sites to see what more information we can get about the adversary.

#### Robtex:
[Robtex](https://www.robtex.com/) is a Threat Intel site that provides information about IP addresses, domain names, etc.

#### ThreatCrowd:
[ThreatCrowd](https://www.threatcrowd.org/) is a Search Engine for Threats that provides intel based on the IP, domain, email address, etc.

## Delivery Phase
Attackers create malware and infect devices to gain initial access or evade defenses and find ways to deliver it through different means. We have identified various IP addresses, domains and Email addresses associated with this adversary. Our task for this lesson would be to use the information we have about the adversary and use various Threat Hunting platforms and OSINT sites to find any malware linked with the adversary.  

## Conclusion:
In this fun exercise, as a SOC Analyst, we have investigated a cyber-attack where the attacker had defaced a website 'imreallynotbatman.com' of the Wayne Enterprise. We mapped the attacker's activities into the 7 phases of the Cyber Kill Chain. Let us recap everything we have found so far:

### Reconnaissance Phase:
We first looked at any reconnaissance activity from the attacker to identify the IP address and other details about the adversary.  
Findings:  
- IP Address 40.80.148.42 was found to be scanning our webserver.
- The attacker was using Acunetix as a web scanner.

### Exploitation Phase:
We then looked into the traces of exploitation attempts and found brute-force attacks against our server, which were successful.  
Findings:
- Brute force attack originated from IP 23.22.63.114.
- The IP address used to gain access: 40.80.148.42
- 142 unique brute force attempts were made against the server, out of which one attempt was successful

### Installation Phase:
 Next, we looked at the installation phase to see any executable from the attacker's IP Address uploaded to our server.  
Findings:
- A malicious executable file 3791.exe was observed to be uploaded by the attacker.
- We looked at the sysmon logs and found the MD5 hash of the file.

### Action on Objective:
After compromising the web server, the attacker defaced the website.  
Findings:
- We examined the logs and found the file name used to deface the webserver.

### Weaponization Phase:
We used various threat Intel platforms to find the attacker's infrastructure based on the following information we saw in the above activities.  
Information we had:
- Domain: prankglassinebracket.jumpingcrab.com
- IP Address: 23.22.63.114  

Findings:
- Multiple masquerading domains were found associated with the attacker's IPs.
- An email of the user Lillian.rose@po1s0n1vy.com was also found associated with the attacker's IP address.

### Deliver Phase:
In this phase, we again leveraged online Threat Intel sites to find malware associated with the adversary's IP address, which appeared to be a secondary attack vector if the initial compromise failed.  
Findings:
- A malware name MirandaTateScreensaver.scr.exe was found associated with the adversary.
- MD5 of the malware was c99131e0169171935c5ac32615ed6261
