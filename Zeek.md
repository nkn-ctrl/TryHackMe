# Zeek

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
| Basics | View the command history:```ubuntu@ubuntu$ history``` 
||Execute the 10th command in history:```ubuntu@ubuntu$ !10``` |
||Execute the previous command:```ubuntu@ubuntu$ !!```|
| Read File |Read sample.txt file:```ubuntu@ubuntu$ cat sample.txt```|
||Read the first 10 lines of the file:```ubuntu@ubuntu$ head sample.txt```|
||Read the last 10 lines of the file:```ubuntu@ubuntu$ tail sample.txt```|
| Find&Filter |Cut the 1st field:```ubuntu@ubuntu$ cat test.txt \| cut -f 1```|
||Cut the 1st column:```ubuntu@ubuntu$ cat test.txt \| cut -c1```|
||Filter specific keywords:```ubuntu@ubuntu$ cat test.txt \| grep 'keywords'```|
||Sort outputs alphabetically:```ubuntu@ubuntu$ cat test.txt \| sort```|
||Sort outputs numerically:```ubuntu@ubuntu$ cat test.txt \| sort -n```|
||Eliminate duplicate lines:```ubuntu@ubuntu$ cat test.txt \| uniq```|
||Count line numbers:```ubuntu@ubuntu$ cat test.txt \| wc -l```|
||Show line numbers```ubuntu@ubuntu$ cat test.txt \| nl```|
| Advanced |Print line 11:```ubuntu@ubuntu$ cat test.txt \| sed -n '11p'```|
||Print lines between 10-15:```ubuntu@ubuntu$ cat test.txt \| sed -n '10,15p'```|
||Print lines below 11:```ubuntu@ubuntu$ cat test.txt \| awk 'NR < 11 {print $0}'```|
||Print line 11:```ubuntu@ubuntu$ cat test.txt \| awk 'NR == 11 {print $0}'```|
| SpecialFilter |specific fields of Zeek logs:```ubuntu@ubuntu$ cat signatures.log \| zeek-cut uid src_addr dst_addr```|

| Use Case | Description |
|----------|-------------|
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
|```file```|View file information.|
|```grep -rin Testvalue1 * \| column -t \| less -S```|Search the "Testvalue1" string everywhere, organise column spaces and view the output with less.|

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
ubuntu@ubuntu$ zeek -C -r smallFlows.pcap dhcp-hostname.zeek 
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
ubuntu@ubuntu$ cat hash-demo.zeek 
# Enable MD5, SHA1 and SHA256 hashing for all files.
@load /opt/zeek/share/zeek/policy/frameworks/files/hash-all-files.zeek
```
- Extract Files  
```zeek -C -r case1.pcap /opt/zeek/share/zeek/policy/frameworks/files/extract-all-files.zeek```
```
ubuntu@ubuntu$ zeek -C -r case1.pcap /opt/zeek/share/zeek/policy/frameworks/files/extract-all-files.zeek

ubuntu@ubuntu$ ls
101.zeek  102.zeek  103.zeek  case1.pcap  clear-logs.sh  conn.log  dhcp.log  dns.log  extract_files  files.log  ftp.pcap  http.log  packet_filter.log  pe.log

ubuntu@ubuntu$ ls extract_files | nl
     1	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja
     2	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3
     3	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl

ubuntu@ubuntu$ cd extract_files

ubuntu@ubuntu$ file *| nl
     1	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja:  ASCII text, with no line terminators
     2	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3:  Composite Document File V2 Document, Little Endian, Os: Windows, Version 6.3, Code page: 1252, Template: Normal.dotm, Last Saved By: Administrator, Revision Number: 2, Name of Creating Application: Microsoft Office Word, Create Time/Date: Thu Jun 27 18:24:00 2019, Last Saved Time/Date: Thu Jun 27 18:24:00 2019, Number of Pages: 1, Number of Words: 0, Number of Characters: 1, Security: 0
     3	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl: PE32 executable (GUI) Intel 80386, for MS Windows

ubuntu@ubuntu$ cat files.log | zeek-cut fuid conn_uids tx_hosts rx_hosts mime_type extracted | nl
     1	Fpgan59p6uvNzLFja	CaeNgL1QzYGxxZPwpk	23.63.254.163	10.6.27.102	text/plain	extract-1561667874.743959-HTTP-Fpgan59p6uvNzLFja
     2	FB5o2Hcauv7vpQ8y3	CCwdoX1SU0fF3BGBCe	107.180.50.162	10.6.27.102	application/msword	extract-1561667889.703239-HTTP-FB5o2Hcauv7vpQ8y3
     3	FOghls3WpIjKpvXaEl	CZruIO2cqspVhLuAO9	107.180.50.162	10.6.27.102	application/x-dosexec	extract-1561667899.060086-HTTP-FOghls3WpIjKpvXaEl

ubuntu@ubuntu$ grep -rin CZruIO2cqspVhLuAO9 * | column -t | nl | less -S
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
ubuntu@ubuntu$ cat /opt/zeek/intel/zeek_intel.txt 
#fields	indicator	indicator_type	meta.source	meta.desc
smart-fax.com	Intel::DOMAIN	zeek-intel-test	Zeek-Intelligence-Framework-Test

ubuntu@ubuntu$ cat intelligence-demo.zeek 
# Load intelligence framework!
@load policy/frameworks/intel/seen
@load policy/frameworks/intel/do_notice
redef Intel::read_files += { "/opt/zeek/intel/zeek_intel.txt" }; 

ubuntu@ubuntu$ zeek -C -r case1.pcap intelligence-demo.zeek 

ubuntu@ubuntu$ cat intel.log | zeek-cut uid id.orig_h id.resp_h seen.indicator matched
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
ubuntu@ubuntu$ zkg install zeek/cybera/zeek-sniffpass
ubuntu@ubuntu$ zkg list
zeek/cybera/zeek-sniffpass (installed: master) - Sniffpass will alert on cleartext passwords discovered in HTTP POST requests

### Calling with script
ubuntu@ubuntu$ zeek -Cr http.pcap sniff-demo.zeek 

### View script contents
ubuntu@ubuntu$ cat sniff-demo.zeek 
@load /opt/zeek/share/zeek/site/zeek-sniffpass

### Calling from path
ubuntu@ubuntu$ zeek -Cr http.pcap /opt/zeek/share/zeek/site/zeek-sniffpass

### Calling with package name
ubuntu@ubuntu$ zeek -Cr http.pcap zeek-sniffpass 
```

- Geolocation Data
```
ubuntu@ubuntu$ zeek -Cr case1.pcap geoip-conn

ubuntu@ubuntu$ cat conn.log | zeek-cut uid id.orig_h id.resp_h geo.orig.country_code geo.orig.region geo.orig.city geo.orig.latitude geo.orig.longitude geo.resp.country_code geo.resp.region geo.resp.city                                                  
Cbk46G2zXi2i73FOU6	10.6.27.102	23.63.254.163	-	-	-	-	-	US	CA	Los Angeles
```

