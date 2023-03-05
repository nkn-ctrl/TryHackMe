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
<br>

`nc -lvnp <port-number>`: starting a netcat listener  
`-l` is used to tell netcat that this will be a listener  
`-v` is used to request a verbose output  
`-n` tells netcat not to resolve host names or use DNS. Explaining this is outwith the scope of the room.  
`-p` indicates that the port specification will follow.  