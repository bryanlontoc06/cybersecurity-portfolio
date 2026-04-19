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

### 🧠 Key Learnings

- Windows CLI & Powershell fundamentals
- File system navigation and manipulation
- File permissions and access control concepts
- Basic networking and system inspection
