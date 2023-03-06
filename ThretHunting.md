## Threat Hunting Methodology

### Documentation
- Clear, organized record-keeping
    • Methods used during attack
    • How the attack was detected
    • How the attack was stopped
- Makes it easier to communicate and study the exact nature of the threat
- Allows for more efficient response in the future if a similar attack occurs

### Communicaiton
- Keep stakeholders apprised of any incidents, findings, and impacts
    • Reduces number of surprises
    • Enables you to set expectations
    • Facilitates collaboration
    • Keeps analysts accountable
    • Helps the owner feel invested in the security process
- Note: Advanced threat actors often monitor communication. To avoid giving the attacker additional information, have a secure channel available that can be used to help prevent eavesdropping

Take the Backup Communition.  

### Patience & Persistence
• Conduct numerous investigations, even if it looks like there is no conclusion.  
• Take time to thoroughly analyze and document all findings.  
• Do not rush to conclusions or prematurely close an investigation.  
• Patience and persistence help you improve your threat hunting skills.

### Adaptability
Threat hunters need to be able to rapidly adapt to new technologies, 
attack methods, and emerging threats.  
This includes:  
• Staying up-to-date with new tools, techniques, and tactics used by 
attackers.  
• Being able to adjust hunting strategies and tactics as necessary.  
• Being able to quickly respond to new threats and incidents.  
• Being able to pivot to new areas of focus as needed.  

### Collaboration
Threat hunters need to work with other members of the organization (including security analysts, network administrators, and others) in 
order to effectively respond to cyber threats.  
By merging information and resources, everyone has a better chance of understanding the threat that you face.  
Collaboration methods include:  
• Sharing information with other teams and organizations, whether 
directly or via online platforms.  
• Coordinating investigations and response tactics with other teams.  
• Having regular meetings to discuss progress and share information.  
• Using shared tools (such as SIEM systems) for data collection and analysis.  
• Asking others to lend their expertise to a problem you may not be equipped to handle by yourself.  

### Critical Thinking
Critical Thinking: the ability to analyze and evaluate information and situations in a logical and systematic manner in order to make sound decisions and judgments.  
Critical thinking for Threat Hunting:  
• Necessary for evaluating and analyzing large amounts of data, 
identifying patterns and anomalies, and making informed decisions 
about how to protect yourself from threats.  
• Allows you to discover solutions that may not be obvious.  
• Enables you to weigh the potential risks and benefits of various 
courses of action to best protect your organization  

### Staying Current
Why is it necessary? Because change is imminent and inescapable.  
Competitors, hackers, and Advanced Persistent Threats (APTs) will all embrace new technologies as they search for ways to compromise systems.  
Staying current by studying the latest technologies and security techniques is the best way to protect against new threats.

### Testing & Validation
Just because something worked at first does not mean that it can adequately protect you now.  
Ask yourself: “Is this working correctly?”  
Through testing & validation, you can:  
• Identify security concerns  
• Determine whether or not systems are giving you reliable information  
• Improve existing systems  
• Identify any gaps or weaknesses with your current security practices  
• Collect evidence that will be necessary for proving compliance  

## Threat Hunting
### Types of Threat Hunting
Proactive  
• Identifying threats before they can cause damage  
• Continuous monitoring, testing, and log review to find indicators of 
compromise before the situation escalates  
Reactive  
• Responding to incidents that have already occurred  
• Identifying attack source, containing damage, and mitigating future 
threats  
Opportunistic  
• Taking advantage of unexpected opportunities to respond to threats  
• Monitoring for anomalies and environment changes to detect potential threats before they can cause damage  

### Key Components of Successful Threat Hunting
• Understand the organization’s assets, networks, and systems  
• Have a thorough understanding of the organization’s threat 
landscape (includes known threats, threats actors, and attack 
vectors)  
• Utilize threat intelligence to direct your threat hunting  
• Use a variety of techniques and tools to monitor for suspicious 
activity  
• Formulate incident response plans for when threats are detected  
• Continuously work to improve your organization’s security  
• Collaborate with other teams to ensure everyone is kept informed 
of the latest policies and threats  
• Educate employees so that they are aware of the impact their 
actions have on system security  

### Documentation and Communication
• Keep detailed logs of all threat hunting activities and results  
• Regularly share findings with stakeholders and other teams  
• Create and maintain a centralized repository for threat intelligence and hunting results  
• Communicate with other threat hunting teams to discuss best practices and other findings  
• Document all processes to ensure consistency during future efforts  
• Provide training and education to stakeholders and team members about current best practices and technologies  
• Regularly review and update policies and processes to stay current with emerging threats  

### The Hunting Grounds
So, what do we know about our network?  
– Is it flat?  
– Is it segmented?  
– Do we have a DMZ (Demilitarized Zone)?  
    • What is located inside our DMZ?  
– Do we have asset detection?  
– Do we have service up/down detection?  
– Do we know what kind of network traffic will be in each network segmentation?  

Answering these questions is fundamentally important. If we cannot answer them, it will be difficult to move forward, as we will be unable to differentiate what is normal and what is not.  
• We may not be able to answer all of them!  
    – We could be assessing a new network.  
    – Tools could be in the middle of deployment or not deployed at all.  
• It may not be your responsibility to know every piece of traffic, where it is coming from/to, and why.  
• However, it is your responsibility to be aware of the roles between hosts and servers within your network.   
• For instance, should a workstation in the accounting department be accessing files on an administrator file share? How could we detect this?  

• The reality is you most likely have an established environment.   
• You may already have a baseline of what your configurations are, how your network is segmented, and what normal traffic looks like.  
• Methods to capture and forward Events and Syslogs that identify system changes.  
• If not, have a conversation with your system and network administration team about incorporating methods to understand what “normal” looks like and how we are monitoring and collecting information in our environment.   
• Threat Hunting is a team sport.   
• We skimmed the surface of properly baselining our network, but the idea is that we need to ensure that we have a firm understanding of our network environment (“Threat Hunting Ground”). Our goal is to understand our environment so we can make it as difficult as possible for an adversary to move in, and ensure we rapidly respond if they do.

### Examples of Hunting Grounds
Utility company  
• Includes control systems and SCADA systems, as well as any external networks that interact with company assets.  
• Any third-party vendors’ systems would also be considered a part of the hunting grounds.  
Automobile manufacturer  
• Includes plants used to build automobiles, as well as any other vehicles or stores used to transport and sell cars once they have been built.  
• Public-facing websites used to advertise or sell vehicles could also be a way for attackers to exploit the system.  
Shipping company  
• Includes vessels used to transport goods, as well as any communications or navigation systems used to monitor and track those goods during transit.  
Hospital  
• Includes hospital networks and equipment, as well as any vendors responsible for providing data management and record-keeping capabilities.  







