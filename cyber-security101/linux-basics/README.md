# 🐧 Linux CLI Basics

## 🎯 Objective

This repository documents my foundational learning in cybersecurity, focusing on:

- Linux CLI fundamentals
- File system navigation and management
- Basic networking commands

📁 This serves as my starting portfolio as I progress toward Application Security and DevSecOps roles.

---

### 📌 File System Navigation

```bash
pwd
ls
cd
```

#### Practice:

- Navigated Linux directories `(/home, /etc, /var)`
- Listed files and hidden files using ls -a

### 📌 File & Directory Management

```bash
mkdir
touch
rm
cp
mv
```

#### Practice:

- Created and organized directories
- Created and deleted test files
- Moved and copied files between folders

### 📌 File Permissions

```bash
chmod
chown
ls -l
```

#### Practice:

- Modified file permissions (e.g., 755, 644)
- Viewed file ownership and access control

### 📌 Networking Basics

```bash
ip a
ping
curl
wget
netstat
```

#### Practice:

- Checked IP configuration
- Tested network connectivity
- Sent HTTP requests using curl

### 📌 Type of Linux Shells

- `Bourne Again Shell (Bash)` - is the default shell for most Linux distributions.
- `Friendly Interactive Shell (Fish)` is also not default in most Linux distributions.
- `Z Shell (Zsh)` is not installed by default in most Linux distributions

### 📌 Shell Scripting and Components

#### Practice:
`Variable`
```bash
# Defining the Interpreter
#!/bin/bash
echo "Hey, what’s your name?"
read name
echo "Welcome, $name"
```

`Loops`
```bash
# Defining the Interpreter
#!/bin/bash
for i in {1..10};
do
echo $i
done
```

`Conditional Statements`
```bash
# Defining the Interpreter
#!/bin/bash
echo "Please enter your name first:"
read name
if [ "$name" = "Stewart" ]; then
        echo "Welcome Stewart! Here is the secret: THM_Script"
else
        echo "Sorry! You are not authorized to access the secret."
fi
```




### 🧠 Key Learnings

- Linux CLI fundamentals
- File system navigation and manipulation
- File permissions and access control concepts
- Basic networking and system inspection
