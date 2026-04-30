# 📌 Hashing Basics
## Recognising Password Hashes
|Prefix|	Algorithm|
|-|-|
|$y$|	yescrypt is a scalable hashing scheme and is the default and recommended choice in new systems
|$gy$|	gost-yescrypt uses the GOST R 34.11-2012 hash function and the yescrypt hashing method
|$7$|	scrypt is a password-based key derivation function
|$2b$, $2y$, $2a$, $2x$|	bcrypt is a hash based on the Blowfish block cipher originally developed for OpenBSD but supported on a recent version of FreeBSD, NetBSD, Solaris 10 and newer, and several Linux distributions
|$6$|	sha512crypt is a hash based on SHA-2 with 512-bit output originally developed for GNU libc and commonly used on (older) Linux systems
|$md5|	SunMD5 is a hash based on the MD5 algorithm originally developed for Solaris
|$1$|	md5crypt is a hash based on the MD5 algorithm originally developed for FreeBSD


## Password Cracking

Time to Crack Some Hashes

Hashcat uses the following basic syntax: hashcat -m <hash_type> -a <attack_mode> hashfile wordlist, where:

- -m <hash_type> specifies the hash-type in numeric format. For example, -m 1000 is for NTLM. Check the official documentation (man hashcat) and example page(opens in new tab) to find the hash type code to use.
- -a <attack_mode> specifies the attack-mode. For example, -a 0 is for straight, i.e., trying one password from the wordlist after the other.
- hashfile is the file containing the hash you want to crack.
- wordlist is the security word list you want to use in your attack.
For example, hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt will treat the hash as Bcrypt and try the passwords in the rockyou.txt file.
