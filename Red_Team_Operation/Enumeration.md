# Enumeration

|Linux Command|Description|
|:----:|:----|
|`hostname`|shows the system’s hostname|
|`who`|shows who is logged in|
|`whoami`|shows the effective username|
|`w`|shows who is logged in and what they are doing|
|`last`|shows a listing of the last logged-in users|
|`ip address show`|shows the network interfaces and addresses|
|`arp`|shows the ARP cache|
|`netstat`|prints network connections|
|`ps`|shows a snapshot of the current processes|


|Windows Command|Description|
|:----:|:----|
|`systeminfo`|shows OS configuration information, including service pack levels|
|`whoami`|shows the user name and group information along with the respective security identifiers|
|`netstat`|shows protocol statistics and current TCP/IP network connections|
|`net user`|shows the user accounts on the computer|
|`net localgroup`|shows the local groups on the computer|
|`arp`|shows the IP-to-Physical address translation tables|

## SMB
### SMBClient
`smbclient //<IP_ADDR>/anonymous`  
```
smbclient Usage
smbclient -L <server>                    # List shares
smbclient -L <server> -U <user>%<pwd>    # List shares with auth

smbclient //<server>/<share> -U <user>            # Connect to share interactively

smbclient //<server>/<share> -c <cmd> -U <user>   # Run command on share
smbclient Commands
The following commands can be passed with smbclient //<server>/<share> -c

ls                      # List files on share
cd <folder>; ls         # List files on some folder

mkdir <folder>          # Create remote folder

get <remote file name>                    # Download file
put <local file name> <remote filename>   # Upload file
rename <remote file> <remote new name>    # Rename file
```
### enum4linux
```
enum4linux -a <IP_ADDR>
```  
<details>
<summary>details</summary>
```
 enum4linux 10.10.215.115
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Jul 26 11:22:29 2023

 =========================================( Target Information )=========================================

Target ........... 10.10.215.115
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

~ ~ ~ ~ ~ ~ ~ ~
~ ~ ~ ~ ~ ~ ~ ~

==================( Users on 10.10.215.115 via RID cycling (RIDS: 500-550,1000-1050) )==================


[I] Found new SID: 
S-1-22-1

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[I] Found new SID: 
S-1-5-32

[+] Enumerating users using SID S-1-5-21-2853212168-2008227510-3551253869 and logon username '', password ''

S-1-5-21-2853212168-2008227510-3551253869-501 BASIC2\nobody (Local User)
S-1-5-21-2853212168-2008227510-3551253869-513 BASIC2\None (Domain Group)

[+] Enumerating users using SID S-1-22-1 and logon username '', password ''

S-1-22-1-1000 Unix User\kay (Local User)
S-1-22-1-1001 Unix User\jan (Local User)

[+] Enumerating users using SID S-1-5-32 and logon username '', password ''

S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
```
