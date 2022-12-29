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
- -r    Reading option, read/process a pcap file.
- -C    Ignoring checksum errors.

## Zeek Logs
[Zeek's official documentation](https://docs.zeek.org/en/current/script-reference/log-files.html) and [Corelight log cheat sheet](https://corelight.com/about-zeek/zeek-data)
### extracting specific columns from log files
Zeek-cut	Cut specific columns from zeek logs.
```$ cat conn.log | zeek-cut uid proto id.orig_h```





