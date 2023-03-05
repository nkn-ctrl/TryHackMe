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










