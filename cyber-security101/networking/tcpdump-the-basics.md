# 📌 Tcpdump: The Basics
ssh user@10.49.154.85

### Practice
## Filtering Expressions
How many packets in traffic.pcap use the ICMP protocol?
user@ip-10-49-154-85:~$ tcpdump -r traffic.pcap icmp | wc -l

What is the IP address of the host that asked for the MAC address of 192.168.124.137?
user@ip-10-49-154-85:~$ tcpdump -r traffic.pcap arp and host 192.168.124.137


What hostname (subdomain) appears in the first DNS query?
user@ip-10-49-154-85:~$ tcpdump -r traffic.pcap -nn port 53

## Advance Filtering

How many packets have only the TCP Reset (RST) flag set?
user@ip-10-49-154-85:~$ tcpdump -r traffic.pcap "tcp[tcpflags] == tcp-rst" | wc -l

What is the IP address of the host that sent packets larger than 15000 bytes?
user@ip-10-49-154-85:~$ tcpdump -r traffic.pcap -nn 'greater 15000' | head -n 1

## Displaying Packets

What is the MAC address of the host that sent an ARP request?
user@ip-10-49-154-85:~$ tcpdump -e -r traffic.pcap arp
