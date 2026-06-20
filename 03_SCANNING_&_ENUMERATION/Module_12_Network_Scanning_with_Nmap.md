# Module 12: Network Scanning with Nmap

> **Phase 3 — Scanning & Enumeration**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `03_SCANNING`

---

## Table of Contents

1. [Nmap Fundamentals](#1-nmap-fundamentals)
2. [Host Discovery (Ping Sweeps)](#2-host-discovery-ping-sweeps)
3. [Port Scanning Techniques](#3-port-scanning-techniques)
4. [Service and Version Detection](#4-service-and-version-detection)
5. [OS Fingerprinting](#5-os-fingerprinting)
6. [Nmap Scripting Engine (NSE)](#6-nmap-scripting-engine-nse)
7. [Timing and Performance](#7-timing-and-performance)
8. [Output Formats](#8-output-formats)
9. [Masscan (Ultra-Fast Scanning)](#9-masscan-ultra-fast-scanning)
10. [Zenmap (GUI for Nmap)](#10-zenmap-gui-for-nmap)
11. [Hands-On Lab: Full Network Enumeration](#11-hands-on-lab-full-network-enumeration)
12. [Summary & Key Takeaways](#12-summary--key-takeaways)
13. [Quiz](#13-quiz)
14. [Resources](#14-resources)

---

## 1. Nmap Fundamentals

**Nmap (Network Mapper)** is the most widely used network discovery and security auditing tool. It is the foundation of all network reconnaissance.

### What Nmap Can Do

| Capability | Description |
|---|---|
| **Host Discovery** | Find live hosts on a network |
| **Port Scanning** | Identify open ports and services |
| **Version Detection** | Identify software versions running on open ports |
| **OS Fingerprinting** | Determine operating system of remote host |
| **Script Scanning** | Run custom scripts for vulnerability detection |
| **Firewall Evasion** | Bypass firewalls and IDS/IPS |

### Installation

**Linux (Ubuntu/Debian/Kali):**
_It was pre-installed tool in kali linux but in case if it was not installed the can be install using given commands_
```bash
sudo apt update
sudo apt install nmap -y
```
**macOS:**
```bash
brew install nmap
```

**Windows:**
1. Download from https://nmap.org/download.html
2. Run installer
3. Add to PATH

**Verify installation:**
```bash
nmap --version
```

### Basic Syntax

```bash
nmap [scan type] [options] [target]
```

**Examples:**
```bash
nmap 192.168.1.1                    # Scan single IP
nmap 192.168.1.1-20                 # Scan IP range
nmap 192.168.1.0/24                 # Scan CIDR range
nmap example.com                    # Scan domain
nmap -iL targets.txt                # Scan from file
```

### Nmap Help

**Quick help:**
```bash
nmap -h
```

**Full manual:**
```bash
man nmap
```

---

## 2. Host Discovery (Ping Sweeps)

Host discovery determines which hosts are alive on a network before performing deeper scans.

### Why Host Discovery Matters

Scanning every port on every IP in a /24 network would take hours. Host discovery reduces the scope to live hosts only.

| Method | Command | Description | Best For |
|---|---|---|---|
| **Ping Sweep** | `-sn` | ICMP echo + TCP SYN to 443 + TCP ACK to 80 | Fast live host detection |
| **No Ping** | `-Pn` | Skip host discovery; treat all hosts as live | Firewalled networks |
| **TCP SYN Ping** | `-PS` | Send SYN packet to specified ports | Bypassing ICMP block |
| **TCP ACK Ping** | `-PA` | Send ACK packet to specified ports | Alternative to SYN |
| **UDP Ping** | `-PU` | Send UDP packet to specified ports | UDP-based detection |
| **SCTP Ping** | `-PY` | Send SCTP packet | Specialized networks |

---

### 2.1 ICMP Ping Sweep (`-sn`)

**Command:**
```bash
nmap -sn 192.168.56.0/24
```

**What it does:**
- Sends ICMP Echo Request (ping) to each IP
- Also sends TCP SYN to port 443 (HTTPS)
- Also sends TCP ACK to port 80 (HTTP)
- Sends ICMP Timestamp Request

**Example Output:**
```
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-15 10:00
Nmap scan report for 192.168.56.1
Host is up (0.0012s latency).
Nmap scan report for 192.168.56.10
Host is up (0.0008s latency).
Nmap scan report for 192.168.56.20
Host is up (0.0021s latency).
Nmap scan report for 192.168.56.30
Host is up (0.0015s latency).
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.35 seconds
```

**What the Output Tells You:**
- `192.168.56.1` - Live (likely your host)
- `192.168.56.10` - Live (Kali VM)
- `192.168.56.20` - Live (Windows 10 VM)
- `192.168.56.30` - Live (Ubuntu Server)
- 252 IPs are dead or not responding

**When to Use:** When you need a quick list of live hosts. This is the fastest discovery method.

---

### 2.2 Skip Host Discovery (`-Pn`)

**Command:**
```bash
nmap -Pn 192.168.56.20
```

**What it does:** Assumes the host is up and proceeds to port scanning. Does NOT send any ping probes.

**When to Use:**
- The host is behind a firewall that blocks ICMP
- The host is configured to ignore ping requests
- You already know the host is up
- You want to scan without any delay

**Example:**
```bash
nmap -Pn 192.168.56.20
```

**Output:**
```
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-15 10:05
Nmap scan report for 192.168.56.20
Host is up (0.0010s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
```

> ⚠️ **Note:** `-Pn` skips host discovery entirely and will scan every IP specified, even if it's offline. This is slower but necessary for firewalled networks.

---

### 2.3 TCP SYN Ping (`-PS`)

**Command:**
```bash
nmap -PS22,80,443 192.168.56.0/24
```

**What it does:** Sends a TCP SYN packet to specified ports. If the host responds with SYN-ACK, it's up. If it responds with RST, it's up but port is closed.

**Example Output:**
```
Nmap scan report for 192.168.56.20
Host is up (0.0012s latency).
```

**When to Use:** When ICMP is blocked but TCP is allowed (most networks allow TCP traffic).

---

### 2.4 TCP ACK Ping (`-PA`)

**Command:**
```bash
nmap -PA80,443 192.168.56.0/24
```

**What it does:** Sends a TCP ACK packet to specified ports. A RST response indicates the host is up.

**When to Use:** Alternative to SYN ping when SYN probes are blocked.

---

### 2.5 UDP Ping (`-PU`)

**Command:**
```bash
nmap -PU53,161 192.168.56.0/24
```

**What it does:** Sends a UDP packet to specified ports. ICMP port unreachable responses indicate the host is up.

**When to Use:** When TCP and ICMP are both blocked but UDP is allowed.

---

### Host Discovery Summary

| Command | Speed | Accuracy | Best For |
|---|---|---|---|
| `-sn` | Fastest | High | Standard networks |
| `-Pn` | Slowest | Assumes all up | Firewalled networks |
| `-PS` | Fast | High | TCP-enabled networks |
| `-PA` | Fast | High | TCP ACK allowed |
| `-PU` | Slow | Medium | UDP networks |

---

## 3. Port Scanning Techniques

Port scanning identifies which ports are open and listening for connections. This is the most important phase of network reconnaissance.

### Port States

| State | Meaning | What It Looks Like |
|---|---|---|
| **Open** | An application is listening | Service responds |
| **Closed** | No application listening | RST (TCP) or ICMP port unreachable |
| **Filtered** | Firewall/IDS blocking | No response or ICMP unreachable |
| **Unfiltered** | Firewall not blocking | RST response |
| **Open/Filtered** | Can't determine | No response (could be open or filtered) |
| **Closed/Filtered** | Can't determine | ICMP unreachable |

---

### 3.1 TCP SYN Scan (`-sS`) — The Most Common

**Command:**
```bash
nmap -sS 192.168.56.20
```

**What it does:**
- Sends TCP SYN packet to target port
- If SYN-ACK returns → port is **open**
- If RST returns → port is **closed**
- If no response → port is **filtered**

**Why Use SYN Scan:**
- Fast
- Stealthy (doesn't complete handshake)
- Less likely to be logged by applications

**Example Output:**
```
Starting Nmap 7.94 at 2024-01-15 10:10
Nmap scan report for 192.168.56.20
Host is up (0.0010s latency).
Not shown: 995 closed ports
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
3306/tcp open     mysql
3389/tcp open     ms-wbt-server
```

**Explanation of Results:**
- `22/tcp open ssh` → SSH server is listening
- `80/tcp open http` → Web server is running
- `443/tcp open https` → HTTPS server running
- `3306/tcp open mysql` → MySQL database accessible
- `3389/tcp open ms-wbt-server` → RDP open (Windows)

**The 3-Way Handshake for SYN Scan:**
```
Kali (attacker)              Target
      |                         |
      |---- SYN --------------->|  (Port 22)
      |                         |
      |<--- SYN-ACK ------------|  (Port is OPEN)
      |                         |
      |---- RST --------------->|  (Terminate connection)
```

**Why RST:** By sending RST instead of ACK, we never complete the handshake, so the application doesn't log the connection attempt.

> ✅ **Best practice:** Always use SYN scan `-sS` unless you need something specific. It's fast, accurate, and stealthy.

---

### 3.2 TCP Connect Scan (`-sT`)

**Command:**
```bash
nmap -sT 192.168.56.20
```

**What it does:**
- Performs full TCP 3-way handshake
- SYN → SYN-ACK → ACK
- If connection completes, port is open
- If RST received, port is closed

**The 3-Way Handshake for Connect Scan:**
```
Kali (attacker)              Target
      |                         |
      |---- SYN --------------->|  (Port 22)
      |                         |
      |<--- SYN-ACK ------------|  (Port is OPEN)
      |                         |
      |---- ACK --------------->|  (Complete handshake)
      |                         |
      |---- RST/ACK ----------->|  (Close connection)
```

**When to Use:**
- When you don't have raw socket privileges (non-root user)
- When SYN scan doesn't work (some firewalls)

**Trade-off:** Connect scan is slower and more likely to be logged by applications.

---

### 3.3 UDP Scan (`-sU`)

**Command:**
```bash
nmap -sU 192.168.56.20
```

**What it does:**
- Sends UDP packet to target port
- If port is open → service responds (or no response)
- If ICMP port unreachable → port is closed
- If no response → port is open or filtered

**Example Output:**
```
Starting Nmap 7.94 at 2024-01-15 10:15
Nmap scan report for 192.168.56.20
Host is up (0.0010s latency).
Not shown: 99 closed ports
PORT     STATE         SERVICE
53/udp   open|filtered domain
123/udp  open|filtered ntp
137/udp  open          netbios-ns
```

**Explanation of Results:**
- `53/udp open|filtered domain` → DNS might be open but no response (normal)
- `123/udp open|filtered ntp` → NTP may be open
- `137/udp open netbios-ns` → NetBIOS name service is open (common on Windows)

**When to Use:**
- To find UDP services (DNS, SNMP, DHCP, NTP)
- To discover uncommon UDP ports

> ⚠️ **Warning:** UDP scanning is slow and unreliable. Many UDP ports return no response even when open.

---

### 3.4 TCP Null Scan (`-sN`)

**Command:**
```bash
nmap -sN 192.168.56.20
```

**What it does:** Sends a TCP packet with NO flags set.

**How it works:**
- According to RFC 793, if a port is closed, it should respond with RST
- If port is open, it should ignore the packet (no response)

**Example Output:**
```
PORT     STATE         SERVICE
80/tcp   open|filtered http
443/tcp  open|filtered https
```

**When to Use:**
- To bypass firewalls that don't understand null packets
- As part of firewall testing

**Limitation:** Many modern firewalls ignore invalid packets, resulting in "open|filtered" states.

---

### 3.5 TCP FIN Scan (`-sF`)

**Command:**
```bash
nmap -sF 192.168.56.20
```

**What it does:** Sends a TCP packet with only the FIN flag set.

**How it works:** Same principle as Null scan. Closed ports respond with RST; open ports ignore.

**When to Use:** Similar to Null scan.

---

### 3.6 TCP Xmas Scan (`-sX`)

**Command:**
```bash
nmap -sX 192.168.56.20
```

**What it does:** Sends a TCP packet with FIN, PSH, and URG flags set (illuminated like a Christmas tree).

**How it works:** Same principle. Closed ports respond with RST; open ports ignore.

**Example Output:**
```
PORT     STATE         SERVICE
443/tcp  open|filtered https
```

> 💡 **Fun fact:** The Xmas scan is named after the Xmas tree because it lights up all the flag bits. It's a cool name and an effective technique.

---

### 3.7 TCP ACK Scan (`-sA`)

**Command:**
```bash
nmap -sA 192.168.56.20
```

**What it does:** Sends TCP ACK packet to target.

**Purpose:**
- Not to identify open ports, but to **map firewall rules**
- If RST comes back → port is **unfiltered** (no firewall blocking)
- If no response → port is **filtered** (firewall blocking)

**Example Output:**
```
PORT     STATE      SERVICE
22/tcp   unfiltered ssh
80/tcp   unfiltered http
443/tcp  unfiltered https
```

**Explanation:** The firewall is not blocking these ports.

**When to Use:** When you need to map firewall rules before exploiting.

---

### 3.8 Port Scanning Techniques Comparison

| Technique | Command | Stealth | Speed | Best For |
|---|---|---|---|---|
| SYN Scan | `-sS` | High | Fast | Most scans |
| Connect Scan | `-sT` | Low | Medium | Non-root users |
| UDP Scan | `-sU` | Medium | Slow | UDP services |
| Null Scan | `-sN` | High | Fast | Firewall evasion |
| FIN Scan | `-sF` | High | Fast | Firewall evasion |
| Xmas Scan | `-sX` | High | Fast | Firewall evasion |
| ACK Scan | `-sA` | High | Fast | Firewall mapping |

---

## 4. Service and Version Detection (`-sV`)

Service detection identifies exactly what software is running on each open port, including the version number.

### What Version Detection Reveals

| Information | Example |
|---|---|
| **Service name** | Apache httpd, OpenSSH, Microsoft IIS |
| **Version** | 2.2.14, 7.2p2, 10.0 |
| **OS from service** | Linux, Windows, FreeBSD |
| **Protocol details** | FTP (vsftpd 2.3.4) |

### Basic Version Detection

**Command:**
```bash
nmap -sV 192.168.56.20
```

**Example Output:**
```
Starting Nmap 7.94 at 2024-01-15 10:20
Nmap scan report for 192.168.56.20
Host is up (0.0010s latency).
PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp   open     http       Apache httpd 2.2.8 ((Ubuntu) PHP/5.2.4-2ubuntu5.10)
443/tcp  open     http       Apache httpd 2.2.8 (SSL-only)
3306/tcp open     mysql      MySQL 5.0.51a-3ubuntu5
3389/tcp open     ms-wbt-server Microsoft Terminal Services
```

**What This Tells Us:**
- `OpenSSH 4.7p1` → Very old version (2010). Likely has known vulnerabilities.
- `Apache httpd 2.2.8` → Old, vulnerable to many exploits.
- `MySQL 5.0.51a` → Extremely old, default credentials likely.
- The system is likely Ubuntu 8.04 (based on Debian version).

**Why Version Detection Matters:**

| Version | Vulnerability |
|---|---|
| OpenSSH 4.7p1 | Multiple vulnerabilities, username enumeration |
| Apache 2.2.8 | DoS attacks, directory traversal |
| MySQL 5.0.51a | Default credentials, SQL injection |

---

### Version Detection Intensity Levels

| Intensity | Command | Probes Sent | Speed | Accuracy |
|---|---|---|---|---|
| **Light** | `--version-light` | 2 | Fast | Low |
| **Default** | `-sV` | 7 | Medium | Medium |
| **All** | `--version-all` | 20+ | Slow | High |

**Light version detection:**
```bash
nmap -sV --version-light 192.168.56.20
```

**All version probes:**
```bash
nmap -sV --version-all 192.168.56.20
```

**When to use each:**
- `--version-light` → When you need quick results
- `-sV` → For most scans (good balance)
- `--version-all` → When you need absolute accuracy (takes longer)

---

## 5. OS Fingerprinting (`-O`)

OS fingerprinting determines the operating system of a remote host by analyzing network responses.

### How OS Fingerprinting Works

Nmap uses a combination of techniques:
- **TCP/IP stack fingerprinting** — Different OSes respond differently
- **Service banners** — Services often reveal OS
- **TTL values** — Default TTL varies by OS
- **Window sizes** — TCP window size varies by OS

### Basic OS Detection

**Command:**
```bash
nmap -O 192.168.56.20
```

**Example Output:**
```
Starting Nmap 7.94 at 2024-01-15 10:25
Nmap scan report for 192.168.56.20
Host is up (0.0010s latency).
Not shown: 995 closed ports
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
3306/tcp open     mysql
3389/tcp open     ms-wbt-server
Device type: general purpose
Running: Microsoft Windows 10|11|Server 2016
OS CPE: cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11
OS details: Microsoft Windows 10 1809-2004, Windows 11, or Server 2016
```

**What This Tells Us:**
- The target is running Windows (10, 11, or Server 2016)
- Likely a Windows 10 VM based on the setup

**Another Example (Linux):**
```bash
nmap -O 192.168.56.30
```

**Output:**
```
Running: Linux 4.x|5.x
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.10 - 5.18, Ubuntu 20.04 (Linux 5.4)
```

### OS Detection Options

| Option | Description |
|---|---|
| `-O` | Enable OS detection |
| `--osscan-guess` | Guess aggressively if perfect match not found |
| `--osscan-limit` | Only OS detect on hosts with at least one open port |

**Aggressive guessing:**
```bash
nmap -O --osscan-guess 192.168.56.20
```

**Limiting OS scan:**
```bash
nmap -O --osscan-limit 192.168.56.0/24
```

### What OS Fingerprinting Reveals

| OS | TTL (Default) | Window Size | TCP Options |
|---|---|---|---|
| Linux | 64 | 5840 | Yes |
| Windows | 128 | 65535 | No |
| macOS | 64 | 65535 | Yes |
| FreeBSD | 64 | 65535 | Yes |
| Cisco IOS | 255 | 4128 | No |

---

## 6. Nmap Scripting Engine (NSE)

The Nmap Scripting Engine is what makes Nmap truly powerful. It provides hundreds of scripts for vulnerability detection, brute forcing, and exploitation.

### NSE Categories

| Category | Description | Example Scripts |
|---|---|---|
| **default** | Basic scripts (run with `-sC`) | smb-os-discovery, ssh-hostkey |
| **discovery** | Information gathering | smb-enum-shares, dns-zone-transfer |
| **vuln** | Vulnerability detection | smb-vuln-ms17-010, http-vuln-cve2021-41773 |
| **exploit** | Exploit scripts | http-shellshock, ftp-proftpd-backdoor |
| **auth** | Authentication bypass | ftp-anon, smb-enum-users |
| **brute** | Brute force attacks | ssh-brute, http-form-brute |
| **dos** | Denial of service | smb-dos, http-slowloris |
| **safe** | Safe scripts (won't crash) | All of the above except dos |

### Running NSE Scripts

**Run default scripts:**
```bash
nmap -sC 192.168.56.20
```

**Run specific category:**
```bash
nmap --script vuln 192.168.56.20
```

**Run specific script:**
```bash
nmap --script smb-vuln-ms17-010 192.168.56.20
```

**Run multiple scripts:**
```bash
nmap --script smb-vuln-*,http-vuln-* 192.168.56.20
```

**Run scripts with arguments:**
```bash
nmap --script smb-enum-shares --script-args smbuser=msfadmin,smbpass=msfadmin 192.168.56.40
```

### NSE Examples with Output

#### Example 1: SMB Vulnerability Scan (EternalBlue)

**Command:**
```bash
nmap --script smb-vuln-ms17-010 192.168.56.40
```

**Output:**
```
Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (MS17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       Critical: CVSS:9.8 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H)
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|_      https://docs.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010
```

**Explanation:** Metasploitable 2 is vulnerable to EternalBlue (MS17-010). This is a critical finding.

---

#### Example 2: FTP Anonymous Login

**Command:**
```bash
nmap --script ftp-anon 192.168.56.40
```

**Output:**
```
Host script results:
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--   1 ftp      ftp         512 Jan 15  2024 README.txt
| -rw-r--r--   1 ftp      ftp        1024 Jan 15  2024 notes.txt
|_ -r--r--r--  1 ftp      ftp         256 Jan 15  2024 config.ini
```

**Explanation:** Anonymous FTP login is allowed. This is a security risk. The attacker can read any files in the FTP directory.

---

#### Example 3: Apache HTTP Vulnerability

**Command:**
```bash
nmap --script http-vuln-cve2021-41773 192.168.56.30
```

**Output:**
```
Host script results:
| http-vuln-cve2021-41773: 
|   VULNERABLE:
|   Apache HTTP Server Path Traversal and Remote Code Execution (CVE-2021-41773)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2021-41773
|     Risk factor: HIGH
|     Description:
|       A path traversal flaw was found in Apache HTTP Server 2.4.49.
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-41773
|_      https://www.cve.org/CVERecord?id=CVE-2021-41773
```

---

### NSE Script Categories Summary

| Category | When to Use |
|---|---|
| **default** | Always run these (`-sC`) |
| **vuln** | To check for known vulnerabilities |
| **discovery** | To find more information about the target |
| **exploit** | Only in lab environments (will change system state) |
| **brute** | To test weak passwords (can be detected) |
| **auth** | To find authentication bypasses |
| **dos** | Only in controlled labs (causes harm) |
| **safe** | Safe to run anywhere |

> ⚠️ **Warning:** `exploit` and `dos` scripts can damage systems. Only use in your own lab or with explicit written permission.

---

## 7. Timing and Performance

Nmap allows you to control scan speed and performance using timing templates and rate limits.

### Timing Templates (`-T`)

| Template | Command | Speed | Accuracy | Best For |
|---|---|---|---|---|
| **Paranoid** | `-T0` | Extremely slow | Very stealthy | IDS evasion |
| **Sneaky** | `-T1` | Very slow | Stealthy | Careful scanning |
| **Polite** | `-T2` | Slow | Low bandwidth | Network-friendly |
| **Normal** | `-T3` | Medium | Balanced | Most scans |
| **Aggressive** | `-T4` | Fast | Less accurate | CTF/Time-limited |
| **Insane** | `-T5` | Extremely fast | May miss ports | Very fast networks |

**Examples:**
```bash
nmap -T4 192.168.56.20          # Aggressive scan
nmap -T2 192.168.56.20          # Polite scan (less network disruption)
```

### Rate Limiting

| Option | Description |
|---|---|
| `--min-rate` | Minimum packets per second |
| `--max-rate` | Maximum packets per second |
| `--min-hostgroup` | Minimum hosts to scan together |
| `--max-hostgroup` | Maximum hosts to scan together |

**Examples:**
```bash
nmap --min-rate 100 192.168.56.20     # At least 100 packets/sec
nmap --max-rate 500 192.168.56.20     # No more than 500 packets/sec
```

### Performance Recommendations

| Scenario | Timing | Rate |
|---|---|---|
| **Unknown network** | `-T3` | Default |
| **Firewalled network** | `-T1` | `--max-rate 10` |
| **Lab network (fast)** | `-T4` | `--min-rate 1000` |
| **Public internet** | `-T2` | `--max-rate 100` |

---

## 8. Output Formats

Nmap supports multiple output formats for different purposes.

### Output Options

| Option | Format | Best For |
|---|---|---|
| `-oN` | Normal | Human-readable |
| `-oX` | XML | Parsing, importing to tools |
| `-oG` | Grepable | Scripting, text processing |
| `-oA` | All formats | Everything at once |

**Normal output (`-oN`):**
```bash
nmap -sV -oN scan_result.txt 192.168.56.20
```

**XML output (`-oX`):**
```bash
nmap -sV -oX scan_result.xml 192.168.56.20
```

**XML is required for tools like:**
- Metasploit (database import)
- Nessus/OpenVAS integrations
- Custom reporting tools

**Grepable output (`-oG`):**
```bash
nmap -sV -oG scan_result.gnmap 192.168.56.20
```

**All formats (`-oA`):**
```bash
nmap -sV -oA scan 192.168.56.20
# Generates: scan.nmap, scan.xml, scan.gnmap
```

### Using Grepable Output

**Example content:**
```
# Nmap 7.94 scan initiated Mon Jan 15 10:30:00 2024 as: nmap -sV -oG - 192.168.56.20
Host: 192.168.56.20 ()  Status: Up
Host: 192.168.56.20 ()  Ports: 22/open/tcp//ssh//OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)/, 80/open/tcp//http//Apache httpd 2.2.8/
```

**Extract with grep:**
```bash
grep "open" scan_result.gnmap | cut -d' ' -f2
# Returns IPs with open ports
```

---

## 9. Masscan (Ultra-Fast Scanning)

**Masscan** is a high-speed port scanner designed for scanning the entire internet. It can scan all ports on the entire IPv4 internet in under 6 minutes.

### Masscan vs Nmap

| Feature | Nmap | Masscan |
|---|---|---|
| **Speed** | Slow-Medium | Extremely Fast |
| **Accuracy** | Very High | High (with correct settings) |
| **Feature set** | Comprehensive | Basic scanning only |
| **Output formats** | Multiple | Limited |
| **Best for** | Detailed scans | Large network scans |

### Installation

```bash
# Ubuntu/Debian/Kali
sudo apt install masscan -y

# macOS
brew install masscan

# From source
git clone https://github.com/robertdavidgraham/masscan
cd masscan
make
sudo make install
```

### Basic Masscan Usage

**Scan a single host on common ports:**
```bash
masscan -p22,80,443 192.168.56.20
```

**Scan all ports on a subnet:**
```bash
masscan -p1-65535 192.168.56.0/24
```

**Scan with rate limit (important!):**
```bash
masscan -p80 --rate=1000 192.168.56.0/24
```

**Output formats:**
```bash
masscan -p80,443 192.168.56.0/24 -oJ scan.json     # JSON
masscan -p80,443 192.168.56.0/24 -oX scan.xml      # XML
masscan -p80,443 192.168.56.0/24 -oL scan.list     # List format
```

### Masscan Output Example

**Command:**
```bash
masscan -p22,80,443,3306,3389 192.168.56.0/24 --rate=1000
```

**Output:**
```
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2024-01-15 10:35:00 GMT
 -- forced options: -sS -Pn -n --randomize-hosts -v --send-eth
Initiating SYN Stealth Scan
Scanning 256 hosts [5 ports/host]
Discovered open port 80/tcp on 192.168.56.30
Discovered open port 22/tcp on 192.168.56.30
Discovered open port 443/tcp on 192.168.56.30
Discovered open port 445/tcp on 192.168.56.20
Discovered open port 3389/tcp on 192.168.56.20
Discovered open port 22/tcp on 192.168.56.40
Discovered open port 80/tcp on 192.168.56.40
```

### When to Use Masscan

| Use Case | Why Masscan |
|---|---|
| **Initial discovery** | Quickly find which hosts have interesting ports |
| **Large networks** | /16 or larger networks |
| **Internet scanning** | Shodan-style scanning |
| **Time-limited engagements** | When you need results fast |

> ⚠️ **Warning:** Scanning too fast can trigger IDS/IPS alerts or crash network equipment. Use rate limiting.

---

## 10. Zenmap (GUI for Nmap)

**Zenmap** is the official graphical interface for Nmap. It makes Nmap accessible for beginners and provides visualization tools.

### Why Use Zenmap

| Feature | Benefit |
|---|---|
| **Point-and-click** | No need to memorize commands |
| **Visual topology** | See network structure visually |
| **Profile system** | Save and reuse scan profiles |
| **History** | View and compare past scans |
| **Interactive** | Click on hosts for more details |

### Installation

**Linux (Ubuntu/Debian/Kali):**
```bash
sudo apt install zenmap -y
```

**Windows/macOS:**
- Included in the Nmap installer
- Look for "Zenmap" in your start menu

### Zenmap Interface Overview

```
┌─────────────────────────────────────────────────────────────┐
│  File   Profile   Scan   Tools   Help                       │
├─────────────────────────────────────────────────────────────┤
│  Target:    192.168.56.0/24                                │
│  Profile:   Intense scan                                   │
│  Command:   nmap -T4 -A -v 192.168.56.0/24                │
│  [Scan]                                                    │
├─────────────────────────────────────────────────────────────┤
│  Tab: [Nmap Output] [Ports/Hosts] [Topology] [Host Details]│
│                                                             │
│  Starting Nmap 7.94 ...                                    │
│  Nmap scan report for 192.168.56.1                         │
│  Host is up (0.0012s latency).                             │
│  ...                                                       │
├─────────────────────────────────────────────────────────────┤
│  Hosts:  192.168.56.1  192.168.56.10  192.168.56.20       │
│  Ports:  22/tcp open ssh  80/tcp open http  445/tcp open   │
└─────────────────────────────────────────────────────────────┘
```

### Zenmap Profiles

| Profile | Command | Use Case |
|---|---|---|
| **Intense scan** | `-T4 -A -v` | Full scan with service/OS detection |
| **Quick scan** | `-T4 -F` | Fast scan of common ports |
| **Ping sweep** | `-sn` | Find live hosts |
| **Regular scan** | `-sS -sV` | Standard port + version scan |

### Topology View

Zenmap's topology view shows the network structure visually:

```
      [Internet]
          │
          ├── 192.168.56.1 (Host)
          │
          ├── 192.168.56.10 (Kali)
          │     ├── 22/tcp (ssh)
          │     └── 80/tcp (http)
          │
          ├── 192.168.56.20 (Windows)
          │     ├── 135/tcp (msrpc)
          │     ├── 139/tcp (netbios)
          │     ├── 445/tcp (smb)
          │     └── 3389/tcp (rdp)
          │
          └── 192.168.56.40 (Metasploitable)
                ├── 21/tcp (ftp)
                ├── 22/tcp (ssh)
                ├── 80/tcp (http)
                ├── 139/tcp (netbios)
                ├── 445/tcp (smb)
                └── 3306/tcp (mysql)
```

### When to Use Zenmap vs Command Line

| Tool | Best For |
|---|---|
| **Zenmap** | Beginners, visual network mapping, learning Nmap |
| **Command-line** | Scripting, automation, advanced scans, SSH sessions |

> 💡 **Recommendation:** Start with Zenmap to understand Nmap. Move to command-line when comfortable.

---

## 11. Hands-On Lab: Full Network Enumeration

### Lab Overview

In this lab you will perform a complete enumeration of your home lab network using Nmap.

### Setup

- **Kali Linux** (attacker)
- **Metasploitable 2** (target)
- Both on Host-Only network (192.168.56.0/24)

---

### Part 1: Host Discovery

**Step 1: Discover live hosts**

```bash
nmap -sn 192.168.56.0/24
```

**Record:** Which hosts are live? What are their IP addresses?

**Step 2: Discover with TCP SYN ping**

```bash
nmap -sn -PS22,80,443 192.168.56.0/24
```

**Compare:** Are the same hosts found? Why might some be different?

**Deliverable:** Create a table of live hosts.

---

### Part 2: Port Scanning

**Step 1: SYN scan all ports on Metasploitable**

```bash
nmap -sS -p- 192.168.56.40
```

**Record:** How many ports are open? Which ones?

**Step 2: UDP scan common ports**

```bash
nmap -sU -p53,69,123,137,161,514 192.168.56.40
```

**Record:** Which UDP ports are open?

**Step 3: Compare SYN vs Connect scan**

```bash
nmap -sS -p22,80,445 192.168.56.40
nmap -sT -p22,80,445 192.168.56.40
```

**Record:** Are the results the same? Why?

**Deliverable:** Complete port scan results table.

---

### Part 3: Service and Version Detection

**Step 1: Default service detection**

```bash
nmap -sV 192.168.56.40
```

**Record:** What services and versions are running on each port?

**Step 2: Full version detection**

```bash
nmap -sV --version-all 192.168.56.40
```

**Compare:** Did `--version-all` reveal more details?

**Step 3: Light version detection**

```bash
nmap -sV --version-light 192.168.56.40
```

**Compare:** How does it differ from default?

**Deliverable:** Service/version table with all details.

---

### Part 4: OS Fingerprinting

**Step 1: OS detection on Metasploitable**

```bash
nmap -O 192.168.56.40
```

**Record:** What OS does Nmap think Metasploitable is running?

**Step 2: OS detection on Windows VM (if available)**

```bash
nmap -O 192.168.56.20
```

**Record:** What OS is the Windows VM?

**Deliverable:** OS detection results.

---

### Part 5: NSE Scripts

**Step 1: Default scripts on Metasploitable**

```bash
nmap -sC 192.168.56.40
```

**Record:** What scripts ran? What did they find?

**Step 2: SMB vulnerability scan**

```bash
nmap --script smb-vuln-* 192.168.56.40
```

**Record:** What vulnerabilities were found?

**Step 3: FTP anonymous login check**

```bash
nmap --script ftp-anon 192.168.56.40
```

**Record:** Is anonymous FTP allowed?

**Step 4: Enumeration scripts**

```bash
nmap --script smb-enum-shares,smb-enum-users 192.168.56.40
```

**Record:** What shares and users were discovered?

**Deliverable:** NSE script results with explanations.

---

### Part 6: Timing and Performance

**Step 1: Default timing scan**

```bash
time nmap -p80 192.168.56.40
```

**Record:** How long did it take?

**Step 2: Aggressive timing scan**

```bash
time nmap -T4 -p80 192.168.56.40
```

**Compare:** Was it faster? How much?

**Step 3: Rate-limited scan**

```bash
time nmap --max-rate 10 -p80 192.168.56.40
```

**Compare:** Was it slower?

**Deliverable:** Time comparison table.

---

### Part 7: Masscan (Optional)

**Step 1: Masscan on common ports**

```bash
masscan -p22,80,443,445,3306,3389 192.168.56.40 --rate=1000
```

**Compare:** Is it faster than Nmap?

**Deliverable:** Masscan results.

---

### Part 8: Final Report

Create `lab_12_nmap_enumeration.md`:

# Lab 12: Full Network Enumeration with Nmap

## Target Information
- Network: 192.168.56.0/24
- Attacker: Kali Linux (IP: )
- Target: Metasploitable 2 (IP: )

## Host Discovery Results
| IP | Status | Method |
|----|--------|--------|
| 192.168.56.1 | Alive | -sn |
| 192.168.56.40 | Alive | -sn |

## Port Scanning Results
| Port | Protocol | State | Service | Version |
|------|----------|-------|---------|---------|
| 21 | TCP | Open | FTP | vsftpd 2.3.4 |
| 22 | TCP | Open | SSH | OpenSSH 4.7p1 |
| 23 | TCP | Open | Telnet | Linux telnetd |
| 25 | TCP | Open | SMTP | Postfix smtpd |
| 80 | TCP | Open | HTTP | Apache httpd 2.2.8 |
| 139 | TCP | Open | NetBIOS | Samba smbd |
| 445 | TCP | Open | SMB | Samba smbd |
| 3306 | TCP | Open | MySQL | MySQL 5.0.51a |
| 5432 | TCP | Open | PostgreSQL | PostgreSQL 8.3 |
| 5900 | TCP | Open | VNC | VNC (protocol 3.3) |

## OS Fingerprinting
- Target: Linux 2.6.x (Ubuntu 8.04)
- Confidence: 90%

## NSE Script Results
| Script | Finding |
|--------|---------|
| smb-vuln-ms17-010 | VULNERABLE |
| ftp-anon | Anonymous login allowed |
| smb-enum-shares | Shares: tmp, data, pub |
| smb-enum-users | Users: msfadmin, root, guest |

## Vulnerabilities Identified
1. vsftpd 2.3.4 - Backdoor (CVE-2011-2523)
2. OpenSSH 4.7p1 - Multiple vulnerabilities
3. Apache 2.2.8 - Directory traversal
4. Samba 3.0.20 - username map script vulnerability
5. MySQL 5.0.51a - Default credentials

## Reflection
- What was the most interesting find?
- Which Nmap features were most useful?
- What would you scan next?

---

## 12. Summary & Key Takeaways

**Nmap Fundamentals:**
- Installation, syntax, and help
- Know the difference between host discovery and port scanning

**Host Discovery:**
- `-sn` for ping sweeps
- `-Pn` to skip host discovery
- TCP/UDP pings for firewalled networks

**Port Scanning:**
- SYN scan (`-sS`) is the default and best
- Connect scan (`-sT`) when no raw sockets
- UDP scan (`-sU`) for UDP services

**Service and OS Detection:**
- `-sV` for version detection
- `-O` for OS fingerprinting
- Use `--version-all` for complete details

**NSE Scripts:**
- `-sC` for default scripts
- `--script vuln` for vulnerability scanning
- Use categories: default, vuln, discovery, exploit

**Performance:**
- `-T0` to `-T5` for timing
- Rate limiting for stealth
- Adjust for network conditions

**Tools:**
- Masscan for fast scans
- Zenmap for GUI
- Use both for different scenarios

---

## 13. Quiz

1. What is the difference between `-sn` and `-Pn`?

2. Which Nmap scan type is the most stealthy and why?

3. What information does `-sV` reveal that `-sS` does not?

4. What is the Nmap Scripting Engine (NSE) used for?

5. What does an NSE script with "vuln" category do?

6. What is the difference between Masscan and Nmap?

7. What does `-O` do in Nmap?

8. How can you slow down a Nmap scan to avoid detection?

<details>
<summary>📋 Click to reveal answers</summary>

1. `-sn` performs host discovery (ping sweep) only — it does NOT scan ports. `-Pn` skips host discovery entirely and assumes all hosts are up, then proceeds to port scanning.

2. **SYN scan (`-sS`)** is the most stealthy. It never completes the TCP handshake, so the target application may not log the connection attempt.

3. `-sV` reveals the **service name and version** (e.g., Apache httpd 2.2.8, OpenSSH 4.7p1). `-sS` only tells you the port is open.

4. The NSE is a scripting engine that allows Nmap to run custom scripts for vulnerability detection, enumeration, brute forcing, and more.

5. "vuln" category scripts check for **known vulnerabilities** (CVEs) on the target.

6. Masscan is **ultra-fast** but limited to basic scanning. Nmap is **slower** but has a much larger feature set (scripts, OS detection, version detection, etc.).

7. `-O` enables **OS fingerprinting** — it tries to determine the operating system of the target host.

8. Use a slower timing template: `-T2` (Polite) or `-T1` (Sneaky). Or use `--max-rate` to limit packets per second.

</details>

---

## 14. Resources

### Official Resources
- [Nmap Official Site](https://nmap.org)
- [Nmap Documentation](https://nmap.org/docs.html)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [NSE Scripts Index](https://nmap.org/nsedoc/)

### Additional Reading
- [Masscan GitHub](https://github.com/robertdavidgraham/masscan)
- [Zenmap GUI Guide](https://nmap.org/zenmap/)

### Practice
- [TryHackMe Nmap Room](https://tryhackme.com/room/furthernmap)
- [HackTheBox Nmap Walkthroughs](https://www.hackthebox.com)

---

**Previous Module:** [Module 11 — Anonymity, Privacy & OpSec](../02_RECONNAISSANCE_&_OSINT/Module_11_Anonymity_Privacy_&_Operational_Security.md)

**Next Module:** [Module 13 — Packet Analysis with Wireshark](Module_13_Packet_Analysis_with_Wireshark.md)

---

*Module 12 | Phase 3: Scanning & Enumeration | cybersecurity_beginner_to_advance*
