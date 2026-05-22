# 📌 Passive Reconnaissance

## Passive versus Active Recon

**Passive Reconnaissance**

**Passive reconnaissance** relies exclusively on publicly available information. No packets are sent to the target and no direct interaction occurs. It is analogous to observing the target territory from a safe distance using binoculars, without ever stepping onto their land.

Common passive activities include:

- Querying public DNS records from open resolvers (A, MX, TXT, etc.).
- Searching certificate transparency logs (e.g., crt.sh) for subdomains and issued certificates.
- Reviewing job postings on LinkedIn or company career pages for tech stack hints.
- Reading public news, press releases, or leaked documents on paste sites.
- Checking exposed devices via search engines like Shodan or Censys.
- Scanning public GitHub repositories for hardcoded credentials or configuration files.


**Active Reconnaissance**

**Active reconnaissance** requires direct engagement with the target. Your probes can be logged, detected, or blocked. It is analogous to walking up to the doors and windows to test locks, cameras, and alarms.

Common active activities include:

- Sending packets to discover live hosts (e.g., ICMP pings, ARP requests).
- Port scanning or service enumeration (Nmap, masscan).
- Interacting with web applications or APIs (fuzzing endpoints, directory brute-forcing).
- Social engineering attempts (phishing, vishing, pretexting phone calls).
- Physical approaches (tailgating, posing as a vendor).

## Whois

From a WHOIS response, the following details may be available (when not redacted):

Registrar: The company (e.g., Namecheap, GoDaddy) that registered the domain.
Registrant contact information: Name, organisation, address, phone, and email. However, privacy services (standard since GDPR 2018) usually replace this with "Withheld for Privacy" or similar.
Dates: Creation (registration), Updated (last change), and Expiration (renewal deadline).
Name servers: The DNS servers authoritative for the domain.
Status codes: For example, clientTransferProhibited indicates the domain is locked against unauthorised transfers.
Abuse contacts: The registrar's email and phone for reporting issues.

To query WHOIS, use the whois command-line client (faster than most web tools) or online viewers for legacy queries.

Syntax: whois DOMAIN_NAME

What to look for:

- Redirection chain (Verisign to registrar server).
- Dates: useful for estimating company age or identifying renewal phishing windows.
- Name servers: potential new targets (if in scope).
- Status: locked domains (e.g., clientTransferProhibited) are harder to hijack.

Online alternatives (if the whois command behaves unexpectedly):

- https://whois.icann.org/ (legacy WHOIS)
- https://lookup.icann.org/ (modern RDAP-focused lookup)
- https://www.whoxy.com/ (historical WHOIS snapshots, free limited use)

## NSlookup and dig

**nslookup**

nslookup (Name Server Lookup) is the older of the two tools.

Syntax:

nslookup DOMAIN_NAME performs a simple lookup using your default resolver.
nslookup -type=TYPE DOMAIN_NAME [SERVER] specifies a record type and an optional DNS server.

Common DNS record types:

|Query type|	Result|
|--|--|
|A |	IPv4 address(es) for the domain|
|AAAA|	IPv6 address(es) for the domain|
|CNAME|	Canonical Name: an alias that points one domain name to another|
|MX|	Mail Servers: the servers responsible for handling email for the domain|
SOA|	Start of Authority: the primary name server, admin email, and zone serial number|
|TXT|	Text Records: arbitrary text, commonly used for SPF, DKIM, DMARC, and domain verification|


**dig**

dig is the modern, preferred DNS query tool.

Syntax: dig [@SERVER] DOMAIN_NAME [TYPE]

Privacy tip: Use public resolvers like 1.1.1.1 (which supports DNS over HTTPS and DNS over TLS) to avoid your ISP logging your queries.

Defender note: Monitor for unexpected DNS changes (new MX records, rogue TXT entries). These can be signs of subdomain takeover or configuration errors.


## Summary

Key tools and techniques:

- WHOIS: Domain registration details including registrar, dates, and name servers. Most personal details are now redacted for privacy.
- DNS lookups: A/AAAA (IP addresses), MX (mail servers), TXT (SPF/DMARC/verification), and other record types, queried via public resolvers like 1.1.1.1.
- Subdomain enumeration: DNSDumpster for DNS aggregation and graphing, and crt.sh for Certificate Transparency log searches, which is the most effective passive method for discovering subdomains via public SSL/TLS certificates.
- Exposed services: Shodan.io for device banners, ports, and hosting information.

The practical value of these methods is that they are fully passive. They trigger no alerts, carry minimal legal risk (when used ethically and within scope), yet they often uncover forgotten subdomains, outdated services, or misconfigurations.

Command quick-reference:

|Purpose|	Command-line Example|
|--|--|
Lookup WHOIS| record	whois tryhackme.com|
Lookup DNS A records (legacy)|	nslookup -type=A tryhackme.com
Lookup DNS MX records at specific server (legacy)|	nslookup -type=MX tryhackme.com 1.1.1.1
|Lookup DNS TXT records (legacy)|	nslookup -type=TXT tryhackme.com
|Lookup DNS A records (recommended)|	dig tryhackme.com A
|Lookup DNS MX records at specific server (recommended)|	dig @1.1.1.1 tryhackme.com MX|
|Lookup DNS TXT records (recommended)|	dig tryhackme.com TXT
|Passive subdomain discovery (browser-based)|	Visit https://crt.sh and search %.tryhackme.com|

Tips:

- Use DoH/DoT resolvers (e.g., 1.1.1.1) to keep your own queries private.
- As a defender, monitor your footprint: set Shodan/Censys alerts, watch CT logs for new certificates, and track DNS changes for takeover risks.
- Even though passive recon does not touch the target directly, always ensure your overall engagement is authorised and within scope.
- Results change over time. IPs rotate (Cloudflare anycast), subdomains appear and disappear, and privacy redactions increase.
