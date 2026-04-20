# 🪟 Windows CLI Basics

## 🎯 Objective

This repository documents my foundational learning in cybersecurity, focusing on:

- Windows CLI basics
- File system navigation and management
- Basic networking commands

📁 This serves as my starting portfolio as I progress toward Application Security and DevSecOps roles.

---

### 📌 File Management (CMD / PowerShell)

```bash
cd
dir
mkdir
del
copy
move
```

#### Practice:

Navigated Windows directories
Created and managed files and folders

### 📌 System Information

```bash
systeminfo
ipconfig
whoami
```

#### Practice:

- Retrieved system information
- Checked network configuration
- Identified current user context

### 📌 Networking Commands

```bash
ping
tracert
netstat
```

#### Practice:

- Tested network connectivity
- Traced network routes
- Viewed active connections

### 📌 Task and Process Management

```bash
tasklist
tasklist /FI "imagename eq sshd.exe"
```

#### Practice:

- Viewed running processes

# 🪟 Windows Powershell CLI Basics

- commands are known as `cmdlets` (pronounced `command-lets`). They are much more powerful than the traditional Windows commands and allow for more advanced data manipulation.

### 📌 Basic Syntax: Verb-Noun Command

```bash
Get-Content
Set-Location
Get-Command
-CommandType "Function"
Get-Help
Get-Alias
```

#### Practice:

- Retrieves (gets) the content of a file and displays it in the console.
- Changes (sets) the current working directory.
- List all available cmdlets, functions, aliases, and scripts that can be executed in the current PowerShell session
- Display only the available commands of type “function” `Get-Command -CommandType "Function"`
- provides detailed information about cmdlets, including usage, parameters, and examples
- Lists all aliases available.

### 📌 Find and Download Cmdlets

Cmdlet -Property "pattern\*".

```bash
Find-Module -Name "PowerShell*"
Install-Module -Name "PowerShellGet"
```

### 📌 Navigating the File System and Working with Files

```bash
Get-ChildItem
Set-Location
New-Item
Remove-Item
Copy-Item
Get-Content
```

#### Practice:

- lists the files and directories in a location specified with the -Path parameter
- navigate to a different directory,
- create an item in PowerShell.
- read and display the contents of a file

#### Example:

```bash
PS C:\Users\captain> Set-Location -Path ".\Documents"
PS C:\Users\captain\Documents>
```

```bash
PS C:\Users\captain\Documents> New-Item -Path ".\captain-cabin\captain-wardrobe" -ItemType "Directory"

    Directory: C:\Users\captain\Documents\captain-cabin

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----          9/4/2024  12:20 PM                captain-wardrobe

PS C:\Users\captain\Documents> New-Item -Path ".\captain-cabin\captain-wardrobe\captain-boots.txt" -ItemType "File"

    Directory: C:\Users\captain\Documents\captain-cabin\captain-wardrobe

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          9/4/2024  11:46 AM              0 captain-boots.txt
```

### 📌 Piping, Filtering, and Sorting Data

`Piping` is a technique used in command-line environments that allows the output of one command to be used as the input for another. This creates a sequence of operations where the data flows from one command to the next. Represented by the | symbol, piping is widely used in the Windows CLI, as introduced earlier in this module, as well as in Unix-based shells.

#### Example:

`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Sort-Object Length`
`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"`
`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Select-Object Name,Length`

-ne: "not equal". This operator can be used to exclude objects from the results based on specified criteria.

-gt: "greater than". This operator will filter only objects which exceed a specified value. It is important to note that this is a strict comparison, meaning that objects that are equal to the specified value will be excluded from the results.

-ge: "greater than or equal to". This is the non-strict version of the previous operator. A combination of -gt and -eq.

-lt: "less than". Like its counterpart, "greater than", this is a strict operator. It will include only objects which are strictly below a certain value.

-le: "less than or equal to". Just like its counterpart -ge, this is the non-strict version of the previous operator. A combination of -lt and -eq.

`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Where-Object -Property "Name" -like "ship*" `

### 📌 Piping, Filtering, and Sorting Data

`Piping` is a technique used in command-line environments that allows the output of one command to be used as the input for another. This creates a sequence of operations where the data flows from one command to the next. Represented by the | symbol, piping is widely used in the Windows CLI, as introduced earlier in this module, as well as in Unix-based shells.

```bash
Get-ChildItem
```

#### Example:

`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Sort-Object Length`
`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Where-Object -Property "Extension" -eq ".txt"`

-ne: "not equal". This operator can be used to exclude objects from the results based on specified criteria.

-gt: "greater than". This operator will filter only objects which exceed a specified value. It is important to note that this is a strict comparison, meaning that objects that are equal to the specified value will be excluded from the results.

-ge: "greater than or equal to". This is the non-strict version of the previous operator. A combination of -gt and -eq.

-lt: "less than". Like its counterpart, "greater than", this is a strict operator. It will include only objects which are strictly below a certain value.

-le: "less than or equal to". Just like its counterpart -ge, this is the non-strict version of the previous operator. A combination of -lt and -eq.

`PS C:\Users\captain\Documents\captain-cabin> Get-ChildItem | Where-Object -Property "Name" -like "ship*" `

### 📌 Piping, Filtering, and Sorting Data

```bash
Get-ComputerInfo
Get-LocalUser
Get-NetIPConfiguration
Get-NetIPAddress
```

### 📌 Piping, Filtering, and Sorting Data

```bash
Get-Process
Get-Service
Get-NetTCPConnection
Get-FileHash
Get-FileHash -Path .\ship-flag.txt
Get-Item -Path "C:\House\house_log.txt" -Stream *
```

#### Practice:

- provides a detailed view of all currently running processes, including CPU and memory usage, making it a powerful tool for monitoring and troubleshooting.
- retrieve information about the status of services on the machine, such as which services are running, stopped, or paused.
- displays current TCP connections, giving insights into both local and remote endpoints
- generate file hashes, which is particularly valuable in incident response, threat hunting, and malware analysis, as it helps verify file integrity and detect potential tampering

### 📌 Scripting

`Scripting` is the process of writing and executing a series of commands contained in a text file, known as a script, to automate tasks that one would generally perform manually in a shell, like PowerShell.

```bash
Invoke-Command
Get-Help Invoke-Command -examples
Invoke-Command -ComputerName RoyalFortune -ScriptBlock { Get-Service }
```

For `blue team` professionals such as incident responders, malware analysts, and threat hunters, PowerShell scripts can automate many different tasks, including log analysis, detecting anomalies, and extracting indicators of compromise (IOCs). These scripts can also be used to reverse-engineer malicious code (malware) or automate the scanning of systems for signs of intrusion.

For the `red team`, including penetration testers and ethical hackers, PowerShell scripts can automate tasks like system enumeration, executing remote commands, and crafting obfuscated scripts to bypass defences. Its deep integration with all types of systems makes it a powerful tool for simulating attacks and testing systems’ resilience against real-world threats.

`Invoke-Command` is essential for executing commands on remote systems, making it fundamental for system administrators, security engineers and penetration testers.

### 🧠 Key Learnings

- Windows CLI & Powershell fundamentals
- File system navigation and manipulation
- File permissions and access control concepts
- Basic networking and system inspection
- Navigating the File System and Working with Files
- Piping, Filtering, and Sorting Data
