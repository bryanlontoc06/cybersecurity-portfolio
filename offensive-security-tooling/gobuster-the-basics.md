# 📌 Gobuster: The Basics
## Gobuster: Introduction
**Enumeration**
Enumeration is the act of listing all the available resources, whether they are accessible or not. For example, Gobuster enumerates web directories.

**Brute Force**
Brute force is the act of trying every possibility until a match is found. It is like having ten keys and trying them all on a lock until one fits. Gobuster uses wordlists for this purpose.

The help page contains multiple sections:

Usage: Shows the syntax on how to use the command.

Available Commands: Multiple commands are available to aid us in enumerating directories, files, DNS subdomains, Google Cloud Storage buckets, and Amazon AWS S3 buckets. Throughout this room, we will focus on the dir, dns, and vhost commands. We will cover each of them in the following tasks.

Flags: These are specific options we can configure to customize our commands. Let’s look at the flags we will often use throughout this room:

|Short Flag|	Long Flag|	Description|
|--|--|--|
|-t|	--threads|	This flag configures the number of threads to use for the scan. Each of these threads sends out requests with a slight delay. The default number of threads is 10. This number may be slow when using large wordlists. You can increase or decrease the number of threads depending on the available system resources.|
|-w|	--wordlist|	The flag configures a wordlist to use for iterating. Each wordlist entry is attached to the URL you included in the command.|
||--delay|	This flag defines the amount of time to wait between sending requests. Some web servers include mechanisms to detect enumeration by looking at how many requests are received in a certain period of time. We can increase the delay between subsequent requests to make it look like normal web traffic.|
||--debug|	This flag helps us to troubleshoot when our command gives unexpected errors.|
|-o|	--output|	This flag writes the enumeration results to a file we choose.|

Example
Let us look at an example of how we would use these commands and flags together to enumerate a web directory:

`gobuster dir -u "http://www.example.thm/" -w /usr/share/wordlists/dirb/small.txt -t 64`

gobuster dir indicates that we will use the directory and file enumeration mode.

-u "http://www.example.thm/" tells Gobuster that the target URL is http://example.thm/(opens in new tab).
-w /usr/share/wordlists/dirb/small.txt directs Gobuster to use the small.txt wordlist to brute force the web directories. Gobuster will use each entry in the wordlist to form a new URL and send a GET request to that URL. If the first entry of the wordlist were images, Gobuster would send a GET request to http://example.thm/images/.(opens in new tab)
-t 64 sets the number of threads Gobuster will use to 64. This improves the performance drastically.

|Flag|	Long Flag|	Description|
|--|--|--|
|-c|	--cookies|	This flag configures a cookie to pass along each request, such as a session ID.|
|-x|	--extensions|	This flag specifies which file extensions you want to scan for. E.g., .php, .js|
|-H|	--headers|	This flag configures an entire header to pass along with each request.|
|-k|	--no-tls-validation|	This flag  skips the process that checks the certificate when https is used. It often happens for CTF events or test rooms like the ones on THM a self-signed certificate is used. This causes an error during the TLS check.|
|-n|	--no-status|	You can set this flag when you don’t want to see status codes of each response received. This helps keep the output on the screen clear.|
|-P|	password|	You can set this flag together with the --username flag to execute authenticated requests. This is handy when you have obtained credentials from a user.|
|-s|	--status-codes|	With this flag, you can configure which status codes of the received responses you want to display, such as 200, or a range like 300-400.|
|-b|	--status-codes-blacklist|	This flag allows you to configure which status codes of the received responses you don’t want to display. Configuring this flag overrides the -s flag.|
|-U|	--username|	You can set this flag together with the --password flag to execute authenticated requests. This is handy when you have obtained credentials from a user.|
|-r|	--followredirect|	This flags configures Gobuster to follow the redirect that it received as a response to the sent request. A HTTP redirect status code (e.g., 301 or 302) is used to redirect the client to a different URL.|

This room taught us about the offensive tool Gobuster. This tool enumerates directories, files, DNS subdomains, and virtual hosts.

We have covered three different modes of the Gobuster tool:

dns mode: enumerates dns subdomains.
dir mode: enumerates directories.
vhost mode: enumerates virtual hosts.

For each mode, we covered the required flags to configure and additional optional flags that fine-tune the desired results.

We have highlighted the difference between virtual hosts and subdomains and the way Gobuster scans for these:

dns mode uses the DNS services to scan for subdomains using the configured domain and wordlist.
vhost mode sends web requests using the configured URL and wordlist.

