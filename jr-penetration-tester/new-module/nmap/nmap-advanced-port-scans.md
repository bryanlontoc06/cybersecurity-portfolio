# 📌 NMAP Advanced Port Sans

## Introduction

Advanced Port Scan

- Types Null Scan - Send a TCP packet with no flags set to infer open ports from the lack of a response.
- FIN Scan - Send a TCP packet with only the FIN flag to probe ports without initiating a connection.
- Xmas Scan - Set FIN, PSH, and URG flags simultaneously to probe ports behind stateless firewalls.
- Maimon Scan - Set FIN and ACK flags together to exploit a behaviour found in certain BSD-derived systems.
- ACK Scan - Send a packet with only the ACK flag to map firewall rules rather than discover open ports.
- Window Scan - Examine the TCP Window field in RST responses to differentiate open from closed ports.
- Custom Scan - Use --scanflags to craft your own TCP flag combinations for tailored probing.

Evasion and Spoofing Techniques

- Spoofing IP - Forge the source IP address using -S so scan traffic appears to originate from a different host.
- Spoofing MAC - Forge the source MAC address using --spoof-mac when on the same local network as the target.
- Decoy Scan - Mix your real IP among multiple decoy addresses using -D to obscure the true scan source.
- Fragmented Packets - Split packets into smaller IP fragments using -f or -ff to evade firewalls and IDS.
- Idle/Zombie Scan - Use an idle third-party host with -sI to scan a target without revealing your own IP address.

Moreover, we will explore options for getting more verbose details from Nmap, including --reason, -v, -vv, and debugging flags.

## TCP Null Scan, FIN Scan, and Xmas Scan

### Null Scan

### 📊 Firewall Detection Cheat Sheet (Nmap SYN vs. ACK Scans)

When performing network reconnaissance, comparing the results of a **TCP SYN Scan (-sS)** and a **TCP ACK Scan (-sA)** against the same port is the most effective way to map out the presence, type, and behavioral rules of a remote firewall.

Below is the matrix of the four possible outcome combinations, what they diagnose, and the recommended tactical next steps.

| SYN Scan Result | ACK Scan Result | Technical Diagnosis (The Verdict) | Tactical Next Steps / Evasion Strategy |
|--|--|--|--|
| 🟢 Open / Closed | ⚪ Unfiltered | **No Firewall Present.** <br/> The network path is completely unobstructed, and you are communicating directly with the target host's operating system. | This is the ideal scenario. Immediately proceed to service version detection (-sV), OS fingerprinting (-O), or credential brute-forcing using tools like Hydra or Medusa. |
| 🟡 Filtered | ⚪ Unfiltered | **Stateless Firewall Detected.** <br/> The firewall is basic and "forgetful." It only blocks inbound SYN packets (attempts to initiate new connections) but automatically allows other flags to pass through. | Bypass the firewall using stealth scans that do not contain the SYN flag. Deploy a Null Scan (-sN), FIN Scan (-sF), or Xmas Scan (-sX) to probe the ports behind the filter. |
| 🟡 Filtered | 🟡 Filtered | **Stateful Firewall / Strict ACL Detected.** <br/> The firewall maintains a state table (connection logbook). It strictly tracks active sessions and drops any unsolicited or non-compliant flags. | Traditional flag manipulation will fail here. Attempt advanced evasion techniques such as Packet Fragmentation (-f or -ff) to split headers, use a Decoy Scan (-D) to mask your IP, or pivot to scanning for UDP services (-sU). |
| 🟢 Open / Closed | 🟡 Filtered | **Non-Standard Firewall Policy.** <br > An uncommon configuration where the firewall permits new inbound connection requests (SYN) but explicitly drops or filters unexpected standalone ACK packets. | Rare behavior, but it does not restrict your access. You can safely ignore the ACK anomalies and continue scanning and exploiting the target using standard TCP methods. |

## TCP Maimon Scan

2 Flags where returned
FIN & ACK

## TCP ACK, Window, and Custom Scan

This behaviour occurs because a TCP packet with the ACK flag set should be sent only in response to a received TCP packet to acknowledge receipt of data, unlike in our case. Hence, this scan won’t tell us whether the target port is open in a simple setup.


## Spoofing and Decoys

```nmap -S SPOOFED_IP MACHINE_IP```

## Fragmented Packets

### Firewall

A firewall is a piece of software or hardware that either permits or blocks packets.

### IDS

An Intrusion Detection System (IDS) inspects network packets for select behavioural patterns or specific content signatures.

## Fragment Packets

- -f - each bytes (8 bytes)
```sudo nmap -sS -p80 -f MACHINE_IP```

## Idle/Zombie Scan

```nmap -sI ZOMBIE_IP MACHINE_IP```

## Getting More Details

- -- reason - gives us the explicit reason why Nmap concluded that the system is up or a particular port is open
- -v for verbose output
- -vv for even more verbosity.
- -d  for debugging details
- -dd for even more details

## Summary

| Port Scan Type |	Example Command |
|--|--|
| TCP Null Scan |	sudo nmap -sN 10.48.175.182
| TCP FIN Scan |	sudo nmap -sF 10.48.175.182
| TCP Xmas Scan |	sudo nmap -sX 10.48.175.182
| TCP Maimon Scan |	sudo nmap -sM 10.48.175.182
| TCP ACK Scan	| sudo nmap -sA 10.48.175.182
| TCP Window Scan |	sudo nmap -sW 10.48.175.182
| Custom TCP Scan |	sudo nmap --scanflags URGACKPSHRSTSYNFIN 10.48.175.182
| Spoofed Source IP |	sudo nmap -S SPOOFED_IP 10.48.175.182
| Spoofed MAC Address |	--spoof-mac SPOOFED_MAC
| Decoy Scan |	nmap -D DECOY_IP,ME 10.48.175.182
| Idle (Zombie) Scan |	sudo nmap -sI ZOMBIE_IP 10.48.175.182
| Fragment IP data into 8 bytes	| -f
| Fragment IP data into 16 bytes |	-ff

| Option |	Purpose |
|--|--|
| --source-port PORT_NUM |	Specify source port number
| --data-length NUM	Append | random data to reach the given length

| Option |	Purpose |
|--|--|
| --reason |	explains how Nmap made its conclusion
| -v |	verbose
| -vv |	very verbose
| -d |	debugging
| -dd |	more details for debugging
