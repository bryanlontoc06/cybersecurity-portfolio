# 📌 Password Cracking

## Identifying Hash Types

### Visual Characteristics

The fastest check is always length and format:

| Hash Type                        | Length                        | Prefix / Format | Example                                                          |
| -------------------------------- | ----------------------------- | --------------- | ---------------------------------------------------------------- |
| MD5                              | 32 hex chars                  | None            | 5f4dcc3b5aa765d61d8327deb882cf99                                 |
| SHA-1                            | 40 hex chars                  | None            | 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8                         |
| SHA-256                          | 64 hex chars                  | None            | 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8 |
| NTLM                             | 32 hex chars                  | None            |
| 8846f7eaee8fb117ad06bdd830b7586c |
| bcrypt                           | ~60 chars $2a$, $2b$, or $2y$ | $2y$12$...      |

### Using Hashid

hashid analyses the format of a hash string and returns a list of possible algorithms. It is pre-installed on Kali. If you don't find it pre-installed on the AttackBox, you can run apt install hashid to install it from the offical repositories.

hashid

```bash
root@tryhackme:~# hashid '5f4dcc3b5aa765d61d8327deb882cf99'
Analyzing '5f4dcc3b5aa765d61d8327deb882cf99'
[+] MD2
[+] MD5
[+] MD4
[+] Double MD5
[+] LM
[+] RIPEMD-128
[+] Haval-128
[+] Tiger-128
[+] Skein-256(128)
[+] Skein-512(128)
[+] Lotus Notes/Domino 5
[+] Skype
[+] Snefru-128
[+] NTLM
[+] Domain Cached Credentials
[+] Domain Cached Credentials 2
[+] DNSSEC(NSEC3)
[+] RAdmin v2.x
```

hashid returns several candidates for a 32-character hex string because multiple algorithms produce output of that length. Use context to narrow it down, or try the most likely candidates in order when cracking.

For bcrypt, hashid gives an unambiguous result:

hashid

```bash
root@tryhackme:~# hashid '$2y$10$wJ/mZDURD4jQ0lrCEMheE.8FzMXNEBNjIkuZgEFm9VMn1m4ZP4eDG'
Analyzing '$2y$10$wJ/mZDURD4jQ0lrCEMheE.8FzMXNEBNjIkuZgEFm9VMn1m4ZP4eDG'
[+] Blowfish(OpenBSD)
[+] Woltlab Burning Board 4.x
[+] bcrypt
```

All three results refer to the same hash format. hashid labels it Blowfish(OpenBSD) because bcrypt uses Blowfish's key schedule internally and was first shipped with OpenBSD. Any $2y$ or $2b$ prefix means bcrypt.

### Hashcat --identify

If we are already working in Hashcat, we can skip switching tools entirely. Since version 6.2.6, Hashcat includes a built-in identification flag:

hashcat

```bash
root@tryhackme:~# hashcat --identify '5f4dcc3b5aa765d61d8327deb882cf99'
The following 11 hash-modes match the structure of your input hash:

      # | Name                                                       | Category
  ======+============================================================+======================================
    900 | MD4                                                        | Raw Hash
      0 | MD5                                                        | Raw Hash
     70 | md5(utf16le($pass))                                        | Raw Hash
   2600 | md5(md5($pass))                                            | Raw Hash salted and/or iterated
   3500 | md5(md5(md5($pass)))                                       | Raw Hash salted and/or iterated
   4400 | md5(sha1($pass))                                           | Raw Hash salted and/or iterated
  20900 | md5(sha1($pass).md5($pass).sha1($pass))                    | Raw Hash salted and/or iterated
   4300 | md5(strtoupper(md5($pass)))                                | Raw Hash salted and/or iterated
   1000 | NTLM                                                       | Operating System
   9900 | Radmin2                                                    | Operating System
   8600 | Lotus Notes/Domino 5                                       | Enterprise Application Software (EAS)
```

The output lists matching modes with their numbers ready to copy directly into a cracking command. For a clean workflow, --identify is often the fastest path from unknown hash to first crack attempt.

`hashcat -m 0 '5f4dcc3b5aa765d61d8327deb882cf99' /usr/share/wordlists/rockyou.txt`

### Online Resources

For quick lookups, two sites are worth knowing:

- [crackstation.net](https://crackstation.net/): Paste a hash, and it checks against a pre-computed lookup table of billions of entries. If the plaintext has been seen before, it comes back instantly.
- [hashes.com](https://hashes.com/en/decrypt/hash): Identifies the hash type and attempts a lookup against a large database. Also accepts bulk hash submissions.

### Hashcat Modes and John Formats

Once we have identified the algorithm, we translate it to the tool-specific format or mode number:

**----------------------------------------**
| Algorithm | Hashcat Mode ( -m) | John Format ( --format=) |
| --------- | ------------------ | ------------------------ |
| MD5 | 0 | raw-md5 |
| SHA-1 | 100 | raw-sha1 |
| SHA-256 | 1400 | raw-sha256 |
| SHA-512 | 1700 | raw-sha512 |
| NTLM | 1000 | nt |
| bcrypt | 3200 | bcrypt |
**----------------------------------------**

These values are fixed. Running hashcat -m 1000 always means NTLM, regardless of the hash's format. Getting the mode wrong is one of the most common causes of a crack producing no results.

## Wordlists and Attack Strategies

Note: All tool and wordlist paths in this task reflect the current AttackBox. If you are on an older version, the paths may differ: Hashcat rules at `/opt/hashcat/rules/`, and John rules at `/opt/john/rules/`. If a path does not exist, use `find / -name <filename> 2>/dev/null` to locate it.

### Dictionary attacks

### Rule-Based Attacks

Rule-based attacks take an existing wordlist and apply transformations to each word, generating the mutations that people commonly use when constructing passwords:

- Capitalise the first letter: password → Password
- Append a number: password → password1
- Add a special character: password → password!
- Substitute characters: password → p@ssw0rd

| Rule File | Description |
| best64.rule | 64 highly effective mutations, good first choice |
| rockyou-30000.rule | 30,000 rules derived from RockYou analysis |
| d3ad0ne.rule | Large community-built rule set |
| dive.rule | An extensive rule set covering a wide range of mutations |
| OneRuleToRuleThemAll.rule | Popular community-compiled rule set; not bundled by default, verify it exists on the system before use |

John the Ripper has its own rule sets at `/usr/local/john/run/rules/`. You can activate them with `--rules=wordlist` (applies default mutations) or `--rules=single` (applies the Single rule set, which generates name and username-based mutations).

John also supports mask attacks via its `--mask=` option, though we will focus on Hashcat's mask syntax in this room since it is more widely documented.

### Mask Attacks

| Placeholder | Character Set           |
| ----------- | ----------------------- |
| ?l          | Lowercase letters (a-z) |
| ?u          | Uppercase letters (A-Z) |
| ?d          | Digits (0-9)            |
| ?s          | Special characters      |
| ?a          | All printable ASCII     |

A mask for a password like Summer2026! would be ?u?l?l?l?l?l?d?d?d?d?s. This generates far fewer candidates than a full brute force of the same length.

### Choosing the Right Approach

| Scenario                                  | Best approach                          |
| ----------------------------------------- | -------------------------------------- |
| No information about the password         | Dictionary attack with rockyou.txt     |
| Dictionary fails, password likely mutated | Dictionary + rules (e.g., best64.rule) |
| Known password pattern or enforced policy | Mask attack                            |
| Short password, small character set       | Brute force (constrained length only)  |
| Target likely used company-specific terms | Custom wordlist + rules                |

## Cracking with John the Ripper and Hashcat

### Setting Up the Example Hash

All examples in this task use two MD5 hashes. Write them to files before running anything:

```bash
echo "5f4dcc3b5aa765d61d8327deb882cf99" > demo.txt
echo "0571749e2ac330a7455809c6b0e7af90" > demo2.txt
echo "37b4e2d82900d5e94b8da524fbeb33c0" > demo3.txt
```

demo.txt is used for basic dictionary attacks, demo2.txt for rule-based attacks, and demo3.txt for the mask attack

### John the Ripper

John the Ripper (usually just "John") is a versatile CPU-based cracker. It handles a wide range of hash formats, including many non-standard ones, and can auto-detect formats when we do not specify one explicitly. It is particularly good for Unix shadow file entries and for quick attempts that require automatic format detection.

**Basic Dictionary Attack**

john --format

```bash
root@tryhackme:~# john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt demo.txt
Created directory: /root/.john
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
password         (?)
1g 0:00:00:00 DONE (2026-03-24 06:24) 4.166g/s 1600p/s 1600c/s 1600C/s 123456..michael1
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

Flags explained:

- --format=raw-md5, specifies the hash format explicitly (use the John format labels from Task 3)
- --wordlist=, path to the wordlist file
- demo.txt, the file containing one hash per line

**Auto-detect mode (when unsure of the format):**

john autodetect

```bash
root@tryhackme:~# john --wordlist=/usr/share/wordlists/rockyou.txt demo.txt
Warning: detected hash type "LM", but the string is also recognized as "dynamic=md5($p)"
Use the "--format=HAVAL-128-4" option to force loading these as that type instead
Use the "--format=NT" option to force loading these as that type
Loaded 2 password hashes with no different salts (LM [DES 256/256 AVX2])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
...
0g 0:00:00:24 DONE (2026-03-24 06:25) 0g/s 440565p/s 440565c/s 881130C/s !!1QWER..*7¡VA
Session completed.
```

Notice the result: 0g means zero passwords cracked. John defaulted to LM format, which is wrong for our MD5 hash, so the entire wordlist ran without a single match. This is exactly why explicit --format is preferred when we know the algorithm.

**Rule-Based Attack:**

john --rules

```bash
root@tryhackme:~# john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt --rules=wordlist demo2.txt
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
sunshine         (?)
1g 0:00:00:00 DONE (2026-03-24 06:40) 1.492g/s 573.1p/s 573.1c/s 573.1C/s 123456..michael1
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

**Viewing Cracked Passwords**

John stores results in its potfile at /usr/local/john/run/john.pot. To display previously cracked hashes:

john --show

```bash
root@tryhackme:~# john --show --format=raw-md5 demo.txt
?:password

1 password hash cracked, 0 left
```

Always include --format with --show, otherwise John may not locate the entries in the potfile correctly.

### Hashcat

**Basic Dictionary Attack:**

```bash
root@tryhackme:~# hashcat -m 0 -a 0 demo.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.2.6) starting
...

5f4dcc3b5aa765d61d8327deb882cf99:password

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 5f4dcc3b5aa765d61d8327deb882cf99
Speed.#1.........:     7724 H/s (0.08ms) @ Accel:256 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
...
```

Flags explained:

- -m 0, hash type (0 = MD5; use the mode numbers from Task 3)
- -a 0, attack mode (0 = dictionary)
- demo.txt, the file containing one hash per line
- The final argument is the wordlist path

**Rule-Based Attack:**

hashcat rules

```bash
root@tryhackme:~# hashcat -m 0 -a 0 demo2.txt /usr/share/wordlists/rockyou.txt -r /usr/local/hashcat/rules/best64.rule
hashcat (v6.2.6) starting

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 1104517645

0571749e2ac330a7455809c6b0e7af90:sunshine

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 5f4dcc3b5aa765d61d8327deb882cf99
Time.Started.....: Tue Mar 24 06:51:23 2026 (0 secs)
Time.Estimated...: Tue Mar 24 06:51:23 2026 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best64.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  8591.6 kH/s (4.07ms) @ Accel:256 Loops:77 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
...

Started: Tue Mar 24 06:51:22 2026
Stopped: Tue Mar 24 06:51:25 2026
```

Flags explained:

- -r, specifies a rule file to apply to the wordlist

**Mask Attack:**

hashcat mask

```bash
root@tryhackme:~# hashcat -m 0 -a 3 demo3.txt '?l?l?l?l?l?l?l?l'
hashcat (v6.2.6) starting

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Brute-Force
* Raw-Hash

Host memory required for this attack: 0 MB

37b4e2d82900d5e94b8da524fbeb33c0:football

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 37b4e2d82900d5e94b8da524fbeb33c0
Time.Started.....: Wed May 20 12:49:52 2026 (11 secs)
Time.Estimated...: Wed May 20 12:50:03 2026 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Mask.......: ?l?l?l?l?l?l?l?l [8]
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 27970.6 kH/s (4.14ms) @ Accel:128 Loops:512 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 310855680/208827064576 (0.15%)
Rejected.........: 0/310855680 (0.00%)
Restore.Point....: 17664/11881376 (0.15%)
Restore.Sub.#1...: Salt:0 Amplifier:1024-1536 Iteration:0-512
Candidate.Engine.: Device Generator
Candidates.#1....: rladoner -> mrelkess
Hardware.Mon.#1..: Util: 90%

Started: Tue Mar 24 07:06:42 2026
Stopped: Tue Mar 24 07:06:50 2026
```

Flags explained:

- -a 3, attack mode 3 = mask attack
- The final argument is the mask pattern

**Saving Output to a File**

Saving output

```bash
root@tryhackme:~# hashcat -m 0 -a 0 demo2.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
cat cracked.txt
0571749e2ac330a7455809c6b0e7af90:sunshine
```

**Viewing Results After the Run**

Hashcat stores results in `/usr/local/hashcat/hashcat.potfile`. After a session completes, add `--show` to display results without re-running the attack:

hashcat --show

```bash
root@tryhackme:~# hashcat -m 0 demo.txt --show
5f4dcc3b5aa765d61d8327deb882cf99:password
```

### Performance notes

Worth knowing before running anything long:

- CPU fallback: On the AttackBox without a dedicated GPU, Hashcat runs in CPU mode. Dictionary attacks against MD5 and SHA-256 will still complete quickly. bcrypt will be slow by design, regardless.
- Potfile behaviour: Both tools skip hashes that are already in their potfile. Re-running an attack against the same hashes will not waste time re-cracking.
- Session resuming: For long Hashcat runs, use --session= to name the session and --restore to resume it if interrupted:

Hashcat Session

`root@tryhackme:~# hashcat -m 3200 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt --session=bcrypt_crack`

If the run is interrupted, resume exactly where it left off:

Hashcat Resume

`root@tryhackme:~# hashcat --session=bcrypt_crack --restore`

### Comparison

|                      | John the Ripper                              | Hashcat                                     |
| -------------------- | -------------------------------------------- | ------------------------------------------- |
| Acceleration         | CPU (primarily)                              | GPU (primarily, CPU fallback)               |
| Speed (MD5/SHA)      | Fast                                         | Very fast                                   |
| Format detection     | Good auto-detect                             | Explicit mode required                      |
| Non-standard formats | Excellent                                    | Good                                        |
| Rule sets            | Built-in + extensible                        | Large file library                          |
| Best for             | Quick attempts, varied formats, shadow files | Sustained attacks, GPU-accelerated cracking |

Neither tool is strictly better. In practice, John works well for quick auto-detect attempts and for formats that Hashcat handles poorly. Hashcat is the choice for sustained, high-speed dictionary, rule, or mask attacks.

## Password Cracking Workflow & Hash Identification Guide

1. Standard Cracking Workflow
   a. Identify the Hash Type: Inspect the structure, length, and prefix of the hash using cat and hashid.
   `cat /path/to/hash.txt | hashid`

b. Execute the Attack: Run Hashcat with the corresponding mode (-m) and your target wordlist.
`hashcat -m <MODE_ID> /path/to/hash.txt /usr/share/wordlists/rockyou.txt`

c. View Cracked Results: Retrieve previously cracked passwords directly from Hashcat's internal potfile.
`hashcat -m <MODE_ID> /path/to/hash.txt --show`

2. Common Hash Identification Cheat Sheet

| Algorith | Common Indicators / Formats                | Hashcat Mode (-m) | Technical Notes                                                           |
| -------- | ------------------------------------------ | ----------------- | ------------------------------------------------------------------------- |
| MD5      | 32 Hexadecimal Characters (128-bit)        | -m 0              | Fast to crack on modern hardware.                                         |
| NTLM     | 32 Hexadecimal Characters (Windows SAM/AD) | -m 1000           | Try this if MD5 exhausts without results on a 32-char hash.               |
| SHA-1    | 40 Hexadecimal Characters                  | -m 100            | Commonly found in legacy web applications.                                |
| SHA-256  | 64 Hexadecimal Characters                  | -m 1400           | Standard modern hashing algorithm.                                        |
| Bcrypt   | Starts with $2a$, $2b$, or $2y$            | -m 3200           | Intentionally CPU-heavy (Blowfish-based); often faster to crack via john. |

3. Key Attack Methodologies

- Dictionary Attack: Iterates sequentially through every plain-text entry in a wordlist (e.g., rockyou.txt).
- Brute-Force Attack: Tests every possible combination of characters within a defined length range.
- Mask Attack: Restricts brute-force attempts to specific known patterns or structural rules (e.g., ?u?l?l?d?d for 1 Uppercase + 3 Lowercase + 2 Digits).
- Rule-Based Attack: Applies mutation rules to wordlist entries (e.g., replacing a with @, appending digits or common year suffixes).

4. Tool Specialization: Hashcat vs. John the Ripper

- Hashcat: Best suited for high-speed, mass dictionary, rule, and mask attacks leveraging GPU acceleration.
- John the Ripper (JTR): Excellent for quick auto-detection, proprietary key files (SSH keys, ZIP/RAR, Linux /etc/shadow), and CPU-bound slow hashes like Bcrypt.
