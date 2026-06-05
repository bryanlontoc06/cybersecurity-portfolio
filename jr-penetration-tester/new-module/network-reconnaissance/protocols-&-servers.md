# 📌 Protocols and Servers

## Telnet

## Hypertext Transfer Protocol (HTTP)

Hypertext Transfer Protocol (HTTP) is the protocol used to transfer web pages. Your web browser connects to the web server and uses HTTP to request HTML pages, images, and other files. It also submits forms and uploads various files. Any time you browse the World Wide Web (WWW), you are using the HTTP protocol.

## File Transfer Protocol (FTP)

File Transfer Protocol (FTP) was developed to make the transfer of files between different computers with different systems efficient. It was one of the earliest protocols designed for the internet and remains in use today, though it has largely been replaced by secure alternatives for most purposes.

## Single Mail Transfer Protocol (SMTP)

## Post Office Protocol 3 (POP3)

Post Office Protocol version 3 (POP3) is a protocol used to download email messages from a Mail Delivery Agent (MDA) server, as shown in the figure below. The mail client connects to the POP3 server, authenticates, downloads the new email messages, and then (optionally) deletes them from the server.

## Internet Message Access Protocol (IMAP)

## Summary

### Protocol Reference
It is useful to remember the default port number for common protocols. Below is a summary of the protocols covered, sorted in alphabetical order, along with their default port numbers and secure alternatives.

| Protocol |	TCP Port |	Application(s) |	Data Security |	Secure Alternative |	Secure Port |
|--|--|--|--|--|--|
| FTP |	21 |	File Transfer |	Cleartext	| FTPS or SFTP |	990 (FTPS), 22 (SFTP) |
| HTTP |	80 |	Worldwide Web |	Cleartext |	HTTPS |	443 |
| IMAP |	143 |	Email (MDA) |	Cleartext |	IMAPS |	993 |
| POP3 |	110 |	Email (MDA) |	Cleartext |	POP3S |	995 |
| SMTP |	25 |	Email (MTA) |	Cleartext |	SMTPS or SMTP with STARTTLS |	465 (SMTPS), 587 (Submission) |
| Telnet |	23 |	Remote Access |	Cleartext |	SSH |	22 |