# 📌 NMAP Basic Port Sans


## TCP and UDP Ports

Nmap considers the following six states:

1. An open port indicates that a service is listening on the specified port.
2. A closed port indicates that no service is listening on the specified port, although the port is accessible. By accessible, we mean that it is reachable and is not blocked by a firewall or other security appliances/programs.
3. Filtered means that Nmap cannot determine whether the port is open or closed because it is not accessible. This state is usually due to a firewall preventing Nmap from reaching that port. Nmap’s packets may be blocked from reaching the port; alternatively, responses may be blocked from reaching Nmap’s host.
4. Unfiltered means that Nmap cannot determine whether the port is open or closed, even though the port is accessible. This state is encountered when using an ACK scan -sA.
5. Open|Filtered: This means that Nmap cannot determine whether the port is open or filtered.
6. Closed|Filtered: This means that Nmap cannot decide whether a port is closed or filtered.

## TCP Flags

1. URG: Urgent flag indicates that the urgent pointer field is significant. The urgent pointer indicates that the incoming data is urgent, and that a TCP segment with the URG flag set is processed immediately, without waiting for previously sent TCP segments.
2. ACK: Acknowledgement flag indicates that the acknowledgement number is significant. It is used to acknowledge the receipt of a TCP segment.
3. PSH: Push flag asking TCP to pass the data to the application promptly.
4. RST: The reset flag is used to reset the connection. Another device, such as a firewall, might send it to tear a TCP connection. This flag is also used when data is sent to a host, and there is no service on the receiving end to answer.
5. SYN: The synchronise flag is used to initiate a TCP 3-way handshake and synchronise sequence numbers with the other host. The sequence number should be set randomly during TCP connection establishment.
6. FIN: The sender has no more data to send.

-sT
-sS
-sU
nmap -sU --top-ports 10 MACHINE_IP

## Summary

This room covered three types of scans.

| Port Scan Type |	Example Command |
|--|--|
| TCP Connect Scan |	nmap -sT MACHINE_IP |
| TCP SYN Scan |	sudo nmap -sS MACHINE_IP |
| UDP Scan |	sudo nmap -sU MACHINE_IP |

These scan types should get you started discovering running TCP and UDP services on a target host.

| Option |	Purpose |
|--|--|
| -p- |	all ports |
| -p1-1023 |	scan ports 1 to 1023 |
| -F |	100 most common ports |
| -r |	scan ports in consecutive order |
| -T<0-5> |	-T0 being the slowest and T5 the fastest |
| --max-rate 50 |	rate <= 50 packets/sec |
| --min-rate 15 |	rate >= 15 packets/sec |
| --min-parallelism 100 |	at least 100 probes in parallel |