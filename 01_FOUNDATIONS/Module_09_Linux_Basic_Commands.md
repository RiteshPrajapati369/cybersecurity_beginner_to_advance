# Module 9: Linux Basic Commands
> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 6–8 hours | 📁 Folder: `01_FOUNDATIONS`

---

## Table of Contents

1. [Why Linux Commands Matter in Cybersecurity](#1-why-linux-commands-matter-in-cybersecurity)
2. [Linux File System Navigation](#2-linux-file-system-navigation)
3. [File Management Commands](#3-file-management-commands)
4. [Viewing and Editing Files](#4-viewing-and-editing-files)
5. [File Permissions and Ownership](#5-file-permissions-and-ownership)
6. [Process Management](#6-process-management)
7. [Network Commands](#7-network-commands)
8. [User and Group Management](#8-user-and-group-management)
9. [System Information Commands](#9-system-information-commands)
10. [Searching and Filtering](#10-searching-and-filtering)
11. [Compression and Archiving](#11-compression-and-archiving)
12. [Package Management](#12-package-management)
13. [Hands-On Lab: Linux Command Practice](#13-hands-on-lab-linux-command-practice)
14. [Summary & Key Takeaways](#14-summary--key-takeaways)
15. [Quiz](#15-quiz)
16. [Resources](#16-resources)

---

## 1. Why Linux Commands Matter in Cybersecurity

Linux is the dominant operating system in cybersecurity. Kali Linux, the most popular penetration testing distribution, is built on Linux. Most servers, cloud instances, and security tools run on Linux.

**Without Linux command knowledge, you cannot:**
- Navigate a compromised system during a penetration test
- Read logs to detect intrusions
- Manage files and permissions during forensics
- Run security tools like Nmap, Metasploit, or Wireshark effectively
- Understand attack paths on Linux servers

**This module covers the essential commands you will use daily as a cybersecurity professional.**

---

## 2. Linux File System Navigation

### 2.1 The Linux File System Structure

Linux uses a **hierarchical file system** starting from the root directory (`/`).

```
/
├── bin/         # Essential user binaries (ls, cp, mv)
├── boot/        # Boot loader files (kernel, initrd)
├── dev/         # Device files (hardware, /dev/sda)
├── etc/         # System configuration files
├── home/        # User home directories (/home/user)
├── lib/         # Shared libraries
├── media/       # Mount point for removable media
├── mnt/         # Temporary mount point
├── opt/         # Optional add-on software
├── proc/        # Process information (virtual file system)
├── root/        # Root user's home directory
├── sbin/        # System binaries (fdisk, ifconfig)
├── tmp/         # Temporary files (cleared on reboot)
├── usr/         # User utilities and applications
└── var/         # Variable data (logs, spool files)
```

### 2.2 Navigation Commands

#### `pwd` — Print Working Directory

**What it does:** Shows the current directory you are in.

**Syntax:**
```bash
pwd
```

**Example:**
```bash
kali@kali:~$ pwd
/home/kali
```

**When to use:** Always know where you are before running commands.

---

#### `ls` — List Directory Contents

**What it does:** Lists files and directories in the current or specified location.

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-l` | Long format (permissions, owner, size, date) | `ls -l` |
| `-a` | Show all files (including hidden files starting with `.`) | `ls -a` |
| `-la` | Long format + all files | `ls -la` |
| `-lh` | Human-readable sizes (KB, MB, GB) | `ls -lh` |
| `-R` | Recursive (list subdirectories) | `ls -R` |
| `-t` | Sort by modification time (newest first) | `ls -lt` |
| `-S` | Sort by file size (largest first) | `ls -lS` |

**Examples:**
```bash
# Basic list
kali@kali:~$ ls
Desktop  Documents  Downloads  Music  Pictures

# Long format with all files
kali@kali:~$ ls -la
total 48
drwxr-xr-x 17 kali kali 4096 Jan 15 10:00 .
drwxr-xr-x  4 root root 4096 Jan 14 09:00 ..
-rw-------  1 kali kali  1024 Jan 15 09:30 .bash_history
drwxr-xr-x  2 kali kali 4096 Jan 14 10:00 Desktop
-rw-r--r--  1 kali kali  220 Jan 14 09:00 .bashrc
```

**Understanding `ls -l` output:**
```
-rw-r--r-- 1 kali kali 1024 Jan 15 09:30 .bash_history
│ │ │ │ │   │   │     │    │    │      │
│ │ │ │ │   │   │     │    │    │      └── File name
│ │ │ │ │   │   │     │    │    └────────── Time
│ │ │ │ │   │   │     │    └─────────────── Date
│ │ │ │ │   │   │     └──────────────────── Size (bytes)
│ │ │ │ │   │   └────────────────────────── Group
│ │ │ │ │   └────────────────────────────── Owner
│ │ │ │ └────────────────────────────────── Number of hard links
│ │ │ └──────────────────────────────────── Permissions
```

---

#### `cd` — Change Directory

**What it does:** Move to a different directory.

**Syntax:**
```bash
cd [directory]
```

**Common Usage:**
```bash
# Go to home directory
cd ~
cd          # Same as ~

# Go to root directory
cd /

# Go to previous directory
cd -

# Go up one level
cd ..

# Go up two levels
cd ../..

# Go to specific directory
cd /etc/ssh
cd /home/kali/Desktop
```

---

#### `tree` — Display Directory Tree

**What it does:** Shows the directory structure in a tree format.

**Installation:**
```bash
sudo apt install tree -y
```

**Examples:**
```bash
kali@kali:~$ tree /home/kali/
/home/kali/
├── Desktop
│   └── notes.txt
├── Documents
│   ├── report.pdf
│   └── project
│       └── script.sh
├── Downloads
│   └── tools
│       ├── nmap_scan.txt
│       └── metasploit
│           └── payload
└── Pictures
    └── screenshot.png
```

---

## 3. File Management Commands

### 3.1 `touch` — Create Empty File

**What it does:** Creates an empty file or updates the timestamp of an existing file.

**Syntax:**
```bash
touch [filename]
```

**Examples:**
```bash
# Create empty file
touch notes.txt

# Create multiple files
touch file1.txt file2.txt file3.txt

# Update timestamp of existing file
touch existing_file.txt
```

---

### 3.2 `mkdir` — Make Directory

**What it does:** Creates a new directory.

**Syntax:**
```bash
mkdir [directory_name]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-p` | Create parent directories if they don't exist | `mkdir -p dir1/dir2/dir3` |

**Examples:**
```bash
# Create single directory
mkdir project

# Create nested directories
mkdir -p project/backups/logs

# Create multiple directories
mkdir dir1 dir2 dir3

# Create directory with space in name (use quotes)
mkdir "My Project Files"
```

---

### 3.3 `cp` — Copy Files and Directories

**What it does:** Copies files or directories from source to destination.

**Syntax:**
```bash
cp [options] source destination
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-r` | Recursive (copy directories) | `cp -r folder/ new_folder/` |
| `-i` | Interactive (confirm before overwrite) | `cp -i file1 file2` |
| `-v` | Verbose (show what is being copied) | `cp -v file1 file2` |

**Examples:**
```bash
# Copy file to another location
cp notes.txt /home/kali/Documents/

# Copy file and rename
cp notes.txt backup_notes.txt

# Copy directory recursively
cp -r project/ project_backup/

# Copy multiple files to a directory
cp file1.txt file2.txt file3.txt /home/kali/Downloads/
```

---

### 3.4 `mv` — Move and Rename Files

**What it does:** Moves files to another location or renames them.

**Syntax:**
```bash
mv [source] [destination]
```

**Examples:**
```bash
# Rename file
mv old_name.txt new_name.txt

# Move file to another directory
mv notes.txt /home/kali/Documents/

# Move directory
mv project/ /home/kali/backup/

# Move multiple files
mv file1.txt file2.txt /home/kali/Downloads/
```

---

### 3.5 `rm` — Remove Files and Directories

**What it does:** Deletes files or directories.

**Syntax:**
```bash
rm [options] [target]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-r` | Recursive (delete directories) | `rm -r folder/` |
| `-f` | Force (no confirmation) | `rm -f file.txt` |
| `-rf` | Recursive + Force | `rm -rf folder/` |

> ⚠️ **WARNING:** `rm -rf` is dangerous. It deletes everything without asking. Always double-check before using.

**Examples:**
```bash
# Delete file
rm notes.txt

# Delete file with confirmation
rm -i notes.txt

# Delete directory
rm -r project/

# Force delete (use with caution)
rm -rf temp_folder/
```

---

### 3.6 `find` — Search for Files

**What it does:** Searches for files and directories based on various criteria.

**Syntax:**
```bash
find [path] [options] [expression]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-name` | Search by filename | `find . -name "*.txt"` |
| `-type` | File type (f=file, d=directory) | `find . -type f` |
| `-size` | Search by size | `find . -size +1M` |
| `-mtime` | Modified time (days) | `find . -mtime -7` |
| `-exec` | Execute command on found files | `find . -name "*.tmp" -exec rm {} \;` |

**Examples:**
```bash
# Find all .txt files in current directory
find . -name "*.txt"

# Find all directories
find . -type d

# Find files larger than 10MB
find /home -size +10M

# Find files modified in the last 7 days
find /var/log -mtime -7

# Find files and delete them
find . -name "*.tmp" -exec rm {} \;

# Find files containing "password" in name
find / -name "*password*" 2>/dev/null
```

---

### 3.7 `grep` — Search Inside Files

**What it does:** Searches for text patterns inside files.

**Syntax:**
```bash
grep [options] pattern [file]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-i` | Case-insensitive | `grep -i "password" file.txt` |
| `-r` | Recursive (search directories) | `grep -r "error" /var/log/` |
| `-n` | Show line numbers | `grep -n "error" file.txt` |
| `-v` | Invert (show lines NOT matching) | `grep -v "empty" file.txt` |
| `-c` | Count matches | `grep -c "error" file.txt` |
| `-l` | List filenames with matches | `grep -l "error" *.log` |

**Examples:**
```bash
# Search for "root" in /etc/passwd
grep "root" /etc/passwd

# Case-insensitive search
grep -i "password" config.txt

# Recursive search in /var/log
grep -r "failed" /var/log/

# Search with line numbers
grep -n "ERROR" /var/log/syslog

# Count occurrences
grep -c "root" /etc/passwd

# Search for pattern using regex
grep "^192\.168\." /var/log/auth.log
```

---

## 4. Viewing and Editing Files

### 4.1 `cat` — Concatenate and Display Files

**What it does:** Displays the entire content of a file.

**Syntax:**
```bash
cat [file]
```

**Examples:**
```bash
# Display file
cat /etc/passwd

# Display multiple files
cat file1.txt file2.txt

# Combine files
cat file1.txt file2.txt > combined.txt

# Add line numbers
cat -n /etc/passwd
```

---

### 4.2 `less` — Page Through Files

**What it does:** Displays file content page by page (scrollable).

**Syntax:**
```bash
less [file]
```

**Navigation:**
- `Space` — Next page
- `Enter` — Next line
- `b` — Previous page
- `/pattern` — Search forward
- `?pattern` — Search backward
- `q` — Quit

**Examples:**
```bash
# View large file
less /var/log/syslog

# View with search
less /var/log/auth.log
/search "failed"
```

---

### 4.3 `head` and `tail` — View File Beginning and End

**What they do:** Show first/last lines of a file.

**Syntax:**
```bash
head [options] file
tail [options] file
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-n` | Number of lines | `head -n 20 file.txt` |
| `-f` | Follow (tail only) | `tail -f /var/log/syslog` |

**Examples:**
```bash
# First 10 lines (default)
head /etc/passwd

# First 20 lines
head -n 20 /var/log/syslog

# Last 10 lines
tail /var/log/auth.log

# Follow log in real-time (great for monitoring)
tail -f /var/log/syslog

# Last 50 lines of a log
tail -n 50 /var/log/nginx/access.log
```

---

### 4.4 `nano` — Simple Text Editor

**What it does:** A user-friendly command-line text editor.

**Syntax:**
```bash
nano [file]
```

**Common Shortcuts:**
- `Ctrl + O` — Save file
- `Ctrl + X` — Exit editor
- `Ctrl + W` — Search
- `Ctrl + K` — Cut line
- `Ctrl + U` — Paste

**Examples:**
```bash
# Create/edit file
nano notes.txt

# Edit system file (requires sudo)
sudo nano /etc/hosts
```

---

### 4.5 `vim` — Advanced Text Editor

**What it does:** Powerful modal text editor (steep learning curve but very capable).

**Syntax:**
```bash
vim [file]
```

**Modes:**
- **Command mode** — Default (navigation, delete, copy)
- **Insert mode** — Type text (press `i`)
- **Visual mode** — Select text (press `v`)
- **Command-line mode** — Run commands (press `:`)

**Essential Commands:**
```
# Navigation
h, j, k, l     ← Left, Down, Up, Right
gg              Go to beginning of file
G               Go to end of file

# Editing
i               Enter Insert mode
x               Delete character
dd              Delete line
yy              Copy line
p               Paste below cursor
P               Paste above cursor

# Saving and quitting
:w              Save
:wq             Save and quit
:q!             Quit without saving
:wq!            Force save and quit

# Search
/pattern        Search forward
?pattern        Search backward
n               Next match
N               Previous match
```

**Examples:**
```bash
# Edit file
vim /etc/hosts

# Edit with syntax highlighting (if configured)
vim file.py
```

---

## 5. File Permissions and Ownership

### 5.1 Understanding Linux Permissions

Every file and directory has three sets of permissions:

| User Type | Meaning |
|---|---|
| **Owner (u)** | The user who created the file |
| **Group (g)** | Users in the file's group |
| **Others (o)** | Everyone else |

**Three Permission Types:**

| Permission | File (Meaning) | Directory (Meaning) |
|---|---|---|
| **r (read)** | Read file content | List directory contents |
| **w (write)** | Modify/delete file | Create/delete files in directory |
| **x (execute)** | Run file as program | Enter directory |

**Permission Notation:**

```
-rwxr-xr-x
│││││││││
││││││││└─ Others: execute (x)
│││││││└─ Others: write (-)
││││││└─ Others: read (r)
│││││└─ Group: execute (x)
││││└─ Group: write (-)
│││└─ Group: read (r)
││└─ Owner: execute (x)
│└─ Owner: write (w)
└─ Owner: read (r)
```

**Numeric (Octal) Representation:**

| Value | Permission |
|---|---|---|
| 7 | rwx (4+2+1) |
| 6 | rw- (4+2+0) |
| 5 | r-x (4+0+1) |
| 4 | r-- (4+0+0) |
| 3 | -wx (0+2+1) |
| 2 | -w- (0+2+0) |
| 1 | --x (0+0+1) |
| 0 | --- (0+0+0) |

**Example:** `chmod 755 file.sh`
- Owner: 7 → rwx
- Group: 5 → r-x
- Others: 5 → r-x

---

### 5.2 `chmod` — Change Permissions

**What it does:** Changes file or directory permissions.

**Syntax:**
```bash
chmod [options] permissions file
```

**Symbolic Mode:**
```bash
# Add execute permission for owner
chmod u+x script.sh

# Remove write permission for others
chmod o-w file.txt

# Add read permission for group
chmod g+r file.txt

# Set full permissions for owner, read-execute for others
chmod u=rwx,go=rx script.sh

# Apply recursively to directory
chmod -R 755 /home/kali/scripts/
```

**Numeric Mode:**
```bash
# 755 = rwxr-xr-x
chmod 755 script.sh

# 644 = rw-r--r--
chmod 644 file.txt

# 600 = rw-------
chmod 600 private_key

# 700 = rwx------
chmod 700 private_directory

# Apply recursively
chmod -R 755 /var/www/html/
```

---

### 5.3 `chown` — Change Ownership

**What it does:** Changes the owner and group of a file or directory.

**Syntax:**
```bash
chown [options] owner:group file
```

**Examples:**
```bash
# Change only owner
chown kali file.txt

# Change owner and group
chown kali:kali file.txt

# Change only group
chown :kali file.txt

# Apply recursively
chown -R kali:kali /home/kali/

# Change ownership of system file (requires sudo)
sudo chown root:root /etc/hosts
```

---

### 5.4 Special Permissions

| Permission | Symbol | Effect |
|---|---|---|
| **SUID** | s | Run file with owner's privileges |
| **SGID** | s | Run file with group's privileges |
| **Sticky Bit** | t | Only owner can delete file in directory |

**Examples:**
```bash
# Set SUID (4)
chmod u+s /usr/bin/passwd

# Set SGID (2)
chmod g+s /usr/bin/script

# Set Sticky Bit (1)
chmod +t /tmp

# Combine: 4755 = SUID + rwxr-xr-x
chmod 4755 /usr/bin/sudo
```

---

## 6. Process Management

### 6.1 `ps` — Process Status

**What it does:** Shows running processes.

**Syntax:**
```bash
ps [options]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-e` | All processes | `ps -e` |
| `-f` | Full format | `ps -ef` |
| `-u` | User format | `ps -u kali` |
| `aux` | All processes with details | `ps aux` |

**Examples:**
```bash
# All processes with details
ps aux

# All processes in full format
ps -ef

# Processes for specific user
ps -u root

# Show process tree
ps aux --forest

# Find process by name
ps aux | grep "nginx"
```

**Understanding `ps aux` output:**
```
USER       PID  %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1  22500  1200 ?        Ss   Jan15   0:01 /sbin/init
kali      1234  0.5  2.3 123456 45678 ?        Ssl  Jan15   0:23 /usr/bin/python
```

| Column | Meaning |
|---|---|
| USER | Process owner |
| PID | Process ID |
| %CPU | CPU usage percentage |
| %MEM | Memory usage percentage |
| VSZ | Virtual memory size |
| RSS | Resident memory size |
| TTY | Terminal |
| STAT | Process state |
| START | Start time |
| TIME | CPU time used |
| COMMAND | Command line |

---

### 6.2 `top` and `htop` — Real-Time Process Monitor

**What it does:** Shows running processes in real-time.

**Syntax:**
```bash
top
htop
```

**top Key Shortcuts:**
- `h` — Help
- `q` — Quit
- `k` — Kill process
- `r` — Renice (change priority)
- `P` — Sort by CPU
- `M` — Sort by Memory
- `1` — Show each CPU core

**htop Installation:**
```bash
sudo apt install htop -y
```

**Examples:**
```bash
# Start top
top

# Start htop (more user-friendly)
htop
```

---

### 6.3 `kill` — Terminate Processes

**What it does:** Sends signals to processes (usually to kill them).

**Syntax:**
```bash
kill [options] PID
```

**Common Signals:**

| Signal | Number | Effect |
|---|---|---|
| SIGTERM | 15 | Graceful termination (default) |
| SIGKILL | 9 | Force kill (cannot be ignored) |
| SIGSTOP | 19 | Pause process |
| SIGCONT | 18 | Continue paused process |

**Examples:**
```bash
# Terminate process gracefully
kill 1234

# Force kill process
kill -9 1234

# Kill by name
pkill nginx

# Kill all processes by name
killall firefox

# List all signals
kill -l
```

---

### 6.4 `jobs` and `fg` — Background Processes

**What they do:** Manage background and foreground processes.

**Examples:**
```bash
# Start process in background
nmap -sV 192.168.1.1 &

# List background jobs
jobs

# Bring job to foreground
fg %1

# Bring job to background
bg %1

# Run process in background with nohup (continues after logout)
nohup nmap -sV 192.168.1.1 &
```

---

## 7. Network Commands

### 7.1 `ip` and `ifconfig` — Network Interface Configuration

**What they do:** Display and configure network interfaces.

**`ip` command (modern):**
```bash
# Show all network interfaces
ip a
ip addr show

# Show specific interface
ip a show eth0

# Bring interface up/down
ip link set eth0 up
ip link set eth0 down

# Add IP address
ip addr add 192.168.1.10/24 dev eth0

# Show routing table
ip route show
```

**`ifconfig` command (older):**
```bash
# Show all interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Bring interface up/down
ifconfig eth0 up
ifconfig eth0 down
```

---

### 7.2 `ping` — Test Network Connectivity

**What it does:** Sends ICMP echo requests to test reachability.

**Syntax:**
```bash
ping [options] [host]
```

**Examples:**
```bash
# Ping a host
ping google.com

# Ping with count
ping -c 4 google.com

# Ping with specific size
ping -s 1000 google.com

# Ping with interval
ping -i 0.5 google.com

# Ping broadcast (for discovery)
ping -b 192.168.1.255
```

---

### 7.3 `netstat` and `ss` — Network Statistics

**What they do:** Display network connections, routing tables, and interface statistics.

**`netstat` (older):**
```bash
# All listening ports
netstat -tuln

# All connections
netstat -an

# Display routing table
netstat -r

# Display interface statistics
netstat -i

# Show PID/program name
netstat -tulnp
```

**`ss` (modern, faster):**
```bash
# All listening sockets
ss -tuln

# All connections
ss -an

# Show process info
ss -tunlp

# Filter by port
ss -tunlp | grep :80
```

**Understanding `ss -tuln` output:**
```
Netid  State   Recv-Q  Send-Q  Local Address:Port   Peer Address:Port
tcp    LISTEN  0       128     0.0.0.0:22           0.0.0.0:*
tcp    LISTEN  0       128     127.0.0.1:3306       0.0.0.0:*
udp    UNCONN  0       0       0.0.0.0:68           0.0.0.0:*
```

| Column | Meaning |
|---|---|
| Netid | Protocol (tcp/udp) |
| State | Connection state |
| Recv-Q | Receive queue |
| Send-Q | Send queue |
| Local Address:Port | Listening address |
| Peer Address:Port | Connected address |

---

### 7.4 `nslookup` and `dig` — DNS Lookup

**What they do:** Query DNS servers to resolve domain names to IPs.

**`nslookup`:**
```bash
# Lookup domain
nslookup google.com

# Use specific DNS server
nslookup google.com 8.8.8.8

# Reverse lookup
nslookup 8.8.8.8

# Query specific record type
nslookup -type=MX google.com
```

**`dig` (more detailed):**
```bash
# Basic lookup
dig google.com

# Specific record type
dig google.com A
dig google.com MX
dig google.com NS

# Use specific DNS server
dig @8.8.8.8 google.com

# Short answer
dig google.com +short

# Reverse lookup
dig -x 8.8.8.8
```

---

### 7.5 `curl` — Transfer Data from URLs

**What it does:** Makes HTTP requests and downloads data.

**Syntax:**
```bash
curl [options] [URL]
```

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-O` | Save with original filename | `curl -O http://example.com/file.zip` |
| `-o` | Save with custom filename | `curl -o myfile.zip http://example.com/file.zip` |
| `-L` | Follow redirects | `curl -L http://example.com` |
| `-v` | Verbose (show headers) | `curl -v http://example.com` |
| `-H` | Add custom header | `curl -H "User-Agent: Kali" http://example.com` |
| `-X` | HTTP method | `curl -X POST http://example.com/api` |
| `-d` | Send POST data | `curl -d "name=value" http://example.com` |

**Examples:**
```bash
# Download file
curl -O https://example.com/file.zip

# Save with custom name
curl -o scan_results.txt http://example.com/data

# Show HTTP headers only
curl -I http://example.com

# POST request with data
curl -X POST -d "username=admin&password=pass" http://example.com/login

# Verbose output (shows full HTTP exchange)
curl -v http://example.com

# Download and follow redirects
curl -L -O https://example.com/download/software.iso
```

---

### 7.6 `wget` — Download Files

**What it does:** Non-interactive network downloader.

**Examples:**
```bash
# Download file
wget https://example.com/file.zip

# Download with custom name
wget -O myfile.zip https://example.com/file.zip

# Recursive download
wget -r https://example.com/docs/

# Resume interrupted download
wget -c https://example.com/largefile.iso

# Download and authenticate
wget --user=user --password=pass https://example.com/private/file.txt
```

---

## 8. User and Group Management

### 8.1 `whoami` — Current User

**Examples:**
```bash
kali@kali:~$ whoami
kali
```

### 8.2 `id` — User and Group Information

**Examples:**
```bash
# Current user info
id

# Specific user info
id root

# Output: uid=1000(kali) gid=1000(kali) groups=1000(kali),27(sudo)
```

### 8.3 `sudo` — Execute as Superuser

**What it does:** Runs a command with elevated privileges.

**Examples:**
```bash
# Run command as root
sudo apt update

# Open root shell
sudo su -
sudo -i

# Run command as another user
sudo -u user whoami

# List sudo privileges
sudo -l
```

### 8.4 `useradd` and `userdel` — Add/Delete Users

**Examples:**
```bash
# Create new user
sudo useradd -m -s /bin/bash newuser

# Create user with password
sudo useradd -m -s /bin/bash newuser
sudo passwd newuser

# Delete user
sudo userdel -r newuser
```

### 8.5 `passwd` — Change Password

**Examples:**
```bash
# Change current user password
passwd

# Change another user's password
sudo passwd newuser
```

---

## 9. System Information Commands

### 9.1 `uname` — System Information

**Examples:**
```bash
# Kernel version
uname -r

# All information
uname -a

# OS name
uname -s

# Architecture
uname -m
```

### 9.2 `df` — Disk Space Usage

**Examples:**
```bash
# Disk usage in human-readable format
df -h

# Inodes (file count) usage
df -i

# Specific filesystem
df -h /home
```

### 9.3 `du` — Directory Space Usage

**Examples:**
```bash
# Directory size
du -sh /home/kali/

# Directory size with details
du -h /home/kali/

# Sort by size
du -sh * | sort -h
```

### 9.4 `free` — Memory Usage

**Examples:**
```bash
# Memory usage
free -h

# With swap
free -m

# Show total, used, free, cached
free -h
```

### 9.5 `history` — Command History

**Examples:**
```bash
# Show command history
history

# Repeat last command
!!

# Repeat command number
!123

# Search history
history | grep "nmap"
```

---

## 10. Searching and Filtering

### 10.1 `grep` Advanced Examples

```bash
# Search recursively for "error" in log files
grep -r "error" /var/log/

# Search for IP addresses
grep -E "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" file.txt

# Search for email addresses
grep -E "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" file.txt

# Search for password patterns
grep -i "password\|passwd\|pwd" /var/log/*

# Exclude lines containing "INFO"
grep -v "INFO" /var/log/syslog

# Show context (lines before and after match)
grep -C 3 "error" /var/log/syslog
```

### 10.2 `awk` — Text Processing

**What it does:** Powerful text processing and pattern scanning.

**Syntax:**
```bash
awk 'pattern { action }' file
```

**Examples:**
```bash
# Print first column
awk '{print $1}' /etc/passwd

# Print specific columns
awk '{print $1, $3}' /etc/passwd

# Print lines containing "root"
awk '/root/ {print}' /etc/passwd

# Print with custom separator
awk -F':' '{print $1, $3}' /etc/passwd

# Sum column values
awk '{sum += $3} END {print sum}' numbers.txt

# Print lines where column is greater than value
awk '$3 > 1000' /etc/passwd
```

### 10.3 `sed` — Stream Editor

**What it does:** Performs text replacements and transformations.

**Examples:**
```bash
# Replace text in file (show output)
sed 's/old/new/g' file.txt

# Replace and save to new file
sed 's/old/new/g' file.txt > newfile.txt

# Replace in-place
sed -i 's/old/new/g' file.txt

# Delete lines containing "pattern"
sed '/pattern/d' file.txt

# Print lines 10-20
sed -n '10,20p' file.txt
```

### 10.4 Pipes (`|`) and Redirections

**What they do:** Connect commands to pass output between them.

**Examples:**
```bash
# Pipe: Pass output of one command to another
ls -la | grep "txt"

# Redirect output to file (overwrite)
echo "Hello" > file.txt

# Redirect output to file (append)
echo "World" >> file.txt

# Redirect error output
command 2> error.log

# Redirect both output and error
command &> output.log

# Combine commands
ls -la | grep "txt" | wc -l
```

---

## 11. Compression and Archiving

### 11.1 `tar` — Archive Files

**What it does:** Create and extract tar archives.

**Common Options:**

| Option | Description | Example |
|---|---|---|
| `-c` | Create archive | `tar -cvf archive.tar files/` |
| `-x` | Extract archive | `tar -xvf archive.tar` |
| `-z` | Compress with gzip | `tar -czvf archive.tar.gz files/` |
| `-j` | Compress with bzip2 | `tar -cjvf archive.tar.bz2 files/` |
| `-v` | Verbose | `tar -xvf archive.tar` |
| `-f` | File name | `tar -xf archive.tar` |
| `-t` | List contents | `tar -tvf archive.tar` |

**Examples:**
```bash
# Create tar archive
tar -cvf project.tar project/

# Extract tar archive
tar -xvf project.tar

# Create gzip compressed archive
tar -czvf project.tar.gz project/

# Extract gzip archive
tar -xzvf project.tar.gz

# Extract to specific directory
tar -xzvf project.tar.gz -C /tmp/

# List archive contents
tar -tvf project.tar.gz
```

### 11.2 `zip` and `unzip`

**Examples:**
```bash
# Create zip archive
zip -r project.zip project/

# Extract zip
unzip project.zip

# Extract to specific directory
unzip project.zip -d /tmp/

# List zip contents
unzip -l project.zip
```

---

## 12. Package Management

### 12.1 `apt` — Debian/Ubuntu Package Management

**Examples:**
```bash
# Update package list
sudo apt update

# Upgrade all packages
sudo apt upgrade

# Full upgrade (handles dependencies)
sudo apt full-upgrade

# Install package
sudo apt install nmap

# Install multiple packages
sudo apt install nmap wireshark metasploit-framework

# Remove package
sudo apt remove nmap

# Remove package and configuration
sudo apt purge nmap

# Search for package
apt search nmap

# Show package information
apt show nmap

# List installed packages
apt list --installed

# Clean package cache
sudo apt clean

# Auto-remove unused dependencies
sudo apt autoremove
```

### 12.2 `dpkg` — Low-Level Package Management

**Examples:**
```bash
# Install .deb package
sudo dpkg -i package.deb

# Remove package
sudo dpkg -r package

# List installed packages
dpkg -l

# Show package info
dpkg -s package

# List files in package
dpkg -L package
```

---

## 13. Hands-On Lab: Linux Command Practice

### Objective

Practice essential Linux commands in Kali Linux.

### Lab Setup

- Kali Linux VM (from Module 8)
- Terminal open

### Instructions

**Part 1: Navigation and File Management**

```bash
# 1. Navigate to home directory
cd ~

# 2. Create a new directory
mkdir cybersecurity_lab

# 3. Navigate into it
cd cybersecurity_lab

# 4. Create files
touch notes.txt scan_results.txt targets.txt

# 5. Create nested directory
mkdir -p reports/nmap

# 6. Copy file
cp notes.txt reports/notes_backup.txt

# 7. Rename file
mv targets.txt targets_list.txt

# 8. List everything
ls -la
```

**Part 2: File Permissions**

```bash
# 1. Create script
echo '#!/bin/bash' > myscript.sh
echo 'echo "Hello, World!"' >> myscript.sh

# 2. View permissions
ls -l myscript.sh

# 3. Make executable
chmod +x myscript.sh

# 4. Run it
./myscript.sh

# 5. Set specific permissions
chmod 755 myscript.sh

# 6. Change ownership (if root)
sudo chown root:root myscript.sh
```

**Part 3: Process Management**

```bash
# 1. List all processes
ps aux

# 2. Find specific process
ps aux | grep "bash"

# 3. Monitor processes
htop

# 4. Run process in background
sleep 300 &

# 5. List background jobs
jobs

# 6. Kill process
# Find PID and: kill PID
```

**Part 4: Network Commands**

```bash
# 1. Check IP address
ip a

# 2. Test connectivity
ping -c 4 google.com

# 3. Check listening ports
ss -tuln

# 4. DNS lookup
dig google.com

# 5. Download file
curl -O https://example.com/file.txt
```

**Part 5: Text Processing**

```bash
# 1. View system log (first/last)
head -n 20 /var/log/syslog
tail -n 20 /var/log/syslog

# 2. Search in logs
grep "error" /var/log/syslog

# 3. Count occurrences
grep -c "error" /var/log/syslog

# 4. Extract specific fields
awk '{print $1, $2}' /var/log/syslog

# 5. Pipeline
ps aux | grep "root" | wc -l
```

**Part 6: Archive Files**

```bash
# 1. Archive directory
tar -czvf lab_backup.tar.gz cybersecurity_lab/

# 2. Check archive
tar -tvf lab_backup.tar.gz

# 3. Extract archive
tar -xzvf lab_backup.tar.gz -C /tmp/
```

### Deliverable

Create `lab_09_linux_commands.md`:

# Lab 9: Linux Command Practice

## Completed Commands
- [ ] Navigation commands (pwd, ls, cd)
- [ ] File management (touch, mkdir, cp, mv, rm)
- [ ] File permissions (chmod, chown)
- [ ] Process management (ps, top, kill)
- [ ] Network commands (ip, ping, ss, curl)
- [ ] Text processing (grep, awk, sed)
- [ ] Compression (tar)

## My Commands Used
[Paste the commands you ran]

## Issues Encountered
[Any errors or issues and how you fixed them]

## New Skills Learned
[List 3 things you learned]
---

## 14. Summary & Key Takeaways

| Category | Key Commands |
|---|---|
| **Navigation** | `pwd`, `ls`, `cd` |
| **File Management** | `touch`, `mkdir`, `cp`, `mv`, `rm`, `find` |
| **File Viewing** | `cat`, `less`, `head`, `tail` |
| **Permissions** | `chmod`, `chown` |
| **Processes** | `ps`, `top`, `htop`, `kill` |
| **Network** | `ip`, `ping`, `ss`, `curl`, `wget` |
| **DNS** | `nslookup`, `dig` |
| **Text Processing** | `grep`, `awk`, `sed` |
| **Archiving** | `tar`, `zip` |
| **Package** | `apt`, `dpkg` |

---

## 15. Quiz

1. What command shows your current directory?

2. How do you list all files (including hidden) with details?

3. What command creates an empty file?

4. How do you make a file executable?

5. What command shows all running processes?

6. How do you check which ports are listening?

7. What command searches for text inside files?

8. How do you display disk usage in human-readable format?

<details>
<summary>📋 Click to reveal answers</summary>

1. `pwd`

2. `ls -la`

3. `touch filename`

4. `chmod +x filename`

5. `ps aux` or `ps -ef`

6. `ss -tuln` or `netstat -tuln`

7. `grep pattern file`

8. `df -h`

</details>

---

## 16. Resources

### Documentation
- [Linux Command Line Cheat Sheet](https://www.cheatography.com/davechild/cheat-sheets/linux-command-line/)
- [Linux Journey](https://linuxjourney.com)
- [Explain Shell](https://explainshell.com)

### Practice
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- [TryHackMe Linux Room](https://tryhackme.com/room/linuxfundamentals)
- [Linux Commands Practice](https://www.hackerrank.com/domains/shell)

### Books
- Linux Command Line (William Shotts) — Free online

---

**Previous Module:** [Module 8 — Virtualization & Home Lab Setup](Module_08_Virtualization_and_Home_Lab.md)

**Next Module:** [Module 10 — OSINT Part 1](../02_RECONNAISSANCE_&_OSINT/Module_10_OSINT_Part_1.md)

---

*Module 9 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
