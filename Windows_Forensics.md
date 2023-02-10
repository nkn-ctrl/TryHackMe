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

### Accessing registry hives offline
The majority of these hives are located in the `C:\Windows\System32\Config`.  
1. DEFAULT (mounted on `HKEY_USERS\DEFAULT`)
2. SAM (mounted on `HKEY_LOCAL_MACHINE\SAM`)
3. SECURITY (mounted on `HKEY_LOCAL_MACHINE\Security`)
4. SOFTWARE (mounted on `HKEY_LOCAL_MACHINE\Software`)
5. SYSTEM (mounted on `HKEY_LOCAL_MACHINE\System`)  
<br>

**Hives containing user information**: a user’s profile directory is located in C:\Users\<username>\  
1. NTUSER.DAT (mounted on HKEY_CURRENT_USER when a user logs in)  
    `C:\Users\<username>\`
2. USRCLASS.DAT (mounted on HKEY_CURRENT_USER\Software\CLASSES)  
    `C:\Users\<username>\AppData\Local\Microsoft\Windows`

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
    The past networks a given machine was connected
    - `SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged`
    - `SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Managed`
- **Autostart Programs (Autoruns):** programs or commands that run when a user logs on.  
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run`
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce`
    - `SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce`
    - `SOFTWARE\Microsoft\Windows\CurrentVersion\policies\Explorer\Run`
    - `SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
    - information about services: `information about services:`  
        <img src="https://github.com/nkn-ctrl/pushtest/blob/main/windows_reg_start.png" width="700"><br>
        In this registry key, if the start key is set to `0x02`, this means that this service will start at boot.
- SAM hive and user information: `SAM\Domains\Account\Users`
    <img src="https://github.com/nkn-ctrl/pushtest/blob/main/windows_reg_sam.png" width="700"><br>
    The information contained here includes the relative identifier (RID) of the user, number of times the user logged in, last login time, last failed login, last password change, password expiry, password policy and password hint, and any groups that the user is a part of.   

### Usage or knowledge of files/folders
- Recent Files: `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`
- Office Recent Files: `NTUSER.DAT\Software\Microsoft\Office\VERSION`  
- ShellBags: Each users can change a folder layout. This information is stored.
    - `USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\Bags`  
    - `USRCLASS.DAT\Local Settings\Software\Microsoft\Windows\Shell\BagMRU`  
    - `NTUSER.DAT\Software\Microsoft\Windows\Shell\BagMRU`  
    - `NTUSER.DAT\Software\Microsoft\Windows\Shell\Bags`  
- Open/Save and LastVisited Dialog MRUs:
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePIDlMRU`
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\LastVisitedPidlMRU`
- Windows Explorer Address/Search Bars:
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths`
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`