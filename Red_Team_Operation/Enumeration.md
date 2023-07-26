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

