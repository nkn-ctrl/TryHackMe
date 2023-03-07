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

## Network Security Monitoring
Network Security Monitoring helps to detect and respond to cyber threats by continuously monitoring network traffic and identifying abnormal or suspicious activity.

### Types of Network Monitoring Tools
Network Intrusion Detection Systems (NIDS): used to monitor network traffic and detect any suspicious activity.

Network Intrusion Prevention Systems (NIPS): similar to NIDS, except these are capable of responding to and preventing these threats from continuing.

Endpoint Detection and Response (EDR): monitor endpoint devices (laptops, servers, phones, etc.) for suspicious activity and can respond to threats in real time.

Security Information and Event Management (SIEM): collect, correlate, and analyze system logs in order to identify potential threats and provide threat intelligence.

Network Access Control (NAC): monitor network access and ensure that only authorized devices and users have network access.

Sandboxing: simulate the execution of suspicious files in a controlled environment where their behavior can be studied.

Behavioral Analytics: use machine learning algorithms to detect anomalies in network or device usage.

Cloud Security Solutions: specifically designed to monitor and protect cloud-based infrastructure and services.

<details>
<!--- 
lab X exercise 
--->
## LAB: ATT&CK Framework
### What is the ATT&CK Framework?
The MITRE ATT&CK Framework is a framework created by MITRE to document attacker tactics and techniques, then categorize them in a consistent and systematic way. Organizations often look to the ATT&CK Framework to check for the coverage that specific defensive tooling provides to their network, and to choose new defensive techniques to provide for areas that lack coverage. The MITRE ATT&CK Framework also gives organizations a universal way to discuss attacker tactics and techniques with other like-minded professionals.
Also, organizations can utilize MITRE's public database of adversary groups to identify groups that may be a threat to their organization or industry with a list of their specific tactics and techniques.
The purpose of this lab is to take a deeper dive into the Cyber Kill Chain and view the different stages of an attack. This exercise will also serve as a way for you to explore MITRE ATT&CK, and how it can apply to your organization.
### <u>Web Browser</u> 
1) **Open a web browser and navigate to MITRE ATT&CK Framework URL provided by your instructor.**

<br>
### Exercises:
#### Exercise 1:
1. You are now on the main page the MITRE ATT&CK website. Look at the Matrix on the page, as well as its labeled columns.
   ![ATT_CK_Image](ATT_CK_Image.png)

2. How do each of these columns relate to the Cyber Kill Chain of Reconnaissance -> Weaponization -> Delivery -> Exploitation -> Installation -> Command & Control (C2) -> Actions on Objectives? How do these columns differ? How are they the same? What are some of the reasons why the ATT&CK framework is broken down the way it is?
3. Click **show sub-techniques** button above the Matrix to expand sub-techniques.
4. Look at the **Reconnaissance** column, then look at the sub-techniques listed beneath, following the tree, and observe the categories listed under each sub-technique. 
5. Select **Spearphishing Service** sub-technique under **Phishing for Information,** then take a few minutes to read the page on the technique.
6. Look under the "Mitigations" section. What is the only listed way for an organization to mitigate the threat of spearphishing? Why do you think that is the only mitigation that is listed?
7. Look under **Detection** section. Look at the specific detection methods listed. How can they apply to your organization? What are some of the downsides of these detection techniques? What are the upsides? How would you go about implementing them?
8. Click **Network Traffic Flow** link under **Detection.**
9. Look at the description for this section of the new page. What are some of the tools listed in the description?
10. Take some time to explore the ATT&CK Matrix. Repeat the steps above on one or more areas which may interest you.
#### Exercise 2:
1. Click **Groups** at the top of the page.
2. On the left is a public list of groups, categorized by MITRE.
3. Scroll down in the list and select **Silence,** then take a few minutes to read the page on the group.
4. Under **Techniques Used,** look at the line **Non-Standard Port.** How is this group using this technique?
5. What are some mitigations organizations can use to counter this technique?
6. Click **Non-Standard Port,** then read about the technique. What are some other groups utilizing this technique?
7. What are the detection mechanisms listed that can help organizations detect such a technique?
8. What are the mitigations listed?
9. Why are the detection and mitigation measures different? How could you apply these within your organization?
10. Take some time to explore the **Groups** section. Repeat the steps above on one or more groups which may interest you.
#### Exercise 3:
1. Click **Software** at the top of the page.
2. Below you will find a public list of software listed by MITRE. Take a few minutes to read the description of the page, and take note of the two types of software listed. Without referencing the page, can you think of some examples for each of these two types of software?
3. Scroll down the page and select **arp.**
4. Take a few minutes to read the page. Have you heard of this software before?
5. How is arp used by an adversary? How is arp used by non-malicious software? Can you think of any ways that you could mitigate this threat? What would be the result of blocking this technique? What are some groups who also use arp as part of their attack process?
6. Click **Remote System Discovery,** then take a few minutes to read the page.
7. What is meant by **preventive controls** under the **Mitigations** header?
8. Think about how some of the listed detection techniques apply to you and your organization. What are some advantages and disadvantages of implementing some of these detection measures?
9. Under **Techniques Used,** look at the line **Non-Standard Port.** How is this group using this technique?
10. What are some mitigations that organizations can use to counter this technique?
11. Click **Non-Standard Port,** then read about the technique. What are some other groups utilizing this technique?
12. What are the detection mechanisms listed that can help organizations detect such a technique?
13. What are the mitigations listed?
14. Why do you think the detection and mitigation measures are different? How could you apply these within your organization?
15. Take some time to explore the **Software** section. Repeat the steps above on one or more groups which may interest you.
#### Exercise 4:
1. Look at the **Tactics** and **Techniques** individual sections. What is the difference between the two terms? How do they relate to each other?
2. Look at the **Data Sources** section. How could each of the data sources apply to your organization? What are some reasons why an organization may choose to monitor some data sources, but not others? How do you think you would prioritize data sources if given the choice, and why? Does this alter the way you think about the way your organization collects data?
3. Expand the different data sources to observe each of the data sources in each collection.
4. What is the most interesting technique you have found within the Matrix so far, and why?

</details>


