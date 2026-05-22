# 📌 Active Reconnaissance

## Introduction

**Active reconnaissance** is the process of directly interacting with a target system or network to gather information about it. Passive reconnaissance collects data from public sources without sending any traffic to the target. Active reconnaissance, by contrast, requires transmitting packets, making connections, and probing services. The distinction matters because active techniques leave traces in the form of log entries, IDS alerts, WAF blocks, and honeypot triggers.

## Ping

Quick Reference

|Result|	Most likely meaning|	Next step|
|--|--|--|
|Fast replies, low or no packet loss	|Target is online and allows ICMP|	Proceed to port scanning|
|"Destination Host Unreachable"|	Target is down or no route exists|	Check if machine is powered on|
100% packet loss with no error message|	ICMP is filtered or blocked|	Try TCP/UDP host discovery with Nmap|
High latency or heavy loss	|Network congestion, long distance, or filtering|	Investigate the path with traceroute|

## Traceroute

The traceroute command traces the route that packets take from your system to a target host. Its purpose is to discover the IP addresses of the routers (hops) along the path and to determine how many of them sit between you and the destination. This information is useful for understanding network topology, identifying where filtering or latency occurs, and mapping infrastructure.

## Telnet

The TELNET (Teletype Network) protocol was developed in 1969 to communicate with a remote system via a command-line interface. The telnet command uses this protocol for remote administration, with a default port of 23. From a security perspective, telnet sends all data in cleartext, including usernames and passwords. This makes it trivial for anyone with access to the communication channel to intercept login credentials. The secure alternative is SSH (Secure Shell), which encrypts all traffic and is the standard for remote CLI access today.

## Netcat

Netcat (or simply nc) is a versatile networking utility that supports both TCP and UDP protocols. It can function as a client that connects to a listening port, or as a server that listens on a port of your choice. This dual capability makes it useful for banner grabbing, port probing, simple file transfers, and basic client-server communication. Modern versions such as ncat from the Nmap project also support IPv6 and SSL encryption, making it more versatile than legacy tools like telnet

----

ping confirms whether a target is reachable and provides TTL-based clues about its operating system.

traceroute maps the network path between you and the target, revealing intermediate routers and potential filtering points.

telnet and netcat connect to individual ports to grab banners and identify running services along with their versions.

Quick Reference

|Command|	Example|
|--|--|
|ping|	ping -c 10 10.49.183.38 on Linux or macOS|
|ping|	ping -n 10 10.49.183.38 on Windows|
|ping (IPv6)|	ping -6 MACHINE_IPV6 or ping6 MACHINE_IPV6|
|traceroute|	traceroute 10.49.183.38 on Linux or macOS|
|tracert|	tracert 10.49.183.38 on Windows|
|traceroute (IPv6)|	traceroute -6 MACHINE_IPV6 or traceroute6 MACHINE_IPV6|
|mtr|	mtr 10.49.183.38 for real-time path monitoring|
|telnet (legacy)|	telnet 10.49.183.38 PORT_NUMBER|
|netcat as client|	nc 10.49.183.38 PORT_NUMBER|
|netcat as server|	nc -lvnp PORT_NUMBER|
|netcat (IPv6)|	nc -6 MACHINE_IPV6 PORT_NUMBER|
|curl for HTTP banner|	curl -I http://10.49.183.38 or curl -Ihttps://10.49.183.38|