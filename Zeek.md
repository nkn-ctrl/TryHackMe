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




