# 📌 NMAP Post Port Scans

## Service Detection

```sudo nmap -sV --version-light 10.201.118.127```

## OS Detection and Traceroute

### OS Detection

```sudo nmap -sS -O 10.48.176.61```

### Traceroute

```nmap -sS --traceroute 10.201.118.127```

## Nmap Scripting Engine (NSE)

| Script Category |	Description |
|--|--|
| auth |	Runs authentication-related scripts |
| broadcast |	Discovers hosts by sending broadcast messages |
| brute	| Performs brute-force password auditing against logins |
| default | Runs default scripts (same as -sC) |
| discovery | Retrieves accessible information, such as database tables and DNS names |
| dos | Detects servers vulnerable to Denial of Service (DoS) |
| exploit | Attempts to exploit various vulnerable services |
| external | Checks using a third-party service, such as Geoplugin and Virustotal |
| fuzzer | Launches fuzzing attacks |
| intrusive | Runs intrusive scripts such as brute-force attacks and exploitation |
| malware | Scans for backdoors |
| safe |	Runs safe scripts that won't crash the target |
| version |	Retrieves service versions |
| vuln	| Checks for vulnerabilities or exploits in a vulnerable service |

```sudo nmap -sS -sC 10.201.118.127```
```sudo nmap -sS -n --script "http-date" 10.201.118.127```

## Saving the Output

The number of files can grow quickly, hindering your ability to find previous scan results. The three main formats are:

- Normal
- Grepable (grepable)
- XML

There is a fourth one that we don't recommend:

- Script Kiddie

### Normal

```nmap -oN scan.nmap 10.48.176.61```

### Grepable

```nmap -sS -sV -O -oG MACHINE_IP_scan 10.48.176.61```

### XML

The third format is XML. You can save the scan results in XML format using -oX FILENAME. The XML format would be most convenient for processing the output in other programs. Conveniently enough, you can save the scan output in all three formats using -oA FILENAME to combine -oN, -oG, and -oX for normal, grepable, and XML.

### Script

```nmap -sS 127.0.0.1 -oS FILENAME```

- -oN -> Normal (For your own reading/human-readable)
- -oG -> Grepable (For filtering and searching using the grep command)
- -oX -> XML (For parsing or importing into other tools and scripts)
- -oA -> All (Generates files for all three of the formats above simultaneously)
- -oS -> Script Kiddie (For l33tspeak formatting/just for show)

## Summary

| Option |	Meaning |
|--|--|
| -sV |	Determine service/version info on open ports |
| -sV --version-light |	Try the most likely probes (2) |
| -sV --version-all |	Try all available probes (9) |
| -O |	Detect OS |
| --traceroute |	Run traceroute to the target |
| --script=SCRIPTS |	Nmap scripts to run |
| -sC or --script=default |	Run default scripts |
| -A |	Equivalent to -sV -O -sC --traceroute |
| -oN |	Save output in normal format |
| -oG |	Save output in a grepable format |
| -oX |	Save output in XML format |
| -oA |	Save output in normal, XML and Grepable formats |
