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
<img src="(https://user-images.githubusercontent.com/73976100/223368250-fcd71e10-c9cd-41f2-a89a-5d4782ae74b9.jpg">  

### File System Structure - Linux
<img src="https://user-images.githubusercontent.com/73976100/223368762-96163399-50ee-47a5-8e2e-aa398b26dbb6.png">  

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
	Read:  Allow file names to be read.
	Write: Allow entries to be modified within the directory.
	Execute: Allow access to contents of metadata for entries to the directory.




















