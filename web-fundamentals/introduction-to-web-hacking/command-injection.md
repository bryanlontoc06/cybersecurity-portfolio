# 📌 Command Injection


## Exploiting Command Injection

Command Injection can be detected in mostly one of two ways:

- Blind command injection
- Verbose command injection

|Method|	Description|
|--|--|
|Blind|	This type of injection is where there is no direct output from the application when testing payloads. You will have to investigate the behaviours of the application to determine whether or not your payload was successful.
|Verbose| This type of injection is where there is direct feedback from the application once you have tested a payload. For example, running the whoami command to see what user the application is running under. The web application will output the username on the page directly.|

**Detecting Blind Command Injection**

Blind command injection is when command injection occurs; however, there is no output visible, so it is not immediately noticeable. For example, a command is executed, but the web application outputs no message.

For this type of command injection, we will need to use payloads that will cause some time delay. For example, the ping and sleep commands are significant payloads to test with. Using ping as an example, the application will hang for x seconds in relation to how many pings you have specified.

Useful payloads


**Linux**

|Payload|	Description|
|--|--|
|whoami|	See what user the application is running under.|
ls| List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.|
ping| This command will invoke the application to hang. This will be useful in testing an application for blind command injection.|
|sleep|	This is another useful payload in testing an application for blind command injection, where the machine does not have ping installed.|
|nc|	Netcat can be used to spawn a reverse shell onto the vulnerable application. You can use this foothold to navigate around the target machine for other services, files, or potential means of escalating privileges.|


**Windows**

|Payload|	Description|
|--|--|
|whoami|	See what user the application is running under.
|dir|	List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.|
|ping| This command will invoke the application to hang. This will be useful in testing an application for blind command injection.|
timeout| This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the ping command is not installed.|


## Remediating Command Injection

**Vulnerable Functions**

In PHP, many functions interact with the operating system to execute commands via shell; these include:

- Exec
- Passthru
- System


**Input sanitisation**

Sanitising any input from a user that an application uses is a great way to prevent command injection. This is a process of specifying the formats or types of data that a user can submit. For example, an input field that only accepts numerical data or removes any special characters such as > , & and /.


**Bypassing Filters**

Applications will employ numerous techniques in filtering and sanitising data that is taken from a  user's input. These filters will restrict you to specific payloads; however, we can abuse the logic behind an application to bypass these filters. For example, an application may strip out quotation marks; we can instead use the hexadecimal value of this to achieve the same result.