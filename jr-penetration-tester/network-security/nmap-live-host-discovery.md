# 📌 Nmap Live Host Discovery

## Introduction

**Nmap** is an industry-standard tool for mapping networks, identifying live hosts, and discovering running services. Nmap’s scripting engine can further extend its functionality, from fingerprinting services to exploiting vulnerabilities. A Nmap scan usually goes through the steps shown in the figure below, although many are optional and depend on the command-line arguments you provide.

- Enumerate Targets
- Discover live hosts
- Reverse-DNS lookup
- Scan ports
- Detect versions
- Detect OS
- Traceroute
- Scripts
- Write Output


## Understanding Hosts Discovery through TCP/IP Layer

- ARP from Link Layer
- ICMP from the Network Layer
- TCP from the Transport Layer
- UDP from the Transport Layer

|--| ISO/OSI | TCP/IP |--|
|--|--|--|--|
|7| Application Layer | Application Layer | HTTP, HTTPS, SMTP, POP3, IMAP, SSH, FTP, SNMP, Telnet, RDP, ...
|6| Presentation Layer | Application Layer | HTTP, HTTPS, SMTP, POP3, IMAP, SSH, FTP, SNMP, Telnet, RDP, ...
|5| Session Layer | Application Layer | HTTP, HTTPS, SMTP, POP3, IMAP, SSH, FTP, SNMP, Telnet, RDP, ...
|4| Transport Layer | Transport Layer | TCP, UDP |
|3| Network Layer | Network Layer | IPV4, IPV6, ICMP, IPsec
|2| Data Link Layer | Link Layer | ARP, Ethernet (802.3), WiFi( 802.11), DSL, Bluetooth, ...
|1| Physical Layer | Link Layer | ARP, Ethernet (802.3), WiFi( 802.11), DSL, Bluetooth, ...

ICMP has [many types](https://www.iana.org/assignments/icmp-parameters/icmp-parameters.xhtml). ICMP ping uses Type 8 (Echo) and Type 0 (Echo Reply).

## Enumerating Targets

## Nmap Host Discovery Using ARP
nmap -PR -sn Target_IP

## Nmap Host Discovery Using ICMP
ICMP Echo to discover live hosts:
nmap -PE -sn Target_IP

ICMP Timestamp to discover live hosts:
nmap -PP -sn Target_IP

ICMP Address Mark to discover live hosts:
nmap -PM -sn Target_IP

## Nmap Host Discovery Using TCP and UDP

TCP SYN Ping:
nmap -PS -sn Target_IP

TCP ACK Ping:
nmap -PA -sn Target_IP

UDP Ping:
nmap -PU -sn Target_IP

Masscan
- masscan 10.200.6.0/24 -p443
- masscan 10.200.6.0/24 -p80,443
- masscan 10.200.6.0/24 -p22-25

## Using Reverse-DNS Lookup

Reverse DNS (rDNS) is the process of resolving an IP address to a hostname (the opposite of normal DNS). Instead of asking “What is the IP of example.com?”, you ask “What domain name is assigned to 10.200.6.15?”

add `-r`

## Summary

| Scan Type |	Example Command |
|--|--|
| ARP Scan |	sudo nmap -PR -sn 10.200.6.0/24 |
| ICMP Echo Scan |	sudo nmap -PE -sn 10.200.6.0/24 |
| ICMP Timestamp Scan |	sudo nmap -PP -sn 10.200.6.0/24 |
| ICMP Address Mask Scan |	sudo nmap -PM -sn 10.200.6.0/24 |
| TCP SYN Ping Scan |	sudo nmap -PS22,80,443 -sn 10.200.6.0/30 |
| TCP ACK Ping Scan |	sudo nmap -PA22,80,443 -sn 10.200.6.0/30 |
| UDP Ping Scan |	sudo nmap -PU53,161,162 -sn 10.200.6.0/30 |

| Option |	Purpose |
|--|--|
| -n |	no DNS lookup |
| -R |	reverse-DNS lookup for all hosts |
| -sn |	host discovery only |