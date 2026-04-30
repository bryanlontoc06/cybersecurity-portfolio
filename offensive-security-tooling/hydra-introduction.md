# 📌 Hydra Introduction

What is **Hydra**?
**Hydra** is a brute force online password cracking program, a quick system login password “hacking” tool.

**Hydra Commands**
The options we pass into Hydra depend on which service (protocol) we’re attacking. For example, if we wanted to brute force FTP with the username being user and a password list being passlist.txt, we’d use the following command:

`hydra -l user -P passlist.txt ftp://10.49.143.18`

For this deployed machine, here are the commands to use Hydra on SSH and a web form (POST method).

**SSH**
`hydra -l <username> -P <full path to pass> 10.49.143.18 -t 4 ssh`

|Option|	Description|
|--|--|
|-l|	specifies the (SSH) username for login|
|-P|	indicates a list of passwords|
|-t|	sets the number of threads to spawn|

For example, hydra -l root -P passwords.txt 10.49.143.18 -t 4 ssh will run with the following arguments:

Hydra will use root as the username for ssh
It will try the passwords in the passwords.txt file
There will be four threads running in parallel as indicated by -t 4
Post Web Form
We can use Hydra to brute force web forms, too. You must know which type of request it is making; GET or POST methods are commonly used. You can use your browser’s network tab (in developer tools) to see the request types or view the source code.

`sudo hydra <username> <wordlist> 10.49.143.18 http-post-form "<path>:<login_credentials>:<invalid_response>"`

|Option|	Description|
|--|--|
|-l|	the username for (web form) login|
|-P|	the password list to use|
|http-post-form|	The type of the form is POST|
|<path>|	the login page URL, for example, login.php|
|<login_credentials>|	the username and password used to log in, for example, `username=^USER^&password=^PASS^`|
|<invalid_response>|	part of the response when the login fails|
|-V|	verbose output for every attempt|

Below is a more concrete example Hydra command to brute force a POST login form:

`hydra -l <username> -P <wordlist> 10.49.143.18 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -V`

The login page is only /, i.e., the main IP address.
The username is the form field where the username is entered
The specified username(s) will replace ^USER^
The password is the form field where the password is entered
The provided passwords will be replacing ^PASS^
Finally, F=incorrect is a string that appears in the server reply when the login fails
On a side note, if the web server is listening on a non-default port number, you can explicitly specify the port number using -s <port>, for example:

`hydra -l <username> -P <wordlist> 10.49.143.18 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect" -s <port> -V`

For Hands-on DEMOs, please refer to:
cybersecurity-portfolio/README.md
### Authentication Security Testing: Burp Suite Interception and Hydra Brute Force Simulation
https://www.youtube.com/watch?v=w30ETtKzKcY


Existing Web Shells Available Online

The power of supported languages by the web servers can result in web shells with lots of functionality and avoid detection at the same time. Let's explore some of the most popular web shells that can be found online

[p0wny-shell](https://github.com/flozz/p0wny-shell) - A minimalistic single-file PHP web shell that allows remote command execution.
The image is a screenshot of the web shell p0wny-shell showing commands being executed in a GUI similar to a real terminal

[b374k shell](https://github.com/b374k/b374k) - A more feature-rich PHP web shell with file management and command execution, among other functionalities.
The image is a screenshot of b374k shell displaying the file manager feature that allows to manipulate files

[c99 shell](https://www.r57shell.net/single.php?id=13) - A well-known and robust PHP web shell with extensive functionality.
The image is a screenshot of  c99 shell displaying the command execution feature and the file manipulation one

You can find more web shells at: https://www.r57shell.net/index.php.