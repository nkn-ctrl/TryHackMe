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

Event Viewer can be launched by typing `C:\ eventvwr.msc`. <br>





