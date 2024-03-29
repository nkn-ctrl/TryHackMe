# Windows Event Logs
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/windows_event_log_0.png" width="400"> <br>

## Event Viewer
 The log files with the `.evtx` file extension typically reside in `C:\Windows\System32\winevt\Logs`. <br>

- System Logs: Records events associated with the Operating System segments. 
- Security Logs: Records events connected to logon and logoff activities on a device.
- Application Logs: Records events related to applications installed on a system. 
- Directory Service Events: Active Directory changes and activities are recorded in these logs, mainly on domain controllers.
- File Replication Service Events:
- DNS Event Logs: DNS servers use these logs to record domain events and to map out
- Custom Logs: Events are logged by applications that require custom data storage. This allows applications to control the log size or attach other parameters, such as ACLs, for security purposes.<br>
<br>
Event Types<br>

|Event type|Description|
|:--------:|-----------|
|Error|	An event that indicates a significant problem such as loss of data or loss of functionality. For example, if a service fails to load during startup, an Error event is logged.|
|Warning| An event that is not necessarily significant, but may indicate a possible future problem. For example, when disk space is low, a Warning event is logged. |
|Information|An event that describes the successful operation of an application, driver, or service. For example, when a network driver loads successfully, it may be appropriate to log an Information event.|
|Success Audit|An event that records an audited security access attempt that is successful. For example, a user's successful attempt to log on to the system is logged as a Success Audit event.|
|Failure Audit|An event that records an audited security access attempt that fails. For example, if a user tries to access a network drive and fails, the attempt is logged as a Failure Audit event.|
<br>

[Windows Event Log Resources | Event ID Lookup](https://www.myeventlog.com/)<br>
<br>
Event Viewer can be launched by typing `C:\ eventvwr.msc`. <br>

## [wevtutil.exe](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/wevtutil)

`C:\ wevtutil.exe ` <br>
<br>

```
PS> C:\Users\Administrator> wevtutil.exe /?
Windows Events Commandline Utility.
Enables you to retrieve information about event logs and publishers, install and uninstall event manifests, run queries, and export, archive and clear logs.

Usage:

You can use either the short (for example, ep /uni) or long (for example, enum-publishers /unicode) version of the command and option names. Commands, options and option values are not case-sensitive.

Variables are noted in all upper-case.

wevtutil COMMAND [ARGUMENT [ARGUMENT] ...] [/OPTION:VALUE [/OPTION:VALUE] ...]

Commands:

el  | enum-logs              List log names.
gl  | get-log                Get log configuration information.
sl  | set-log                Modify configuration of a log.
ep  | enum-publishers        List event publishers.
gp  | get-publisher          Get publisher configuration information.
im  | install-manifest       Install event publishers and logs from manifest.
um  | uninstall-manifest     Uninstall event publishers and logs from manifest.
qe  | query-events           Query events from a log or log file.
gli | get-log-info           Get log status information.
epl | export-log             Export a log.
al  | archive-log            Archive an exported log.
cl  | clear-log              Clear a log.
```

## [Get-WinEvent](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent?view=powershell-5.1)
The Get-WinEvent cmdlet gets events from event logs, including classic logs, such as the System and Application logs.<br>

### Example 1: Get all the logs from a local computer
```
Get-WinEvent -ListLog *

LogMode   MaximumSizeInBytes RecordCount LogName
-------   ------------------ ----------- -------
Circular            15532032       14500 Application
Circular             1052672         117 Azure Information Protection
Circular             1052672        3015 CxAudioSvcLog
Circular            20971520             ForwardedEvents
Circular            20971520           0 HardwareEvents
```

### Example 2: Get event log providers and log names
```
Get-WinEvent -ListProvider *

Name     : .NET Runtime
LogLinks : {Application}
Opcodes  : {}
Tasks    : {}

Name     : .NET Runtime Optimization Service
LogLinks : {Application}
Opcodes  : {}
Tasks    : {}
```

### Example 3: Log filtering
Log filtering allows you to select events from an event log. We can filter event logs using the Where-Object cmdlet as follows:<br>
```
Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }

   ProviderName: WLMS

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
12/21/2020 4:23:47 AM          100 Information
12/18/2020 3:18:57 PM          100 Information
12/15/2020 8:50:22 AM          100 Information
12/15/2020 8:18:34 AM          100 Information
```
The use of the Get-WinEvent cmdlet's FilterHashtable parameter is recommended to filter event logs.<br>

```
Get-WinEvent -FilterHashtable @{
  LogName='Application' 
  ProviderName='WLMS' 
}
```
Syntax of a [hash table](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables?view=powershell-7.3&viewFallbackFrom=powershell-7.1) <br>
`@{ <name> = <value>; [<name> = <value> ] ...}`
<br>
<img src="https://assets.tryhackme.com/additional/win-event-logs/filter-hashtable.png" width="700"><br>
<br>
<img src="https://assets.tryhackme.com/additional/win-event-logs/build-hash-table.png" width="600"> <img src="https://assets.tryhackme.com/additional/win-event-logs/msi-installer.png"> <br>
<br>
Powershell - Get-WinEvent Filters<br>
```
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} | Select-Object -Property Message | Select-String -Pattern 'SecureString'
```

## XPath Query
[XPath Reference](https://learn.microsoft.com/en-us/previous-versions/dotnet/netframework-4.0/ms256115(v=vs.100))  
```
// The following query selects all events from the channel or log file where the severity level is less than or equal to 3 and the event occurred in the last 24 hour period. 
XPath Query: *[System[(Level <= 3) and TimeCreated[timediff(@SystemTime) <= 86400000]]]
```
```
PS C:\Users\Administrator> Get-WinEvent -Logname Application -FilterXPath '*[System[(Level <= 3) and TimeCreated[timediff(@SystemTime) <= 86400000]]]'


   ProviderName: Microsoft-Windows-Security-SPP

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
1/21/2023 11:02:46 PM          903 Information      The Software Protection service has stopped....
1/21/2023 11:01:05 PM          902 Information      The Software Protection service has started....
1/21/2023 11:01:03 PM         8225 Warning          The existing scheduler data does not match the expected data.  T...
```
<img src="https://assets.tryhackme.com/additional/win-event-logs/xpath-3a.png" width="700"> <br>

```
PS C:\Users\Administrator> Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=100'

   ProviderName: WLMS

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
12/21/2020 4:23:47 AM          100 Information
12/18/2020 3:18:57 PM          100 Information
12/15/2020 8:50:22 AM          100 Information
```

```
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=101 and */System/Provider[@Name="WLMS"]'
```
<br>
<img src="https://assets.tryhackme.com/additional/win-event-logs/xpath-7b.png" width="700"> <br>

```
PS C:\Users\Administrator> Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"' -MaxEvents 1

   ProviderName: Microsoft-Windows-Security-Auditing

TimeCreated                     Id LevelDisplayName Message
-----------                     -- ---------------- -------
12/21/2020 10:50:26 AM         4624 Information     An account was successfully logged on...
```
<br>

- find WLMS events with a System Time of 2020-12-15T01:09:08.940277500Z <br>
    `Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"] and */System/TimeCreated[@Name="SystemTime"]="2020-12-15T01:09:08.940277500Z"'`
- find a user named Sam with an Logon Event ID of 4720 <br>
    `Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="Sam" and */System/EventID=4720'`
- View an oldest Log<br>
   `PS C:\Users\airt002> Get-WinEvent -path C:\Users\airt002\Downloads\Filtering.evtx -FilterXPath '*/System/EventID=3' -Oldest -MaxEvents 1 | Format-List`

## Event IDs
[The Windows Logging Cheat Sheet (Windows 7 - Windows 2012)](https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/580595db9f745688bc7477f6/1476761074992/Windows+Logging+Cheat+Sheet_ver_Oct_2016.pdf)  
<br>
[Appendix L: Events to Monitor](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/appendix-l--events-to-monitor)  
<br>
[Windows Security Log Encyclopedia](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/)  
<br>
[Mapping MITRE ATT&amp;CK with Window Event Log IDs - Security Investigation](https://www.socinvestigation.com/mapping-mitre-attck-with-window-event-log-ids/)  
<br>

- 400   Powershell downgrade attack  
```
Get-WinEvent -LogName "Windows PowerShell" |
Where-Object Id -eq 400 |
Foreach-Object {
     $version = [Version] ($_.Message -replace '(?s).*EngineVersion=([\d\.]+)*.*','$1')
     if($version -lt ([Version] "5.0")) { $_ }
}
```
 
- 4799   A security-enabled local group membership was enumerated  
### User Logon
- 4624   Security / An account was successfully logged on.  
<br>

Which user logged in last?  
```
PS C:\Users\Administrator> Get-WinEvent -Computer $env:COMPUTERNAME -FilterHashtable @{Logname='Security';ID=4624} | select @{N='User'; E={$_.Properties[1].Value}}, TimeCreated
```
[Windowsのnetコマンドの使い方](https://atmarkit.itmedia.co.jp/ait/articles/0211/23/news002.html)  
```
C:\Windows\system32>net user john
User name                    John
Full Name                    John
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            3/2/2019 5:48:19 PM
Password expires             Never
Password changeable          3/2/2019 5:48:19 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   3/2/2019 5:48:32 PM

Logon hours allowed          All

Local Group Memberships      *Users
Global Group memberships     *None
The command completed successfully.
```  
What two accounts had administrative privileges (other than the Administrator user)?  
```
net localgroup administrators
```

- 4672   Security / Special privileges assigned to new logon.
- 1102   Security / Audit event log clear 