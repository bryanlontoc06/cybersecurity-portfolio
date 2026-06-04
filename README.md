# Cybersecurity Portfolio

This repository contains my foundational cybersecurity learning.

## 📁 Contents

- CyberSecurity101
  - [Cryptography](cyber-security101/cryptography/)
  - [Defensive Security](cyber-security101/defensive-security/)
  - [Defensive Security Tooling](cyber-security101/defensive-security-tooling/)
  - [Exploitation Basics](cyber-security101/exploitation-basics/)
  - [Linux Basics](cyber-security101/linux-basics/)
  - [Networking](cyber-security101/networking/)
  - [Offensive Security Tooling](cyber-security101/offensive-security-tooling/)
  - [OWASP Top 10 (2025)](cyber-security101/owasp-top-10-2025/)
  - [Security Solutions](cyber-security101/security-solutions/)
  - [SQLMap: The Basics](cyber-security101/sqlmap-the-basics)
  - [Web Hacking](cyber-security101/web-hacking/)
  - [Windows Basics](cyber-security101/windows-basics/)

- Introduction to Web Hacking
  - [Content Discovery](web-fundamentals/introduction-to-web-hacking/content-discovery.md)
  - [Authentication Bypass](web-fundamentals/introduction-to-web-hacking/authentication-bypass.md)
  - [IDOR](web-fundamentals/introduction-to-web-hacking/idor.md)
  - [File Inclusion](web-fundamentals/introduction-to-web-hacking/file-inclusion.md)
  - [Intro to SSRF ](web-fundamentals/introduction-to-web-hacking/intro-to-ssrf.md)
  - [Intro to XSS](web-fundamentals/introduction-to-web-hacking/intro-to-xss.md)
  - [Race Conditions](web-fundamentals/introduction-to-web-hacking/race-conditions.md)
  - [Command Injection](web-fundamentals/introduction-to-web-hacking/command-injection.md)
  - [SQL Injection](web-fundamentals/introduction-to-web-hacking/sql-injection.md)

- JR Penetration Tester
  - [Penetration Testing Foundations](jr-penetration-tester/new-module/penetration-testing-foundations/)
  - [Network Reconnaissance](jr-penetration-tester/new-module/network-reconnaissance/)

## 📌 Status

Work in progress

## ⚠️ Note

This portfolio is continuously evolving as I progress toward real-world cybersecurity and security engineering roles such as Application Security, DevSecOps, and Cloud Security.

## Hands-on Demo
*click to watch*
### [Meterpreter Payload Exploitation Demo](https://www.youtube.com/watch?v=IEp0_TeC64Y)
### [Reflected XSS Demonstration Using Burp Suite: Bypassing Client-Side Validation](https://www.youtube.com/watch?v=UzijItCYh2c)
### [Authentication Security Testing: Burp Suite Interception and Hydra Brute Force Simulation](https://www.youtube.com/watch?v=w30ETtKzKcY)
### [SQL Injection Attack Simulation: Database Enumeration & Data Exfiltration](https://www.youtube.com/watch?v=ET-OHsv9D3U)
### [LFI to RCE Exploitation Demo](https://www.youtube.com/watch?v=5KqkAFk9TUI)
### [Exploiting Race Conditions Simulation](https://www.youtube.com/watch?v=60Hc53sekaY)
### [Bypass Server-side Filtering File Extensions: Magic Number Simulation](https://www.youtube.com/watch?v=bZaOKmchk0E)
### [Bypass Server-side Filtering File Extensions: Challenge Simulation](https://www.youtube.com/watch?v=WUFBU101Wpg)
### [Pickle Rick](https://www.youtube.com/watch?v=Isx37gbB830)

##  🛠️ Tools

🔍 1. Reconnaissance & Reputation

Used to verify the safety or malicious nature of files, IP addresses, or websites.

- [VirusTotal](virustotal.com) – Analyzes files and URLs using over 70 antivirus engines to detect malware.
- [AbuseIPDB](abuseipdb.com) – A central database to check if an IP address has been reported for hacking or malicious activity.
- [IPinfo](ipinfo.io) – Provides geographical location, ASN, and ISP details for a specific IP address.

🐚 2. Shell Generators & Payloads

Resources used to generate the scripts or commands needed to gain "Initial Access" to a server.

- [RevShells](revshells.com) – An automated generator for various types of Reverse Shells (Bash, Python, PHP, etc.).
- [PayloadsAllTheThings](github.com/swisskyrepo/PayloadsAllTheThings) – A massive repository of bypass techniques and attack payloads for almost every web vulnerability.

🛠️ 3. Execution & Listeners

Tools you run on your own machine (AttackBox) to host files or wait for an incoming connection.

- Netcat (nc) – Known as the "Swiss Army Knife" of networking. Primarily used as a listener to catch reverse shells. (sample: nc -lvnp 1234)
- Python HTTP Server – A built-in module to quickly host files for RFI attacks. (Command: python3 -m http.server 80).
- [CyberChef](gchq.github.io/CyberChef) – A web app for encoding, decoding, and analyzing data (e.g., converting Base64, Hex, or URL encoding).

📚 4. Privilege Escalation & Post-Exploitation

Tools used once you have gained initial access to elevate your permissions to "Admin" or "Root."

- [GTFOBins](gtfobins.github.io) – A curated list of Unix binaries that can be exploited to bypass local security restrictions.
- [LinPEAS](github.com/peass-ng/PEASS-ng) – An automated script that searches for privilege escalation paths in Linux environments.

---

[DNS Dumpster](https://dnsdumpster.com/)
[Shodan IO](https://www.shodan.io/)
[Censys IO](https://search.censys.io/)
