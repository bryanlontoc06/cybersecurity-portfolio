# 📌 Protocols and Servers 2

## Sniffing Attack

Useful Tcpdump Filters

When capturing traffic, effective filtering helps you focus on relevant packets:

### Capture traffic on a specific port
sudo tcpdump port 110 -A

### Capture traffic to/from a specific host
sudo tcpdump host 10.20.30.148 -A

### Capture HTTP traffic (may include credentials in POST requests)
sudo tcpdump port 80 -A

### Capture FTP traffic (credentials sent in cleartext)
sudo tcpdump port 21 -A

### Write captured packets to a file for later analysis
sudo tcpdump -w capture.pcap

### Read and analyse a capture file
tcpdump -r capture.pcap -A

## Man-in-the-Middle (MITM) Attack

### How MITM Attacks Work

For an attacker to position themselves between two communicating parties, they typically need to redirect traffic through their system. Common techniques include:

ARP Spoofing is effective on local networks. The attacker sends forged ARP (Address Resolution Protocol) messages to associate their MAC address with the IP address of the default gateway or target system. As a result, traffic intended for those systems is sent to the attacker instead.

DNS Spoofing involves providing false DNS responses to redirect victims to attacker-controlled servers. This can happen through compromised DNS servers, DNS cache poisoning, or by responding to DNS queries faster than the legitimate server.

Rogue Access Points are fake wireless access points set up by attackers. When victims connect to these networks (often named to look like legitimate networks such as Airport_WiFi_Free), all their traffic flows through the attacker's system.

BGP Hijacking operates at the internet routing level, where attackers announce false BGP routes to redirect traffic through their infrastructure. This is a more sophisticated attack, typically targeting specific organisations or regions.

### Tools for MITM Attacks

Tools for MITM Attacks
Many tools can aid in carrying out MITM attacks. Understanding these helps you recognise what attackers can do and why mitigations matter:

- Bettercap(opens in new tab) is the modern successor to Ettercap and is actively maintained. It supports ARP spoofing, DNS spoofing, HTTP/HTTPS proxying, and has a modular architecture for various attack scenarios.
- Ettercap(opens in new tab) is a classic tool for MITM attacks on LANs. While still functional, Bettercap is generally preferred for modern assessments.
- mitmproxy is an interactive HTTPS proxy that allows inspection and modification of traffic. It is particularly useful for analysing and manipulating HTTP/HTTPS communications.
- Responder is designed for Windows environments and exploits name resolution protocols such as LLMNR (Link-Local Multicast Name Resolution) and NBT-NS (NetBIOS Name Service). These are fallback protocols that Windows systems use when standard DNS resolution fails. Responder listens for these broadcast queries and responds with its own IP address, tricking victims into sending authentication credentials to the attacker. This is a common technique during internal penetration tests of Active Directory environments.

### MITM Against Encrypted Traffic
MITM attacks can target HTTPS and other encrypted protocols through several approaches:

**SSL Stripping** downgrades HTTPS connections to HTTP. When a victim tries to connect to a website, the attacker intercepts the request, establishes an HTTPS connection with the legitimate server, but serves the content to the victim over unencrypted HTTP. The victim may not notice the missing padlock icon, especially if they did not explicitly type https://.

**Fake Certificates** involve the attacker presenting their own certificate and establishing separate encrypted connections with both parties. This works if the victim accepts an invalid certificate warning or if the attacker has compromised a Certificate Authority (CA).

**Compromised or Rogue CAs** represent a more serious threat. If an attacker controls a trusted CA (or obtains a fraudulent certificate from one), they can generate valid-looking certificates for any domain.

## Transport Security Layer (TLS)

## Secure Shell (SSH)

### SSH Key Generation
To set up key-based authentication, generate a key pair using ssh-keygen:

```bash
# Generate an Ed25519 key (recommended for modern systems)
ssh-keygen -t ed25519 -C "your_email@example.com"

# For systems that don't support Ed25519, use RSA with 4096 bits
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Ed25519 keys are the current recommendation. They are shorter, faster, and considered more secure than RSA keys. RSA keys should use at least 4096 bits if Ed25519 is not available.

Your private key is stored in ~/.ssh/id_ed25519 (or ~/.ssh/id_rsa) and should be protected with a strong passphrase. The public key is stored with a .pub extension and can be safely shared.

To enable key-based login on a server, add your public key to the ~/.ssh/authorized_keys file on the remote system:

```bash
# Copy your public key to a remote server
ssh-copy-id mark@MACHINE_IP
```

### Useful SSH Options

SSH has many useful features for penetration testers and system administrators:

**Connect on a non-standard port**
ssh -p 2222 mark@MACHINE_IP

**Use a specific private key**
ssh -i ~/.ssh/custom_key mark@MACHINE_IP

**Jump through a bastion/jump host to reach an internal server**
ssh -J bastion.example.com mark@internal-server

**Local port forwarding (access remote service through local port)**
ssh -L 8080:localhost:80 mark@MACHINE_IP

**Dynamic port forwarding (SOCKS proxy)**
ssh -D 9050 mark@MACHINE_IP

**Run a single command without interactive shell**
ssh mark@MACHINE_IP "cat /etc/passwd"


### SSH Config File

For frequent connections, you can create shortcuts in ~/.ssh/config:

```bash
Host webserver
    HostName MACHINE_IP
    User mark
    Port 22
    IdentityFile ~/.ssh/id_ed25519

Host internal
    HostName 10.10.10.50
    User admin
    ProxyJump bastion.example.com
```
With this configuration, you can simply type ssh webserver instead of the full command.


### Secure File Transfer

SSH provides secure file transfer capabilities through several methods:

SFTP (SSH File Transfer Protocol) is the recommended method for interactive file transfers. It provides a familiar FTP-like interface over an encrypted SSH connection:

```bash
sftp mark@MACHINE_IP
```


SCP (Secure Copy Protocol) has traditionally been used for simple file copies. However, the OpenSSH project has deprecated scp in favour of sftp due to security concerns with the SCP protocol. SCP still works but displays a deprecation warning on newer systems:

```bash
# Copy from remote to local
scp mark@MACHINE_IP:/home/mark/archive.tar.gz ~/

# Copy from local to remote
scp backup.tar.bz2 mark@MACHINE_IP:/home/mark/
```

rsync over SSH is preferred for transferring large amounts of data or synchronising directories, as it only transfers changed portions of files:

```bash
rsync -avz -e ssh /local/directory/ mark@10.49.184.132:/remote/directory/
```

```bash
user@TryHackMe$ scp document.txt mark@10.49.184.132:/home/mark
mark@10.49.184.132's password:
document.txt                                        100% 1997KB  70.4MB/s   00:00
```

### SSH vs FTPS vs SFTP

- SFTP runs over SSH (port 22) and is the most common choice for secure file transfers today.
- FTPS is FTP secured with TLS (port 990 for implicit TLS). It is a different protocol from SFTP despite the similar names.
- SCP also runs over SSH but is being phased out in favour of SFTP.

#### SSH Hardening Considerations

When assessing or configuring SSH servers, consider these security settings in /etc/ssh/sshd_config:

Disable password authentication (PasswordAuthentication no) after setting up key-based auth.
Disable root login (PermitRootLogin no) to force users to authenticate as regular users first.
Use AllowUsers or AllowGroups to restrict which accounts can log in via SSH.
Change the default port to reduce automated scanning noise (security through obscurity, not a strong control).
Enable fail2ban or similar to block repeated failed authentication attempts.
Use modern key exchange and cipher algorithms by configuring KexAlgorithms, Ciphers, and MACs.

## Password Attack

### Types of Password Attacks
Attacks against passwords have evolved beyond simple brute force:

Password Guessing requires some knowledge of the target, such as their pet's name, birth year, favourite sports team, or children's names. Social media makes gathering this information easier than ever.

Dictionary Attack attempts common words from a dictionary or wordlist. This is effective because many users choose real words or simple variations.

Brute Force Attack tries all possible character combinations. This is exhaustive and time-consuming, but effective against short passwords. The search space grows exponentially with password length, which is why longer passwords are significantly more secure.

Credential Stuffing uses username/password pairs leaked from previous breaches and tries them against other services. This exploits password reuse and is extremely effective. Attackers use automated tools to test millions of credentials across many sites simultaneously.

Password Spraying tries a small number of commonly used passwords against many accounts. Instead of trying many passwords against one account (which triggers lockouts), an attacker tries one or two passwords against thousands of accounts. This evades account lockout mechanisms.

Hybrid Attacks combine dictionary words with common patterns. For example, trying Summer with years appended (Summer2023, Summer2024) or common substitutions (P@ssw0rd, Adm1n!).

### THC Hydra

[THC Hydra](https://github.com/vanhauser-thc/thc-hydra) is a fast and flexible password cracking tool that supports many protocols, including FTP, POP3, IMAP, SMTP, SSH, and all methods related to HTTP. It provides an automated way to try common passwords or entries from a wordlist against network services.

The general command-line syntax is:

```hydra -l username -P wordlist.txt server service```

Where the following options are specified:

- -l username: The login name of the target.
- -P wordlist.txt: A text file containing passwords to try.
- server: The hostname or IP address of the target server.
- service: The service you are attacking (e.g., ftp, ssh, imap, pop3, smtp).


Consider the following examples:

```bash
# Attack FTP with username mark
hydra -l mark -P /usr/share/wordlists/rockyou.txt 10.49.184.132 ftp

# Alternative syntax (equivalent to above)
hydra -l mark -P /usr/share/wordlists/rockyou.txt ftp://10.49.184.132

# Attack SSH with username frank
hydra -l frank -P /usr/share/wordlists/rockyou.txt 10.49.184.132 ssh

# Attack IMAP with username lazie
hydra -l lazie -P /usr/share/wordlists/rockyou.txt 10.49.184.132 imap

# Attack with a list of usernames (credential stuffing style)
hydra -L users.txt -P passwords.txt 10.49.184.132 ssh
```
Useful Hydra Options:

| Option |	Description |
|--|--|
| -l username |	Single username to attack |
| -L users.txt |	File containing a list of usernames |
| -p password |	Single password to try |
| -P wordlist.txt |	File containing a list of passwords |
| -s PORT |	Specify a non-default port |
| -V or -vV |	Verbose output showing attempts |
| -t n |	Number of parallel connections (threads) |
| -d |	Debug mode for troubleshooting |
| -f |	Stop after the first valid password found |
| -w n |	Wait time between connections |

Other Password Attack Tools

While Hydra is excellent for network service attacks, other tools serve different purposes:

1. Medusa is similar to Hydra but with a modular design. Some find it more stable for certain protocols.
2. Ncrack is developed by the Nmap project and designed for high-speed parallel authentication testing.
3. CrackMapExec (CME) / NetExec specialises in Windows/Active Directory environments and can spray passwords across SMB, WinRM, LDAP, and other protocols.
4. Burp Suite Intruder is useful for attacking web-based login forms where Hydra's HTTP modules may not work correctly.
5. Hashcat and John the Ripper are used for cracking password hashes offline rather than attacking live services. If you obtain password hashes (from a database breach, for example), these tools can recover the plaintext passwords much faster than attacking a live service.

### Mitigating Password Attacks
Mitigation against password attacks depends on the target system. Modern defences include:

Password Policies enforce minimum complexity constraints. Modern guidance such as NIST SP 800-63B (a U.S. government standard for digital identity guidelines) recommends focusing on password length over complexity rules, blocking known compromised passwords, and not requiring regular password changes unless there is evidence of compromise.

Account Lockout temporarily or permanently locks an account after a certain number of failed attempts. This is effective against brute force but can be bypassed by password spraying or abused for denial of service.

Throttling and Rate Limiting delay responses to login attempts. A few seconds of delay is tolerable for legitimate users but severely hinders automated tools. More sophisticated implementations use exponential backoff.

CAPTCHA requires solving a challenge difficult for machines. Modern CAPTCHAs use behavioural analysis and risk scoring rather than just image recognition.

Multi-Factor Authentication (MFA) requires additional verification beyond the password, such as a code from an authenticator app, SMS (though SMS is less secure), or a hardware security key. MFA is one of the most effective defences against password attacks.

Passwordless Authentication eliminates passwords entirely using methods like:

- Passkeys (FIDO2/WebAuthn) use cryptographic keys stored on devices, replacing passwords with biometric or PIN verification.
- Magic links sent via email.
- Hardware security keys like YubiKeys.

Breached Password Detection checks passwords against known breach databases during registration and login. Services like "Have I Been Pwned" provide APIs for this purpose.

Behavioural Analysis detects anomalies such as login attempts from unusual locations, impossible travel scenarios, or patterns consistent with automated attacks.

IP-based Controls including geofencing, blocking known malicious IPs, and requiring additional verification for new devices or locations.

Using a combination of the above approaches provides defence in depth against password attacks. For high-security environments, moving towards passwordless authentication eliminates many of these attack vectors entirely.

### Defensive Checklist

When assessing or securing systems, verify the following:

1. All services use TLS 1.2 or TLS 1.3 with strong cipher suites.
2. Cleartext protocols (Telnet, FTP, HTTP) are disabled or restricted to isolated networks.
3. SSH uses key-based authentication with password authentication disabled.
4. Strong password policies are enforced with breached password detection.
5. Account lockout or rate limiting is implemented for all authentication endpoints.
6. Multi-factor authentication is enabled for sensitive systems.
7. Network segmentation limits the impact of sniffing attacks.
8. Certificate validation is properly implemented to prevent MITM attacks.
9. HSTS (HTTP Strict Transport Security) is enabled for web applications to prevent SSL stripping.
10. Logging and monitoring detect authentication anomalies.
