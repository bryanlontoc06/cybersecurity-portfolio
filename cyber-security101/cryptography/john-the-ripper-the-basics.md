# 📌 John the Ripper: The Basics
#### Cracking Basic Hashes

john --format=[format] --wordlist=[path to wordlist] [path to file]

--format=: This is the flag to tell John that you’re giving it a hash of a specific format and to use the following format to crack it
[format]: The format that the hash is in
Example Usage:
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt

Basic:
**john hash.txt**
With wordlist:
**john --wordlist=rockyou.txt hash.txt**
With format:
**john --format=raw-md5 --wordlist=rockyou.txt hash.txt**


#### Cracking/etc/shadow Hashes

Unshadowing
John can be very particular about the formats it needs data in to be able to work with it; for this reason, to crack /etc/shadow passwords, you must combine it with the /etc/passwd file for John to understand the data it’s being given. To do this, we use a tool built into the John suite of tools called unshadow. The basic syntax of unshadow is as follows:

unshadow [path to passwd] [path to shadow]

unshadow: Invokes the unshadow tool
[path to passwd]: The file that contains the copy of the /etc/passwd file you’ve taken from the target machine
[path to shadow]: The file that contains the copy of the /etc/shadow file you’ve taken from the target machine
Example Usage:

unshadow local_passwd local_shadow > unshadowed.txt

Note on the files

When using unshadow, you can either use the entire /etc/passwd and /etc/shadow files, assuming you have them available, or you can use the relevant line from each, for example:

FILE 1 - local_passwd

Contains the /etc/passwd line for the root user:

root:x:0:0::/root:/bin/bash

FILE 2 - local_shadow

Contains the /etc/shadow line for the root user:
  root:$6$2nwjN454g.dv4HN/$m9Z/r2xVfweYVkrr.v5Ft8Ws3/YYksfNwq96UL1FX0OJjY1L6l.DS3KEVsZ9rOVLB/ldTeEL/OIhJZ4GMFMGA0:18576::::::

Cracking
We can then feed the output from unshadow, in our example use case called unshadowed.txt, directly into John. We should not need to specify a mode here as we have made the input specifically for John; however, in some cases, you will need to specify the format as we have done previously using:
--format=sha512crypt

john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt

`hash_id.py` for knowing hash type

----
**zip2john**
zip2john [options] [zip file] > [output file]

[options]: Allows you to pass specific checksum options to zip2john; this shouldn’t often be necessary
[zip file]: The path to the Zip file you wish to get the hash of
>: This redirects the output from this command to another file
[output file]: This is the file that will store the output
Example Usage

zip2john zipfile.zip > zip_hash.txt

----
**rar2john**
rar2john [rar file] > [output file]

rar2john: Invokes the rar2john tool
[rar file]: The path to the RAR file you wish to get the hash of
>: This redirects the output of this command to another file
[output file]: This is the file that will store the output from the command

Example Usage:
/opt/john/rar2john rarfile.rar > rar_hash.txt

----
**ssh2john**
ssh2john [id_rsa private key file] > [output file]

ssh2john: Invokes the ssh2john tool
[id_rsa private key file]: The path to the id_rsa file you wish to get the hash of
>: This is the output director. We’re using it to redirect the output from this command to another file.
[output file]: This is the file that will store the output from
Example Usage
/opt/john/ssh2john.py id_rsa > id_rsa_hash.txt

