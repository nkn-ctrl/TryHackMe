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
Syntax of a [hash table](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_hash_tables?view=powershell-7.3&viewFallbackFrom=powershell-7.1)<br>
`@{ <name> = <value>; [<name> = <value> ] ...}`
<br>
<img src="https://assets.tryhackme.com/additional/win-event-logs/filter-hashtable.png" width="700"><br>
<img src="https://assets.tryhackme.com/additional/win-event-logs/build-hash-table.png" width="600">




