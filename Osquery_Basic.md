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

