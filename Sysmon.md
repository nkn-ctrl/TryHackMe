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
