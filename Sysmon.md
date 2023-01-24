# Sysmon

System Monitor (Sysmon) is a Windows system service and device driver that, once installed on a system, remains resident across system reboots to monitor and log system activity to the Windows event log.  
Events within Sysmon are stored in `Applications and Services Logs/Microsoft/Windows/Sysmon/Operational`  

## Sysmon Config Overview
[Sysmon-Config](https://github.com/SwiftOnSecurity/sysmon-config)  
### Event ID 1: Process Creation
```
<RuleGroup name="" groupRelation="or">
	<ProcessCreate onmatch="exclude">
	 	<CommandLine condition="is">C:\Windows\system32\svchost.exe -k appmodel -p -s camsvc</CommandLine>
	</ProcessCreate>
</RuleGroup>
```
The above code snippet is specifying the Event ID to pull from as well as what condition to look for. In this case, it is excluding the svchost.exe process from the event logs.  
### Event ID 3: Network Connection
```
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
	 	<Image condition="image">nmap.exe</Image>
	 	<DestinationPort name="Alert,Metasploit" condition="is">4444</DestinationPort>
	</NetworkConnect>
</RuleGroup>
```
The first way will identify files transmitted over open ports(nmap.exe).The second method identifies open ports and specifically port 4444 (Metasploit).   
### Event ID 7: Image Loaded
```
<RuleGroup name="" groupRelation="or">
	<ImageLoad onmatch="include">
	 	<ImageLoaded condition="contains">\Temp\</ImageLoaded>
	</ImageLoad>
</RuleGroup>
```
This event will look for DLLs loaded by processes, which is useful when hunting for DLL Injection and DLL Hijacking attacks.The above code snippet will look for any DLLs that have been loaded within the \Temp\ directory  
### Event ID 8: CreateRemoteThread
```
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="include">
	 	<StartAddress name="Alert,Cobalt Strike" condition="end with">0B80</StartAddress>
	 	<SourceImage condition="contains">\</SourceImage>
	</CreateRemoteThread>
</RuleGroup>
```
The CreateRemoteThread Event ID will monitor for processes injecting code into other processes.The first method will look at the memory address for a specific ending condition which could be an indicator of a Cobalt Strike beacon. The second method will look for injected processes that do not have a parent process.  
### Event ID 11: File Created
```
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
	 	<TargetFilename name="Alert,Ransomware" condition="contains">HELP_TO_SAVE_FILES</TargetFilename>
	</FileCreate>
</RuleGroup> 
```
This event ID is will log events when files are created or overwritten the endpoint. This could be used to identify file names and signatures of files that are written to disk( example of a ransomware event monitor).   
### Event ID 12 / 13 / 14: Registry Event
```
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
	 	<TargetObject name="T1484" condition="contains">Windows\System\Scripts</TargetObject>
	</RegistryEvent>
</RuleGroup>
```
### Event ID 15: FileCreateStreamHash
```
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
	 	<TargetFilename condition="end with">.hta</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup> 
```
This event will look for any files created in an alternate data stream.   
### Event ID 22: DNS Event
```
<RuleGroup name="" groupRelation="or">
	<DnsQuery onmatch="exclude">
	 	<QueryName condition="end with">.microsoft.com</QueryName>
	</DnsQuery>
</RuleGroup> 
```
The most common way to deal with these events is to exclude all trusted domains that you know will be very common "noise" in your environment. The above code snippet will get exclude any DNS events with the .microsoft.com query.   

## Hunting Metasploit
```
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```
