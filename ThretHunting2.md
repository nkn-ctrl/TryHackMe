# Threat Hunting Grounds
- The Hunting Grounds
- File System Structure
- Command Line Interface
- System Administration Tools
- File Permissions
- Network Actions
- Registry
- Event Logs
- Malware
- Lab 1

## The Hunting Grounds
So, what do we know about our network?
- Is it flat?
- Is it segmented?
- Do we have a DMZ (Demilitarized Zone)?
- What is located inside our DMZ?
- Do we have asset detection?
- Do we have service up/down detection?
- Do we know what kind of network traffic will be in each network segmentation?

- Answering these questions is fundamentally important. If we can’t answer them, it will be difficult to move forward, as we will be unable to differentiate what is normal and what is not normal.
- We may not be able to answer all of the questions!
    - We could be assessing a new network
    - Tools could be in the middle of deployment or not deployed at all Etc.
- It may not be your responsibility to know every piece of traffic, where it’s coming from/to, and why.
- However, it is your responsibility to be aware of the roles between hosts and servers within your network. 
- For instance, should a workstation in the accounting department be accessing files on an administrator file share? How could we detect this?  

Windows Workstation Images
- Be aware of the software installed on your workstation images.  
        - Is AppLocker an option?
- More importantly, track the services that are installed.
    - If you have tight control on services installed, the second an unknown service gets created, you can quickly hone in on it.
        - For example: Windows Event IDs 7045/4697
- How are updates pushed out? 
    - If you know where updates/installations should be happening, this will aid you when you come across a questionable remote service creation.

### The Hunting Grounds - Windows
- There are many great resources on Windows baselining available online:
    - https://adsecurity.org/?p=3299
        - That website alone is an amazing resource, but that specific article is on Windows Baselining.
- Keep it simple. Follow compliance. Turn off unnecessary services. Track software installed on images. If you can, use an application whitelist. Keep antivirus/OS/HIDS/EDR (Endpoint Detection and Response) applications up to date and current. Ensure GPOs are applied to all endpoints. 
- If you want to be stellar...have a contingency plan for a supply chain attack!

### The Hunting Grounds - Linux
- For the most part, things do not really change when baselining/securing/hardening a Linux-based host.
- Keep it simple and follow the previous measures; however, one of the more important differences is the amount of baseline scripts available for Linux.
        - Choose a reputable source that fits your needs and understand what the configuration script is doing!

### The Hunting Grounds - Networking
- Do we have network diagrams on hand?
    - Placement of routers/firewalls/servers/management devices (jump hosts) and even data flow direction should be highlighted.
        - Are these things in locations that make sense? Do you have choke points for critical assets?

### The Hunting Grounds
- The reality is that you most likely have an established environment. You may already have a baseline of what your configurations are, how your network is segmented, what normal traffic looks like, and methods to capture and forward Events and Syslogs that identify system changes.
- If not, have a conversation with your system and network administration team about incorporating methods to understand what “normal” looks like and how we are monitoring and collecting information in our environment. Threat Hunting is a team sport. 
- We skimmed the surface of properly baselining our network, but the idea is that we need to ensure that we have a firm understanding of our network environment (“Threat Hunting Ground”). Our goal is to understand our environment so we can make it as difficult as possible for an adversary to move in, and ensure we rapidly respond if they do.

## File System Structure
### File System Structure - Windows
![WindowsFileSystemStructure](https://user-images.githubusercontent.com/73976100/223368250-fcd71e10-c9cd-41f2-a89a-5d4782ae74b9.jpg")

### File System Structure - Linux
<img src="https://user-images.githubusercontent.com/73976100/223368762-96163399-50ee-47a5-8e2e-aa398b26dbb6.png" width="600">  

## Command Line Interface
### Command Line Interface - Windows
<img src="https://user-images.githubusercontent.com/73976100/223369481-3171541b-f479-49e2-98a0-3f199dc44bdd.png">  

### Command Line Interface - Linux
The Thompson shell is a historical Unix shell developed by Ken Thompson in the late 1970s. It was one of the first Unix shells and was used as the default shell in early versions of Unix. However, it is no longer widely used and has been replaced by more modern shells such as the Bourne shell and the C shell.  
Thompson shell syntax: command [options] [arguments]  

## System Administration Tools
### System Administration Tools - Windows
<img src="https://user-images.githubusercontent.com/73976100/223370435-c3ba3bcb-a3b6-4891-8dfc-6d8ce607ccba.jpg">  
The images above is a Microsoft Management Console instance. These are common command line interfaces found on Windows operating systems.

### System Administration Tools - Linux
To gather information about the system, the following commands prove useful:
- man – provides help
- hostname – name of system
- pwd – present working directory
- date – date configured on system
- last – recent users logged onto system
- ps – list of active processes running
- history – list of recently entered commands
- clear – clears the screen
- ifconfig – networking/IP address information
- uname – system information
- dmesg – kernel information

## File Permissions
### File Permissions - Windows
<img src="(https://user-images.githubusercontent.com/73976100/223371183-bfd73fa1-622d-4846-9904-10f84891022e.png">  
The image above is an example of common folder permissions found on Windows operating systems.

### File Permissions - Linux
<img src="https://user-images.githubusercontent.com/73976100/223371573-7ee88842-87b1-4ff2-bdaf-648a72bbb555.png">  
Permissions on files apply as read, write, and execute.

Permissions on directories:  
- Read:  Allow file names to be read.
- Write: Allow entries to be modified within the directory.
- Execute: Allow access to contents of metadata for entries to the directory.  

Permissions categories for users:
|  |  |
|---|---|
|u|Users (user who owns the file)|
|g|Group (users that are in the file’s group)|
|o|Other (users who do not own are in the file’s group)|
|a|All – user, group, and other|  

On each file or directory, permissions are applied as: Owners, Group, and all users.  
The example of permissions below would be explained as follows:  
`-rw-r--r--`  

| | |
|-|-|
| -|this is a regular file|
|rw-|the Owner has read and write permissions|
|r--|the Group has only read permissions|
|r--|all users have ONLY read permissions|  

To set permissions on a file or directory, the `chmod` command is used. Each permission is assigned a number:  
r = 4  
w = 2  
x = 1  

The number value is assigned for the owner, group, and all users per file. For example, a file with the value of `741` would be displayed as `-rwxr----x`.  
The command for setting the value for the file above would be: `chmod 741 file1.txt`  

The `setuid/setguid` permissions are used to tell the system to run an executable as the owner with the owner’s permissions.

Be careful using `setuid/setgid` bits in permissions. If you incorrectly assign permissions to a file owned by root with the `setuid/setgid` bit set, then you can open your system to intrusion.

You can only assign the `setuid/setgid` bit by explicitly defining permissions. The character for the `setuid/setguid` bit is `s`.

To set the `setuid/setguid` bit on `file2.sh`, you would issue the command:`chmod g+s file2.sh`.   

The sticky bit can be very useful in shared environments. This is because when it has been assigned to the permissions on a directory, it is set so that only the file owner can rename or delete the said file.

You can only assign the sticky bit by explicitly defining permissions. The character for the sticky bit is ‘t’.

To set the sticky bit on a directory named dir1, you would issue the command:
chmod +t dir1. 
<img src="(https://user-images.githubusercontent.com/73976100/223374479-f495c022-019c-49dd-b032-dd59c62664f5.png">  

## Network Actions
### Network Actions – C2
Cobalt Strike is a legitimate commercial tool often used by red teams to provide a post-exploitation implant named ‘Beacon.’ Threat actors have also used it for the delivery of a memory-based implant that can evade detection and provide numerous capabilities. 

Command and Control (C2) communications utilize common protocols, including HTTPS, DNS, and SMB to aid in avoiding anomaly detection by appearing alongside other legitimate network communications.
<img src="https://user-images.githubusercontent.com/73976100/223375079-b8745719-77b0-462d-96ec-a65223efb5af.png">  

### Network Actions – Lateral Movement
Once initial exploitation has been achieved, lateral movement is usually required. Many modern threat actors use ports, protocols, and services that are built into the operating system to move laterally in a network.  
<img src="https://user-images.githubusercontent.com/73976100/223375434-de3ad414-9d99-447b-b948-e47eeb27c148.jpg">  

### Network Actions – Data Exfiltration
There are many ways to exfiltrate data from a network. The increase in quantity of webservers that are installed and available on devices allows them to be leveraged as a covert channel. A rogue SSL connection can be as damaging to an organization as plugging in a malware-infected thumb drive.  
<img src="https://user-images.githubusercontent.com/73976100/223375653-ec26e889-9721-4e00-8a20-cb9fd1ff68bb.png">  

## Registry
File-less execution and persistence is commonly used by threat actors and can be an indictor of exploitation or access. The Windows registry has been a very common place for file-less persistence to hide.

## Event Logs
### Windows PowerShell Logging
- First things first... let’s review and turn on PowerShell logging.
- Windows PowerShell usage among attackers has decreased due to Windows increasing PowerShell logging capabilities. However, this does not mean that has it has gone away by any means, and monitoring malicious PowerShell usage should still be a priority (among many others). It has gotten easier to monitor PowerShell usage with the addition of Module, Transcription, and Script Block Logging.
- This does not apply to Windows Powershell 2.0! Attackers can use PowerShell 2.0 to evade logging features found in newer versions. Make an assessment on whether or not you can disable PowerShell 2.0 in your environment.
- Powershell 2.0 Event Log
<img src="https://user-images.githubusercontent.com/73976100/223567780-3b60f97d-e504-4aaa-8219-a43f515623f4.png">  

- We are going to assume that your environment consists of primarily Windows 10 images.
    - Note that Windows versions 7/8.1/2008/2012 require additional updates to enable enhanced PowerShell logging.
- Logging must be set through Group Policy.  
<img src="https://user-images.githubusercontent.com/73976100/223568245-9d453fd3-a220-4ae3-9179-3155c16d9d2c.png">  

**Module Logging**
- Module logging will record portions of scripts, some de-obfuscated code, and some data formatted for output.
- This logging will capture some details missed by other PowerShell logging sources, though it may not reliably capture the commands executed.
- For the most part, this will only leave you with limited module input.

- Get-NetIPAddress
<img src="https://user-images.githubusercontent.com/73976100/223568794-dae938e4-c51b-4108-bc89-42c0f01314a6.png">  

**Script Block Logging**
- This policy setting will enable logging of all PowerShell input, which includes the processing of commands, script blocks, functions and scripts invoked interactively or automatically.
- Useful for catching those pesky .ps1 scripts!

- Running a .ps1 script:
<img src="https://user-images.githubusercontent.com/73976100/223569462-eb0b5c90-49c6-4fca-9529-195378137ece.png">  

**Transcript Logging**
- Lets you capture both input and output of PowerShell commands into text-based transcripts. You can specify where you store the transcripts when configuring the policy.
- This is the most detailed capture for PowerShell commands, but transcript storage needs to be planned and evaluated for your environment.

- Contents of a transcript .txt file
<img src="(https://user-images.githubusercontent.com/73976100/223569766-31010a82-40d1-402d-9663-9e3e02adf6b7.png">   

### Windows Event Forwarding
“Windows Event Forwarding (WEF) reads any operational or administrative event log on a device in your organization and forwards the events you choose to a Windows Event Collector (WEC) server.” – Windows
- Powerful log forwarding solution integrated with modern versions of Windows
- Agent Free
- Logs can be pushed/pulled to a WEC server
- Windows Event Channels – queues that can be used for collecting and storing event log entries on a server
- Subscriptions – used to define what events/information get sent to the WEC

- Sample WEF Setup Requirements
    - Group Policy Objects (GPOs) to control security auditing/event logging
    - One or more servers configured as a WEC
    - Kerberos for all endpoints for encryption/sending events
    - WinRM turned on for all endpoints that will be forwarding events
    - Firewall rules for WEF activity (ports 5985/5986)
    - GPOs to specify the URL of the WEF subscription manger(s)
    - One or more event log subscriptions; remember, subscriptions are basically a collection of events based on Event IDs or criteria to tell the endpoint which event logs to forward
    - A GPO linked to the root domain with relevant settings; basically, this GPO will be defining which clients talk to which WEC server and how frequently they do so to check if it has subscriptions for any clients and what events to send.
    - The WEC server then needs to be configured with the Eventlog MMC, and this is also where we will define/hold our subscriptions
    - You can then optionally install a third-party log collection agent on the WEC server if you would like to, in order to ingest into a SIEM  

**Simplified WEF Walkthrough**
- Client receives GPO and begins logging
- Client connects to subscription manager (WEC server) and pulls down relevant subscriptions
- Workstation sends events to the WEC server periodically

- Sample Subscription 
<img src="https://user-images.githubusercontent.com/73976100/223570527-cb5f9c42-3968-4381-9677-cc9d8efa71a6.png">  

### Linux Rsyslog
Rsyslog is a log processing system which accepts data from different types of sources and can output it into multiple formats.
- Client/Server model
- Define a server and set up clients
- Can use UDP 514 or TCP 514 (TCP sounds more reliable)... or define your own port!

**Rsyslog Server**
- Install and enable Rsyslog
- Configure our .conf file at /etc/rsyslog.conf
- Define our listening port (UDP and/or TCP)
- Define our ruleset for the facility (or what process/application is generating the message, the severity level, and the destination of where we are going to store the log
- Restart Rsyslog!

**Example Ruleset:**

    $template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log“
    *.* ?RemoteLogs 
    & ~

    $template tells rsyslog daemon to gather all of the received remote messages and store them under distinct logs in /var/log based on the hostname and the program/application that generated the message using the settings in the RemoteLogs template.

    *.*?RemoteLogs records messages from all facilities at all severity levels using the RemoteLogs template. Severity levels include emerg-0, alert-1, crit-2, err-3, warn-4, notice-5, info-6, debug-7, and * means all.

    & ~ instructs Rsyslog to stop processing messages once it has been written to file. If you do not include this, messages will be written to local files.  

**Rsyslog Client**
- Install and enable Rsyslog

## Malware











































