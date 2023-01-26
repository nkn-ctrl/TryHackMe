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
By default, Metasploit uses port `4444`. The code snippet below will use event ID 3 along with the destination port to identify active connections specifically connections on port `4444` and `5555`.   
```
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=4444'
```
## Detecting Mimikatz
Mimikatz is well known and commonly used to dump credentials from memory along with other Windows post-exploitation activity. Mimikatz is mainly known for dumping LSASS.  
### Detecting File Creation
```
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
		<TargetFileName condition="contains">mimikatz</TargetFileName>
	</FileCreate>
</RuleGroup>
```
### Hunting Abnormal LSASS Behavior
ProcessAccess event ID to hunt for abnormal LSASS behavior. If LSASS is accessed by a process other than `svchost.exe` it should be considered suspicious behavior. Below is to aid in looking for suspicious events you can use a filter to only look for processes besides svchost.exe.   
```
<RuleGroup name="" groupRelation="or">
	<ProcessAccess onmatch="exclude">
		<SourceImage condition="image">svchost.exe</SourceImage>
	</ProcessAccess>
	<ProcessAccess onmatch="include">
		<TargetImage condition="image">lsass.exe</TargetImage>
	</ProcessAccess>
</RuleGroup>
```  
### Detecting LSASS Behavior with PowerShell
```
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=10 and */EventData/Data[@Name="TargetImage"] and */EventData/Data="C:\Windows\system32\lsass.exe"'
```

## Hunting Malware
### Hunting Rats and C2 Servers
The code snippet below is from the Ion-Storm configuration file which will alert when specific ports like `1034` and `1604` as well as exclude common network connections like OneDrive, by excluding events we still see everything that we want without missing anything and cutting down on noise.   
```
<RuleGroup name="" groupRelation="or">
	<NetworkConnect onmatch="include">
		<DestinationPort condition="is">1034</DestinationPort>
		<DestinationPort condition="is">1604</DestinationPort>
	</NetworkConnect>
	<NetworkConnect onmatch="exclude">
		<Image condition="image">OneDrive.exe</Image>
	</NetworkConnect>
</RuleGroup>
```
### Hunting for Common Back Connect Ports with PowerShell
```
Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=3 and */EventData/Data[@Name="DestinationPort"] and */EventData/Data=<Port>'
```

## Hunting Persistence
### Hunting Startup Persistence
Detections for a file being placed in the `\Startup\` or `\Start Menu` directories.  MITRE ATT&CK [T1547](https://attack.mitre.org/techniques/T1547/).  
```
<RuleGroup name="" groupRelation="or">
	<FileCreate onmatch="include">
		<TargetFilename name="T1023" condition="contains">\Start Menu</TargetFilename>
		<TargetFilename name="T1165" condition="contains">\Startup\</TargetFilename>
	</FileCreate>
</RuleGroup>
```  
### Hunting Registry Key Persistence
Registry modification that adjusts that places a script inside `CurrentVersion\Windows\Run` and other registry locations. MITRE ATT&CK [T1112](https://attack.mitre.org/techniques/T1112/)  
```
<RuleGroup name="" groupRelation="or">
	<RegistryEvent onmatch="include">
		<TargetObject name="T1060,RunKey" condition="contains">CurrentVersion\Run</TargetObject>
		<TargetObject name="T1484" condition="contains">Group Policy\Scripts</TargetObject>
		<TargetObject name="T1060" condition="contains">CurrentVersion\Windows\Run</TargetObject>
	</RegistryEvent>
</RuleGroup>
```

## Detecting Evasion Techniques
### Hunting Alternate Data Streams
Event ID 15 will hash and log any NTFS Streams that are included within the Sysmon configuration file.The code snippet below will hunt for files in the `Temp` and `Startup` folder as well as `.hta` and `.bat` extension.  
```
<RuleGroup name="" groupRelation="or">
	<FileCreateStreamHash onmatch="include">
		<TargetFilename condition="contains">Downloads</TargetFilename>
		<TargetFilename condition="contains">Temp\7z</TargetFilename>
		<TargetFilename condition="ends with">.hta</TargetFilename>
		<TargetFilename condition="ends with">.bat</TargetFilename>
	</FileCreateStreamHash>
</RuleGroup>
```

### Detecting Remote Threads
Remote threads is used to evade detections in combination with other techniques. Remote threads are created using the Windows API `CreateRemoteThread` and can be accessed using `OpenThread` and `ResumeThread`. Sysmon event ID 8 from the SwiftOnSecurity configuration file.  
```
<RuleGroup name="" groupRelation="or">
	<CreateRemoteThread onmatch="exclude">
		<SourceImage condition="is">C:\Windows\system32\svchost.exe</SourceImage>
		<TargetImage condition="is">C:\Program Files (x86)\Google\Chrome\Application\chrome.exe</TargetImage>
	</CreateRemoteThread>
</RuleGroup>
```

### Detecting Evasion Techniques with PowerShell
`Get-WinEvent -Path <Path to Log> -FilterXPath '*/System/EventID=8'`

