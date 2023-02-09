# Windows Forensics 1

## Windows Registry:
Five root keys:
|Folder/predefined key|	Description|
|:-------------------:|------------|
|HKEY_CURRENT_USER|Contains the root of the configuration information for the user who is currently logged on. The user's folders, screen colors, and Control Panel settings are stored here. This information is associated with the user's profile. This key is sometimes abbreviated as HKCU.|
|HKEY_USERS|Contains all the actively loaded user profiles on the computer. HKEY_CURRENT_USER is a subkey of HKEY_USERS. HKEY_USERS is sometimes abbreviated as HKU.|
|HKEY_LOCAL_MACHINE|Contains configuration information particular to the computer (for any user). This key is sometimes abbreviated as HKLM.|
|HKEY_CLASSES_ROOT| Is a subkey of `HKEY_LOCAL_MACHINE\Software`. The information that is stored here makes sure that the correct program opens when you open a file by using Windows Explorer. This key is sometimes abbreviated as HKCR.<br><br>Starting with Windows 2000, this information is stored under both the HKEY_LOCAL_MACHINE and HKEY_CURRENT_USER keys. The `HKEY_LOCAL_MACHINE\Software\Classes` key contains default settings that can apply to all users on the local computer. The `HKEY_CURRENT_USER\Software\Classes` key has settings that override the default settings and apply only to the interactive user.<br><br>The HKEY_CLASSES_ROOT key provides a view of the registry that merges the information from these two sources. HKEY_CLASSES_ROOT also provides this merged view for programs that are designed for earlier versions of Windows. To change the settings for the interactive user, changes must be made under `HKEY_CURRENT_USER\Software\Classes` instead of under HKEY_CLASSES_ROOT.<br><br>To change the default settings, changes must be made under `HKEY_LOCAL_MACHINE\Software\Classes`. If you write keys to a key under HKEY_CLASSES_ROOT, the system stores the information under `HKEY_LOCAL_MACHINE\Software\Classes`.<br><br>If you write values to a key under HKEY_CLASSES_ROOT, and the key already exists under `HKEY_CURRENT_USER\Software\Classes`, the system will store the information there instead of under `HKEY_LOCAL_MACHINE\Software\Classes`.|
|HKEY_CURRENT_CONFIG|Contains information about the hardware profile that is used by the local computer at system startup.|  
<br>

The majority of these hives are located in the `C:\Windows\System32\Config`.  
These hives are copied to the `C:\Windows\System32\Config\RegBack` directory every ten days.  
<br>

The Amcache Hive:`C:\Windows\AppCompat\Programs\Amcache.hve`  
Windows creates this hive to save information on programs that were recently run on the system.  

### Data Acquisition
- KAPE: https://www.kroll.com/en/services/cyber-risk/incident-response-litigation-support/kroll-artifact-parser-extractor-kape  
- Autopsy: https://www.autopsy.com/
- FTK Imager: https://www.exterro.com/ftk-imager   

### Exploring Windows Registry
- Registry Viewer: https://accessdata.com/product-download/registry-viewer-2-0-0
- Zimmerman's Registry Explorer: https://ericzimmerman.github.io/#!index.md
- RegRipper: https://github.com/keydet89/RegRipper3.0

### System Information and System Accounts
- OS Version: `SOFTWARE\Microsoft\Windows NT\CurrentVersion`
- Current control set: `HKLM\SYSTEM\CurrentControlSet`
    - ControlSet001 `SYSTEM\ControlSet001` will point to the Control Set that the machine booted with.
    - ControlSet002 `SYSTEM\ControlSet002` will be the last known good configuration.
- Computer Name: `SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName`
- Time Zone information: `SYSTEM\CurrentControlSet\Control\TimeZoneInformation`
- Network Interfaces and Past Networks:`SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`

