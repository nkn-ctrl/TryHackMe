# Nmap
<p align="center">
<img src="https://user-images.githubusercontent.com/73976100/221752594-9bc1f554-c724-4acd-985a-3ee666c30563.png" width="300">
</p>

## Nmap Live Host Discovery
1. ARP scan: This scan uses ARP requests to discover live hosts
2. ICMP scan: This scan uses ICMP requests to identify live hosts
3. TCP/UDP ping scan: This scan sends packets to TCP ports and UDP ports to determine live hosts.  

### Enumerating Targets
- list: `MACHINE_IP scanme.nmap.org example.com` will scan 3 IP addresses.
- range: `10.11.12.15-20` will scan 6 IP addresses: `10.11.12.15`, `10.11.12.16`,… and `10.11.12.20`.
- subnet: `MACHINE_IP/30` will scan 4 IP addresses.
- list file: `nmap -iL list_of_hosts.txt`  
<br>

`nmap -sL TARGETS`: This option will give you a detailed list of the hosts that Nmap will scan without scanning them; however, Nmap will attempt a reverse-DNS resolution on all the targets to obtain their names.  (If you don’t want Nmap to the DNS server, you can add -n.)  

### Discovering Live Hosts

