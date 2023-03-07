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

#### The Hunting Grounds - Windows
- There are many great resources on Windows baselining available online:
    - https://adsecurity.org/?p=3299
        - That website alone is an amazing resource, but that specific article is on Windows Baselining.
- Keep it simple. Follow compliance. Turn off unnecessary services. Track software installed on images. If you can, use an application whitelist. Keep antivirus/OS/HIDS/EDR (Endpoint Detection and Response) applications up to date and current. Ensure GPOs are applied to all endpoints. 
- If you want to be stellar...have a contingency plan for a supply chain attack!

#### The Hunting Grounds - Linux
- For the most part, things do not really change when baselining/securing/hardening a Linux-based host.
- Keep it simple and follow the previous measures; however, one of the more important differences is the amount of baseline scripts available for Linux.
        - Choose a reputable source that fits your needs and understand what the configuration script is doing!















