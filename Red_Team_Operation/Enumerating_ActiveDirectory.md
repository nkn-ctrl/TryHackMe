# Enumeration through Bloodhound  
[BloodHound](https://github.com/BloodHoundAD/BloodHound)  

## Sharphound  
- Sharphound.ps1 - PowerShell script for running Sharphound. However, the latest release of Sharphound has stopped releasing the Powershell script version. This version is good to use with RATs since the script can be loaded directly into memory, evading on-disk AV scans.  
- Sharphound.exe - A Windows executable version for running Sharphound.  
- AzureHound.ps1 - PowerShell script for running Sharphound for Azure (Microsoft Cloud Computing Services) instances. Bloodhound can ingest data enumerated from Azure to find attack paths related to the configuration of Azure Identity and Access Management.  

`Sharphound.exe --CollectionMethods <Methods> --Domain za.tryhackme.com --ExcludeDCs`  
- CollectionMethods - Determines what kind of data Sharphound would collect. The most common options are `Default` or `All`. Also, since Sharphound caches information, once the first run has been completed, you can only use the Session collection method to retrieve new user sessions to speed up the process.  
- Domain - Here, we specify the domain we want to enumerate. In some instances, you may want to enumerate a parent or other domain that has trust with your existing domain. You can tell Sharphound which domain should be enumerated by altering this parameter.  
- ExcludeDCs -This will instruct Sharphound not to touch domain controllers, which reduces the likelihood that the Sharphound run will raise an alert.  

```
PS C:\Users\gordon.stevens\Documents\>SharpHound.exe --CollectionMethods All --Domain za.tryhackme.com --ExcludeDCs
2022-03-16T19:11:41.2898508+00:00|INFORMATION|Resolved Collection Methods: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-03-16T19:11:41.3056683+00:00|INFORMATION|Initializing SharpHound at 7:11 PM on 3/16/2022
2022-03-16T19:11:41.6648113+00:00|INFORMATION|Flags: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-03-16T19:11:41.8211318+00:00|INFORMATION|Beginning LDAP search for za.tryhackme.com
[....]
2022-03-16T19:12:31.6981568+00:00|INFORMATION|Output channel closed, waiting for output task to complete
Closing writers
2022-03-16T19:12:32.2605943+00:00|INFORMATION|Status: 2163 objects finished (+2163 43.26)/s -- Using 85 MB RAM
2022-03-16T19:12:32.2605943+00:00|INFORMATION|Enumeration finished in 00:00:50.4369344
2022-03-16T19:12:32.5418517+00:00|INFORMATION|SharpHound Enumeration Completed at 7:12 PM on 3/16/2022! Happy Graphing!
```  

## Bloodhound  

Bloodhound uses Neo4j as its backend database and graphing system. Before we can start Bloodhound, we need to load Neo4j:   

```
┌──(kali㉿kali)-[~]
└─$ sudo neo4j start        
Directories in use:
home:         /usr/share/neo4j
config:       /usr/share/neo4j/conf
logs:         /etc/neo4j/logs
plugins:      /usr/share/neo4j/plugins
import:       /usr/share/neo4j/import
data:         /etc/neo4j/data
certificates: /usr/share/neo4j/certificates
licenses:     /usr/share/neo4j/licenses
run:          /var/lib/neo4j/run
Starting Neo4j.
Started neo4j (pid:982266). It is available at http://localhost:7474
There may be a short delay until the server is ready.
```  
In another Terminal tab, run `bloodhound --no-sandbox`. This will show you the authentication GUI:  

![39f261aecedccbaf118eb2ee69d55129](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/4c4c1a73-dc4d-413f-870b-785a52eace0b)  

The default credentials for the neo4j database will be `neo4j:neo4j`. Use this to authenticate in Bloodhound. To import our results, you will need to recover the ZIP file from the Windows host. The simplest way is to use SCP command on your AttackBox:  
`scp <AD Username>@THMJMP1.za.tryhackme.com:C:/Users/<AD Username>/Documents/<Sharphound ZIP> .`  

Once you provide your password, this will copy the results to your current working directory. Drag and drop the ZIP file onto the Bloodhound GUI to import into Bloodhound.  

## Attack Paths  

![a6e1af6f79653eeedb18ac9c3be7a038](https://github.com/nkn-ctrl/TryHackMe/assets/73976100/7eb56164-f8e1-49fe-8270-526abd77b30a)  
- Overview - Provides summaries information such as the number of active sessions the account has and if it can reach high-value targets.  
- Node Properties - Shows information regarding the AD account, such as the display name and the title.  
- Extra Properties - Provides more detailed AD information such as the distinguished name and when the account was created.  
- Group Membership - Shows information regarding the groups that the account is a member of.  
- Local Admin Rights - Provides information on domain-joined hosts where the account has administrative privileges.  
- Execution Rights - Provides information on special privileges such as the ability to RDP into a machine.  
- Outbound Control Rights - Shows information regarding AD objects where this account has permissions to modify their attributes.  
- Inbound Control Rights -  Provides information regarding AD objects that can modify the attributes of this account.  

