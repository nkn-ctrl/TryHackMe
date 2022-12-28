# Zeek

## The default log path
``` /opt/zeek/logs/ ```
## Check the version of the Zeek instance
``` zeek -v ```
## ZeekControl mode
```
zeekctl status
zeekctl start 
zeekctl stop 
```
## PCAP processing mode
``` zeek -C -r sample.pcap ```
- -r    Reading option, read/process a pcap file.
- -C    Ignoring checksum errors.


