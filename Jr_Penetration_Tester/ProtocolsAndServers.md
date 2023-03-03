# Protocols and Servers

### Snifing Attack
Sniffing attack refers to using a network packet capture tool to collect information about the target.   
`sudo tcpdump port 110 -A`  
`-A`: To captured packets in ASCII format  

### Man-in-the-Middle (MITM) Attack
A Man-in-the-Middle (MITM) attack occurs when a victim (A) believes they are communicating with a legitimate destination (B) but is unknowingly communicating with an attacker (E).   

<img src="https://user-images.githubusercontent.com/73976100/222410672-b02a909c-5df5-489f-aeb8-9478074b41e0.png" width="600">  

Any time you browse over HTTP, you are susceptible to a MITM attack, and the scary thing is that you cannot recognize it. Many tools would aid you in carrying out such an attack, such as [Ettercap](https://www.ettercap-project.org/) and [Bettercap](https://www.bettercap.org/).  

### Secure Shell (SSH)
`ssh username@SSH_SERVER_IP`  
`scp`:  SSH to transfer files using SCP (Secure Copy Protocol) based on the SSH protocol  
`scp mark@10.10.105.149:/home/mark/archive.tar.gz ~`  
`scp backup.tar.bz2 mark@10.10.105.149:/home/mark/`  

### Password Attack  

