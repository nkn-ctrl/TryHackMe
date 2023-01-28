# Osquery: The Basics
<img src="https://github.com/nkn-ctrl/pushtest/blob/main/osquery_logo.webp" width="700">  
<br>

[Osquery](https://osquery.io/) is an open-source agent created by Facebook in 2014. It converts the operating system into a relational database. It allows us to ask questions from the tables using SQL queries, like returning the list of running processes, a user account created on the host, and the process of communicating with certain suspicious domains.  

## Osquery: Interactive Mode
run command
- Windows:  `C:\Program Files\osquery\osqueryi.exe`
- linux:    `osqueryi`  

<details>
<summary> .help </summary>
osquery> .help<br>
Welcome to the osquery shell. Please explore your OS!<br>
You are connected to a transient 'in-memory' virtual database.<br>
<br>

.all [TABLE]     Select all from a table<br>
.bail ON|OFF     Stop after hitting an error<br>
.connect PATH    Connect to an osquery extension socket<br>
.disconnect      Disconnect from a connected extension socket<br>
.echo ON|OFF     Turn command echo on or off<br>
.exit            Exit this program<br>
.features        List osquery's features and their statuses<br>
.headers ON|OFF  Turn display of headers on or off<br>
.help            Show this message<br>
.mode MODE       Set output mode where MODE is one of:<br>
                   csv      Comma-separated values<br>
                   column   Left-aligned columns see .width<br>
                   line     One value per line<br>
                   list     Values delimited by .separator string<br>
                   pretty   Pretty printed SQL results (default)<br>
.nullvalue STR   Use STRING in place of NULL values<br>
.print STR...    Print literal STRING<br>
.quit            Exit this program<br>
.schema [TABLE]  Show the CREATE statements<br>
.separator STR   Change separator used by output mode<br>
.socket          Show the local osquery extensions socket path<br>
.show            Show the current values for various settings<br>
.summary         Alias for the show meta command<br>
.tables [TABLE]  List names of tables<br>
.types [SQL]     Show result of getQueryColumns for the given query<br>
.width [NUM1]+   Set column widths for "column" mode<br>
.timer ON|OFF      Turn the CPU timer measurement on or off<br>
</details>

### List the tables
`.tables` : List all the available tables that can be queried.  
`.tables process` : Tables are associated with processes.  
`.tables user` : Tables are associated with user.  
```
root@analyst$ osqueryi
Using a virtual database. Need help, type '.help'
osquery> .table user
  => user_groups
  => user_ssh_keys
  => userassist
  => users
```

### Understanding the table Schema

`.schema table_name` : List  of a table's schema  
```
osquery> .schema users
CREATE TABLE users(`uid` BIGINT, `gid` BIGINT, `uid_signed` BIGINT, `gid_signed` BIGINT, `username` TEXT, `description` TEXT, `directory` TEXT, `shell` TEXT, `uuid` TEXT, `type` TEXT, `is_hidden` INTEGER HIDDEN, `pid_with_namespace` INTEGER HIDDEN, PRIMARY KEY (`uid`, `username`, `uuid`, `pid_with_namespace`)) WITHOUT ROWID;
```  
<br>

SQL QUERY SYNTAX: `select column1, column2, column3 from table;`  
```
osquery>select gid, uid, description, username, directory from users;
+-----+------+------------------------------------------------------------+----------------------+-------------------------------------------+
| gid | uid  | description                                                | username           | directory                                   |
+-----+------+-------------------------------------------------------------------------------------------------------------------------------+
| 544 | 500  | Built-in account for administering the computer/domain     | Administrator      |                                             |
| 581 | 503  | A user account managed by the system.                      | DefaultAccount     |                                             |
| 546 | 501  | Built-in account for guest access to the computer/domain   | Guest              |                                             |
| 544 | 1002 |                                                            | James              | C:\Users\James                              |
| 18  | 18   |                                                            | SYSTEM             | %systemroot%\system32\config\systemprofile  |
| 19  | 19   |                                                            | LOCAL SERVICE      | %systemroot%\ServiceProfiles\LocalService   |
| 20  | 20   |                                                            | NETWORK SERVICE    | %systemroot%\ServiceProfiles\NetworkService |
+-----+------+------------------------------------------------------------+--------------------+---------------------------------------------+
```  
### Disolay Mode
```
.mode MODE       Set output mode where MODE is one of:
                   csv      Comma-separated values
                   column   Left-aligned columns see .width
                   line     One value per line
                   list     Values delimited by .separator string
                   pretty   Pretty printed SQL results (default)
```

## Schema [Documentation](https://osquery.io/schema/)

## Creating SQL queries
### Exploring Installed Programs
Query: `SELECT * FROM programs LIMIT 1;`  
```
osquery> .mode line
osquery> SELECT * FROM programs LIMIT 1;
              name = Windows Driver Package - Nuvoton Technology Corporation (Serial) Ports  (11/09/2011 1.0.2011.1109)
           version = 11/09/2011 1.0.2011.1109
  install_location =
    install_source =
          language =
         publisher = Nuvoton Technology Corporation
  uninstall_string = rundll32.exe C:\PROGRA~1\DIFX\04518C~1\DIFxAppA.dll, DIFxARPUninstallDriverPackage C:\Windows\System32\DriverStore\FileRepository\nuvserial.inf_amd64_394508c166981603\nuvserial.inf
      install_date =
identifying_number =
```  
In the above example `LIMIT` was used followed by the number to limit the results to display.  
<br>

Query: `SELECT name, version, install_location, install_date from programs limit 1;`  
```
osquery> select name, version, install_location, install_date from programs limit 1;
            name = Windows Driver Package - Nuvoton Technology Corporation (Serial) Ports  (11/09/2011 1.0.2011.1109)
         version = 11/09/2011 1.0.2011.1109
install_location =
    install_date =
```

### Count
To see how many programs or entries in any table are returned.  
Query: `SELECT count(*) from programs;`  
```
osquery> select count(*) from programs;
count(*) = 45
```  
<br>

### WHERE Clause
Optionally, you can use a WHERE clause to narrow down the list of results returned based on specified criteria.   
Query: `SELECT * FROM users WHERE username='James';`
```
osquery>SELECT * FROM users WHERE username='James';
        uid = 1002
        gid = 544
 uid_signed = 1002
 gid_signed = 544
   username = James
description =
  directory = C:\Users\James
      shell = C:\Windows\system32\cmd.exe
       uuid = S-1-5-21-605937711-2036809076-574958819-1002
       type = local
```  

Filtering operators that can be used in a WHERE clause:
- `=` [equal]
- `<>`  [not equal]
- `>`, `>=` [greater than, greater than, or equal to]
- `<`, `<=` [less than or less than or equal to] 
- `BETWEEN` [between a range]
- `LIKE` [pattern wildcard searches]
- `%` [wildcard, multiple characters]
- `_` [wildcard, one character]  
```
osquery> select name from autoexec where name like '%bat';
osquery> select * from autoexec where name like '%bat';
+---------------------------------------------------------------------------------------------+----------------+---------------+
| path                                                                                        | name           | source        |
+---------------------------------------------------------------------------------------------+----------------+---------------+
| C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\batstartup.bat                 | batstartup.bat | startup_items |
| C:\Users\James\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\batstartup.bat | batstartup.bat | startup_items |
+---------------------------------------------------------------------------------------------+----------------+---------------+
```
### Matching Wildcard Rules
Below is a screenshot from the Osquery [documentation](https://osquery.readthedocs.io/en/stable/deployment/file-integrity-monitoring/) showing examples of using wildcards when used in folder structures:
- `%`: Match all files and folders for one level.
- `%%`: Match all files and folders recursively.
- `%abc`: Match all within-level ending in "abc".
- `abc%`: Match all within-level starting with "abc".
### Matching Examples
- `/Users/%/Library`: Monitor for changes to every user's Library folder, but not the contents within.
- `/Users/%/Library/`: Monitor for changes to files within each Library folder, but not the contents of their subdirectories.
- `/Users/%/Library/%`: Same, changes to files within each Library folder.
- `/Users/%/Library/%%`: Monitor changes recursively within each Library.
- `/bin/%sh`: Monitor the `bin` directory for changes ending in `sh`.  
<br>

Some tables require a **WHERE** clause, such as the file table, to return a value.

### Joining Tables using JOIN Function
`uid` in `users`table is meant to identify the user record,and `uid` in `users`table is meant that the user responsible for executing the particular process.  
Query1: `select uid, pid, name, path from processes;`  
Query2: `select uid, username, description from users;`  
Joined Query: `select p.pid, p.name, p.path, u.username from processes p JOIN users u on u.uid=p.uid LIMIT 10;`  
```
osquery>select p.pid, p.name, p.path, u.username from processes p JOIN users u on u.uid=p.uid LIMIT 10;
+-------+-------------------+---------------------------------------+----------+
| pid   | name              | path                                  | username |
+-------+-------------------+---------------------------------------+----------+
| 7560  | sihost.exe        | C:\Windows\System32\sihost.exe        | James    |
| 6984  | svchost.exe       | C:\Windows\System32\svchost.exe       | James    |
| 7100  | svchost.exe       | C:\Windows\System32\svchost.exe       | James    |
| 7144  | svchost.exe       | C:\Windows\System32\svchost.exe       | James    |
| 8636  | ctfmon.exe        | C:\Windows\System32\ctfmon.exe        | James    |
| 8712  | taskhostw.exe     | C:\Windows\System32\taskhostw.exe     | James    |
| 9260  | svchost.exe       | C:\Windows\System32\svchost.exe       | James    |
| 10168 | RuntimeBroker.exe | C:\Windows\System32\RuntimeBroker.exe | James    |
| 10232 | RuntimeBroker.exe | C:\Windows\System32\RuntimeBroker.exe | James    |
| 8924  | svchost.exe       | C:\Windows\System32\svchost.exe       | James    |
+-------+-------------------+---------------------------------------+----------+
```

## Query Example
Query: `select path, key, name from registry where key = 'HKEY_USERS';`  
Query: `select * from ie_extensions;`  
Query: `select name,install_location from programs where name LIKE '%wireshark%';`
<br>

`userassist`table : UserAssist Registry Key tracks when a user executes an application from Windows Explorer.  


