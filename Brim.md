# [Brim](https://www.brimdata.io/)

| Purpose |	Syntax | Example Query |
|---------|--------|---------------|
| Basic search |You can search any string and numeric value.|Find logs containing an IP address or any value.<br>```10.0.0.1```|
Logical operators 	Or, And, Not. 	
Find logs contain three digits of an IP AND NTP keyword.
192 and NTP
Filter values	"field name" == "value"	
Filter source IP.
id.orig_h==192.168.121.40
List specific log file contents
_path=="log name"
List the contents of the conn log file.
_path=="conn"
Count field values 	count () by "field"	
Count the number of the available log files.
count () by _path
Sort findings	sort	
Count the number of the available log files and sort recursively.
count () by _path | sort -r
Cut specific field from a log file	_path=="conn" | cut "field name"	
Cut the source IP, destination port and destination IP addresses from the conn log file.
_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h
List unique values	uniq	
Show the unique network connections. 

_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq

