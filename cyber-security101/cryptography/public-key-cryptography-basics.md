# 📌 Cryptography
## Public Key Cryptography Basics

**Authentication**: You want to be sure you communicate with the right person, not someone else pretending.
**Authenticity**: You can verify that the information comes from the claimed source.
**Integrity**: You must ensure that no one changes the data you exchange.
**Confidentiality**: You want to prevent an unauthorised party from eavesdropping on your conversations.


**SSH**
**`ssh-keygen`** is the program usually used to generate key pairs.
**`man ssh-keygen`**
**DSA (Digital Signature Algorithm)** is a public-key cryptography algorithm specifically designed for digital signatures.
**ECDSA (Elliptic Curve Digital Signature Algorithm)** is a variant of DSA that uses elliptic curve cryptography to provide smaller key sizes for equivalent security.
**ECDSA-SK (ECDSA with Security Key)** is an extension of ECDSA. It incorporates hardware-based security keys for enhanced private key protection.
**Ed25519** is a public-key signature system using EdDSA (Edwards-curve Digital Signature Algorithm) with Curve25519.
**Ed25519-SK (Ed25519 with Security Key)** is a variant of Ed25519. Similar to ECDSA-SK, it uses a hardware-based security key for improved private key protection.


**Simple comparison**
|Algorithm|	Meaning	Key Idea|
|-|-|
|DSA|	basic signature algorithm	signatures only|
|ECDSA|	DSA using elliptic curves	smaller keys|
|ECDSA-SK|	ECDSA + hardware key	safer private key|
|Ed25519|	modern signature algorithm	faster & modern|
|Ed25519-SK|	Ed25519 + hardware key	fast + hardware protection|

**Super short memory:**
DSA       = old signatures
ECDSA     = smaller keys
ECDSA-SK  = ECDSA + hardware key
Ed25519   = modern signatures
Ed25519-SK= Ed25519 + hardware key


You would use `gpg --import backup.key` to import your key from backup.key
To decrypt your messages, you need to issue `gpg --decrypt confidential_message.gpg`
