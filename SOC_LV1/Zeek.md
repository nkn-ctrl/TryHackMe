# [Zeek](https://zeek.org/)
<img src="https://docs.zeek.org/en/master/_images/zeek-logo-text.png" width="800">

## Network Security Monitoring and Zeek
### The default log path
```/opt/zeek/logs/```
### Check the version of the Zeek instance
```zeek -v```
### ZeekControl mode
```zeekctl```
- status
- start 
- stop
### PCAP processing mode
```zeek -C -r sample.pcap```
- -r:   Reading option, read/process a pcap file.
- -C:   Ignoring checksum errors.
```
root@ubuntu$ zeek -C -r sample.pcap 

root@ubuntu$ ls -l
-rw-r--r-- 1 ubuntu ubuntu  11366 Mar 13 20:45 conn.log
-rw-r--r-- 1 ubuntu ubuntu    763 Mar 13 20:45 dhcp.log
-rw-r--r-- 1 ubuntu ubuntu   2918 Mar 13 20:45 dns.log
-rw-r--r-- 1 ubuntu ubuntu    254 Mar 13 20:45 packet_filter.log
```

## Zeek Logs
[Zeek's official documentation](https://docs.zeek.org/en/current/script-reference/log-files.html) and [Corelight log cheat sheet](https://corelight.com/about-zeek/zeek-data)
### extracting specific columns from log files
Zeek-cut	Cut specific columns from zeek logs.
```$ cat conn.log | zeek-cut uid proto id.orig_h```
```
root@ubuntu$ cat conn.log | zeek-cut uid proto id.orig_h id.orig_p id.resp_h id.resp_p 
CTMFXm1AcIsSnq2Ric	udp	192.168.121.2	51153	192.168.120.22	53
CLsSsA3HLB2N6uJwW	udp	192.168.121.10	50080	192.168.120.10	514
```
### Processing Zeek Logs
| Category | Command Purpose and Usage |
|----------|---------------------------|
| Basics | View the command history:```$ history``` 
||Execute the 10th command in history:```$ !10``` |
||Execute the previous command:```$ !!```|
| Read File |Read sample.txt file:```$ cat sample.txt```|
||Read the first 10 lines of the file:```$ head sample.txt```|
||Read the last 10 lines of the file:```$ tail sample.txt```|
| Find&Filter |Cut the 1st field:```$ cat test.txt \| cut -f 1```|
||Cut the 1st column:```$ cat test.txt \| cut -c1```|
||Filter specific keywords:```$ cat test.txt \| grep 'keywords'```|
||Sort outputs alphabetically:```$ cat test.txt \| sort```|
||Sort outputs numerically:```$ cat test.txt \| sort -n```|
||Eliminate duplicate lines:```$ cat test.txt \| uniq```|
||Count line numbers:```$ cat test.txt \| wc -l```|
||Show line numbers```$ cat test.txt \| nl```|
| Advanced |Print line 11:```$ cat test.txt \| sed -n '11p'```|
||Print lines between 10-15:```$ cat test.txt \| sed -n '10,15p'```|
||Print lines below 11:```$ cat test.txt \| awk 'NR < 11 {print $0}'```|
||Print line 11:```$ cat test.txt \| awk 'NR == 11 {print $0}'```|
| SpecialFilter |specific fields of Zeek logs:```$ cat signatures.log \| zeek-cut uid src_addr dst_addr```|

| Use Case | Description |
|----------|-------------|
|`awk '$9 >= 400' apache.log`|`$9` field requiring it to be greater than or equal to `400`.|
|```sort \| uniq```|Remove duplicate values.|
|```sort \| uniq -c```| Remove duplicates and count the number of occurrences for each value.|
|```sort \| uniq \| nl```| Nmbering. |
|```wc -l```|Count lines|
|```sort -nr```|Sort values numerically and recursively.|
|```rev```|Reverse string characters.|
|```cut -f 1```|Cut field 1.|
|```cut -d '.' -f 1-2```|Split the string on every dot and print keep the first two fields.|
|```grep -v 'test'```|Display lines that  don't match the "test" string.|
|```grep -v -e 'test1' -e 'test2'```|Display lines that don't match one or both "test1" and "test2" strings.|
|`grep -c "admin" apache.log`|Like the `uniq -c` command, count the entries matching the search criteria|
|`grep -n "admin" apache.log`|know which line number in the log file|
|```file```|View file information.|
|```grep -rin Testvalue1 * \| column -t \| less -S```|Search the "Testvalue1" string everywhere, organise column spaces and view the output with less.|
|```cat [domain list].txt \| rev \| cut -d '.' -f 1-2 \| rev```|Pick and Display unique domains from a large list.|

## Zeek Signatures
Run Zeek with signature file
```zeek -C -r sample.pcap -s sample.sig```  
Zeek signatures use the ".sig" extension.
- -C: Ignore checksum errors.
- -r: Read pcap file.
- -s: Use signature file. 

## Zeek Scripts
### Running Zeek with Scripts
```zeek -C -r smallFlows.pcap dhcp-hostname.zeek```  
```
$ zeek -C -r smallFlows.pcap dhcp-hostname.zeek 
student01-PC
vinlap01
```
Scripts are located in a specific path.  
```/opt/zeek/share/zeek/base``` :default, and these are not intended to be modified.  
```/opt/zeek/share/zeek/site``` :User-generated or modified scripts  
```/opt/zeek/share/zeek/policy```   :Policy scripts  
```/opt/zeek/share/zeek/site/local.zeek```  :Like Snort, to automatically load/use a script  

### Run Zeek with signature and script
```zeek -C -r ftp.pcap -s ftp-admin.sig 201.zeek```

### Load Local Scripts
```zeek -C -r ftp.pcap local```

### Load Frameworks
#### File Framework
- Hashes  
```zeek -C -r case1.pcap hash-demo.zeek```
```
$ cat hash-demo.zeek 
# Enable MD5, SHA1 and SHA256 hashing for all files.
@load /opt/zeek/share/zeek/policy/frameworks/files/hash-all-files.zeek

$ cat files.log | zeek-cut md5 sha1 sha256
cd5a4d3fdd5bffc16bf959ef75cf37bc	33bf88d5b82df3723d5863c7d23445e345828904	6137f8db2192e638e13610f75e73b9247c05f4706f0afd1fdb132d86de6b4012
b5243ec1df7d1d5304189e7db2744128	a66bd2557016377dfb95a87c21180e52b23d2e4e	f808229aa516ba134889f81cd699b8d246d46d796b55e13bee87435889a054fb
cc28e40b46237ab6d5282199ef78c464	0d5c820002cf93384016bd4a2628dcc5101211f4	749e161661290e8a2d190b1a66469744127bc25bf46e5d0c6f2e835f4b92db18
```
- Extract Files  
```zeek -C -r case1.pcap /opt/zeek/share/zeek/policy/frameworks/files/extract-all-files.zeek```
```
$ zeek -C -r case1.pcap /opt/zeek/share/zeek/policy/frameworks/files/extract-all-files.zeek

$ ls
101.zeek  102.zeek  103.zeek  case1.pcap  clear-logs.sh  conn.log  dhcp.log  dns.log  extract_files  files.log  ftp.pcap  http.log  packet_filter.log  pe.log

$ ls extract_files | nl
     1	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja
     2	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3
     3	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl

$ cd extract_files

$ file *| nl
     1	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja:  ASCII text, with no line terminators
     2	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3:  Composite Document File V2 Document, Little Endian, Os: Windows, Version 6.3, Code page: 1252, Template: Normal.dotm, Last Saved By: Administrator, Revision Number: 2, Name of Creating Application: Microsoft Office Word, Create Time/Date: Thu Jun 27 18:24:00 2019, Last Saved Time/Date: Thu Jun 27 18:24:00 2019, Number of Pages: 1, Number of Words: 0, Number of Characters: 1, Security: 0
     3	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl: PE32 executable (GUI) Intel 80386, for MS Windows

$ cat files.log | zeek-cut fuid conn_uids tx_hosts rx_hosts mime_type extracted | nl
     1	Fpgan59p6uvNzLFja	CaeNgL1QzYGxxZPwpk	23.63.254.163	10.6.27.102	text/plain	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja
     2	FB5o2Hcauv7vpQ8y3	CCwdoX1SU0fF3BGBCe	107.180.50.162	10.6.27.102	application/msword	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3
     3	FOghls3WpIjKpvXaEl	CZruIO2cqspVhLuAO9	107.180.50.162	10.6.27.102	application/x-dosexec	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl

$ grep -rin CZruIO2cqspVhLuAO9 * | column -t | nl | less -S
#NOTE: The full output is not shown here!. Redo the same actions in the attached VM!
     1	conn.log:43:1561667898.852600   CZruIO2cqspVhLuAO9  10.6.27.102     49162        107.180.50.162      80    tcp  http        
     2	files.log:11:1561667899.060086  FOghls3WpIjKpvXaEl  107.180.50.162  10.6.27.102  CZruIO2cqspVhLuAO9  HTTP  0    EXTRACT,PE  
     3	http.log:11:1561667898.911759   CZruIO2cqspVhLuAO9  10.6.27.102     49162        107.180.50.162      80    1    GET    
```
#### Notice Framework
- Intelligence  
Intelligence source location: ```/opt/zeek/intel/zeek_intel.txt```  
```zeek -C -r case1.pcap intelligence-demo.zeek```
```
$ cat /opt/zeek/intel/zeek_intel.txt 
#fields	indicator	indicator_type	meta.source	meta.desc
smart-fax.com	Intel::DOMAIN	zeek-intel-test	Zeek-Intelligence-Framework-Test

$ cat intelligence-demo.zeek 
# Load intelligence framework!
@load policy/frameworks/intel/seen
@load policy/frameworks/intel/do_notice
redef Intel::read_files += { "/opt/zeek/intel/zeek_intel.txt" }; 

$ zeek -C -r case1.pcap intelligence-demo.zeek 

$ cat intel.log | zeek-cut uid id.orig_h id.resp_h seen.indicator matched
CZ1jLe2nHENdGQX377	10.6.27.102	10.6.27.1	smart-fax.com	Intel::DOMAIN	
C044Ot1OxBt8qCk7f2	10.6.27.102	107.180.50.162	smart-fax.com	Intel::DOMAIN 
```

### Package Manager
Zeek Package Manager is installed with Zeek and available with the ```zkg``` command.  
| Command	| Description |
|---------|-------------|
|```zkg install package_path```|Install a package. Example (zkg install zeek/j-gras/zeek-af_packet-plugin).|
|```zkg install git_url```|Install package. Example (zkg install https://github.com/corelight/ztest).|
|```zkg list```|List installed package.|
|```zkg remove```|Remove installed package.|
|```zkg refresh```|Check version updates for installed packages.|
|```zkg upgrade```|Update installed packages.|

#### Packages
- Cleartext Submission of Password
```
$ zkg install zeek/cybera/zeek-sniffpass
$ zkg list
zeek/cybera/zeek-sniffpass (installed: master) - Sniffpass will alert on cleartext passwords discovered in HTTP POST requests

### Calling with script
$ zeek -Cr http.pcap sniff-demo.zeek 

### View script contents
$ cat sniff-demo.zeek 
@load /opt/zeek/share/zeek/site/zeek-sniffpass

### Calling from path
$ zeek -Cr http.pcap /opt/zeek/share/zeek/site/zeek-sniffpass

### Calling with package name
$ zeek -Cr http.pcap zeek-sniffpass 
```

- Geolocation Data
```
$ zeek -Cr case1.pcap geoip-conn

$ cat conn.log | zeek-cut uid id.orig_h id.resp_h geo.orig.country_code geo.orig.region geo.orig.city geo.orig.latitude geo.orig.longitude geo.resp.country_code geo.resp.region geo.resp.city                                                  
Cbk46G2zXi2i73FOU6	10.6.27.102	23.63.254.163	-	-	-	-	-	US	CA	Los Angeles
```

