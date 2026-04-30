# 📌 Nmap: The Basics

In Nmap:
- sV = Service Version Detection
- sN = TCP Null Scan
- sL = List Scan

#### `-sV` — Service Version Detection

Detects the service and version running on open ports.

It tells you:
what service is running
what version it is

Example:
`80/tcp open  http  Apache httpd 2.4.41`

Meaning:
- Port 80 is open
- It runs HTTP
- Apache version is 2.4.41

#### -sN — TCP Null Scan

Sends TCP packets with no flags set to determine whether ports are open or closed.

It is a stealth scan used to infer port states:

RST response → closed
No response → open or filtered

It does not identify services.

#### -sL — List Scan

Lists the target hosts without scanning them.
It only shows which IPs/hosts would be scanned.

It does not:
- scan ports
- check if hosts are up

Comparison Table:
|Option|Meaning|Purpose|
|-|-|-|
|-sV|Service Version Detection|Detect services and versions|
|-sN|TCP Null Scan|Stealth port scan|
|-sL|List Scan	List|targets only|

Simple summary:
-sV identifies services, -sN stealthily checks ports, and -sL only lists the targets.

#### -sT — Connect Scan

It is a scan type where Nmap completes the full TCP connection to check whether a port is open.

Simple explanation:
When Nmap checks a port using -sT, it performs the normal TCP three-way handshake:
- SYN → “Can I connect?”
- SYN-ACK → “Yes”
- ACK → “Connection established”

If the handshake succeeds:
The port is open

Simple analogy:
Imagine knocking on a door:
You knock
Someone answers
You say `hello`

If someone answers:
The door is open

That’s how -sT works—it fully connects.

Example:
- nmap -sT 192.168.1.10

Possible output:
- 80/tcp open http
- 22/tcp open ssh

This means:
- Port 80 is open
- Port 22 is open

#### -sS — Connect Scan
- sends only part of the handshake
- more stealthy
- does not fully connect

------
Techniques to Remember Networking Concepts Faster

The key idea is:
Don’t try to memorize everything at once — understand patterns first.

When you're learning things like:
- ports
- protocols
- Nmap flags
- Wireshark terms

it can feel overwhelming.

That’s normal.

Instead of memorizing everything immediately, use these techniques:

1. Group related concepts together

Instead of memorizing commands one by one, group them by purpose.

For example in Nmap:

Host discovery
- -sn → discover live hosts
- -sL → list targets

Port scanning
- -sT → full TCP connect
- -sS → stealth SYN scan
- -sN → null scan

Service detection
- -sV → detect service versions

This helps because:

Your brain remembers categories better than random items

2. Connect the flag letters to their meaning

Many flags have a clue in the letter:

- -sV → V = Version
- -sL → L = List
- -sT → T = TCP Connect

This makes commands easier to remember.

3. Use analogies

Create simple mental images.

Examples:

- -sT = knocking and opening the door
- -sS = checking the door without opening
- ARP = “Who owns this IP?”
- DNS = phonebook

This helps because:

Images are easier to remember than definitions

4. Learn by using

Instead of just reading:

`nmap -sV`

actually try:

`nmap -sV scanme.nmap.org`

When you see the results, your brain connects:

command + purpose + output

That creates stronger memory.

5. Make mini cheat sheets

Keep short notes like:

- -sV = service version
- -sT = TCP connect
- 53 = DNS
- 80 = HTTP
- 443 = HTTPS

Short notes are easier to review often.

6. Learn in layers

Don’t study everything at once.

First:
Learn common ports:
- 80
- 443
- 53
- 22

Then:
Learn basic scans:
- -sn
- -sT
- -sV

Later:
Learn advanced scans:
- -sN
- -sX
- packet analysis

This helps reduce overload.

7. Accept partial understanding

You do not need to master everything immediately.

It is enough to know:
"-sV checks service versions"

You don’t need the full internal mechanics right away.
Deep understanding comes with repetition.

Summary
|Option|	Explanation|
|-|-|
|-sT	|TCP connect scan – complete three-way handshake|
|-sS	|TCP SYN – only first step of the three-way handshake|
|-sU	|UDP scan|
|-F	|Fast mode – scans the 100 most common ports|
|-p[range]	|Specifies a range of port numbers – -p- scans all the ports|

### Version Detection: Extract More Information

#### Simple Nmap Cheat Sheet

#### 1. Check if the host is alive
| Use: -sn

`nmap -sn target`

Meaning:
Ping the host only, no port scan.

#### 2. Find open ports
| Use: default scan

`nmap target`

Meaning:
Scan common ports.

#### 3. Scan all ports

| Use: -p-

`nmap -p- target`

Meaning:
Scan ports 1–65535

#### 4. Detect service version

| Use: -sV

`nmap -sV target`

Meaning:
Show the service and version.

Example:
80/tcp open http Apache

#### 5. TCP Connect Scan

| Use: -sT

`nmap -sT target`

Meaning:
Full TCP connection scan.

#### 6. Scan UDP ports

| Use: -sU

`nmap -sU target`

Meaning:
Scan UDP services.

#### 7. Stealth scan

| Use: -sS

`nmap -sS target`

Meaning:
Stealthy TCP SYN scan. Half-open stealth scan.

#### 8. Fast scan

| Use: -F

`nmap -F target`

Meaning:
Scan fewer common ports faster.

#### 9. No DNS resolution

| Use: -n

`nmap -n target`

Meaning:
Skip DNS lookup for faster scanning.


Quick Memory Table
|Goal|Option|
|-|-|
|Host discovery|-sn|
|Open ports|	default|
|All ports|	-p-|
|Service version|	-sV|
|TCP connect|	-sT|
|Stealth SYN|	-sS|
|UDP scan|	-sU|
|Fast scan|	-F|
|No DNS|	-n|

#### Nmap Quick Cheat Sheet
- -sn = check host (Host?)
- -p- = scan all ports (All ports?)
- -sV = detect service version (What service?)
- -sT = full TCP connect (TCP scan?)
- -sS = stealth SYN scan (Stealth?)
- -sU = scan UDP ports (UDP?)
- -F  = fast scan (Fast?)
- -n  = no DNS lookup (No DNS?)

|Option|	Explanation|
|-|-|
|-O	|OS detection|
|-A	|OS detection, version detection, and other additions|
|-Pn	|Scan hosts that appear to be down|
