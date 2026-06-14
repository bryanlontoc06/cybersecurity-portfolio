# 📌 Content Discovery

## Manual Discovery - Common Files

### robots.txt
The robots.txt file tells search engine crawlers which pages they may index. Site owners often list sensitive directories here to prevent them from appearing in search results, making it a ready-made list of interesting locations for a penetration tester.

### sitemap.xml
Unlike robots.txt (which restricts crawlers), sitemap.xml tells search engines which pages the owner wants listed. These files sometimes include staging pages, old content, or URLs that are hard to reach via the normal site. Check it at http://MACHINE_IP/sitemap.xml.

## Manual Discovery - Headers & Framework Stack

### HTTP Headers

## OSINT - Search Engines & Web Tools

### Google Hacking / Dorking

| Filter |	Example	Description |
|--|--|
| site |	site:tryhackme.com |	Returns results only from the specified domain
| inurl |	inurl:admin	Returns | results with the specified word in the URL
| filetype |	filetype:pdf |	Returns results of a specific file type
| intitle |	intitle:admin |	Returns results with the specified word in the page title
| intext |	intext:password |	Returns results containing the specified word in the body
| cache |	cache:tryhackme.com |	Shows Google's cached version of the page

## OSINT - Repositories & Archives

### Wayback Machine

The [Wayback Machine](https://web.archive.org/) is an archive of the Internet dating back to the late 1990s. Search for a domain, and you'll see every snapshot captured over time. This is useful for finding pages that have been removed from the live site but may still be accessible: old login forms, forgotten API endpoints, or content that was published briefly before being taken down.

## Automated Discovery - Gobuster Fundamentals


## Automated Discovery - Subdomains & Virtual Hosts

### Subdomains vs Virtual Hosts
It's important to understand the difference between these two concepts before using Gobuster to enumerate them:

- A subdomain is resolved through DNS. For example, blog.example.thm is a DNS record that points to an IP address.
- A virtual host (vhost) is resolved by the web server. Multiple sites can run on the same IP address, with the server using the Host: HTTP header to decide which site to serve.

As mentioned, Gobuster has separate modes for each: dns for subdomains and vhost for virtual hosts.

### Preparing the Environment
We are going to work in a local network with a DNS server on the web server. To ensure we can resolve the domains used throughout this room, you need to change the /etc/resolv-dnsmasq file:

- Open up a terminal on the AttackBox and enter the command: sudo nano /etc/resolv-dnsmasq.
- Insert nameserver 10.48.129.215 as the first line.
- Save the file by pressing CTRL+O, followed by pressing ENTER, and then exit the editor by pressing CTRL+X.
- Enter the command /etc/init.d/dnsmasq restart to restart the Dnsmasq service.

The file should look something like this:

root@tryhackme:~# cat /etc/resolv-dnsmasq
nameserver 10.48.129.215
nameserver 169.254.169.253


### Updating the Host File
To ensure the domain used in this room resolves correctly, we need to manually map it to the target IP using the /etc/hosts file:

- Open a terminal on the AttackBox and run: sudo nano /etc/hosts.
- Add the following line at the end of the file: 10.48.129.215 example.thm.
- Save the file by pressing CTRL+O, then ENTER, and exit using CTRL+X.
- You can verify the change by running: ping example.thm.

### dns Mode
The dns mode performs DNS lookups using wordlist entries as subdomain candidates. The required flags are -d (domain) and -w (wordlist). The --wildcard option in Gobuster is used to force enumeration even when wildcard DNS is detected, allowing results to be returned despite potential false positives.


```bash
root@tryhackme:~# gobuster dns -d example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --wildcard
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Domain:            example.thm
[+] Threads:           10
[+] Wildcard forced:   true
[+] Timeout:           1s
[+] Wordlist:          /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
===============================================================
Starting gobuster in DNS enumeration mode
===============================================================
Found: shop.example.thm

Found: www.shop.example.thm

Found: webdisk.shop.example.thm

Found: autodiscover.shop.example.thm

Found: autoconfig.shop.example.thm

Found: academy.example.thm

Found: primary.example.thm

Progress: 4997 / 4998 (99.98%)
===============================================================
Finished
===============================================================
```

Some useful flags for dns mode are:

| Flag |	Description |
|--|--|
| -d / --domain |	The target domain to enumerate |
| -i / --show-ips |	Show the IP addresses that subdomains resolve to |
| -r / --resolver |	Use a custom DNS server for lookups |

### vhost Mode
The vhost mode doesn't use DNS. Instead, it sends HTTP requests to the target IP, cycling through wordlist entries as the Host: header value. This finds virtual hosts that aren't registered in public DNS.

Run the vhost scan with the following commands. The --append-domain flag tells Gobuster to combine each wordlist entry with the domain, and --exclude-length filters out false positives that share a common response size:

```bash
root@tryhackme:~# gobuster vhost -u "http://10.48.129.215" --domain example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:              http://10.82.123.47
[+] Method:           GET
[+] Threads:          10
[+] Wordlist:         /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
[+] User Agent:       gobuster/3.6
[+] Timeout:          10s
[+] Append Domain:    true
[+] Exclude Length:   259,271,291,293,306,252,264,304,308,263,301,298,307,309,254,261,267,283,295,318,265,292,320,270,289,313,314,319,268,272,286,312,258,277,287,294,317,251,285,315,302,257,260,262,275,284,266,279,281,297,305,311,250,273,278,282,290,256,276,269,296,310,316,274,300,303,253,255,280,288,299
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Progress: 4997 / 4998 (99.98%)
===============================================================
Finished
===============================================================
```

## Conclusion

| Method |	Techniques |
|--|--|
| Manual |	robots.txt, sitemap.xml, favicon fingerprinting, HTTP headers, framework stack |
| OSINT |	Google dorking, Wappalyzer, Wayback Machine, GitHub, S3 buckets |
| Automated |	Gobuster dir, dns, and vhost modes |
