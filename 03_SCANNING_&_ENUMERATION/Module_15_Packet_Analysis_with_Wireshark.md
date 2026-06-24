# Module 15: Packet Analysis with Wireshark

> **Phase 3 — Scanning & Enumeration**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `03_SCANNING`

---

## Table of Contents

1. [What is Packet Analysis?](#1-what-is-packet-analysis)
2. [Wireshark Fundamentals](#2-wireshark-fundamentals)
3. [Capture Filters](#3-capture-filters)
4. [Display Filters](#4-display-filters)
5. [Colorization Rules](#5-colorization-rules)
6. [Following Streams](#6-following-streams)
7. [Protocol Analysis Deep Dive](#7-protocol-analysis-deep-dive)
8. [Statistics and Endpoints](#8-statistics-and-endpoints)
9. [tcpdump — Command-Line Capture](#9-tcpdump--command-line-capture)
10. [tshark — Command-Line Analysis](#10-tshark--command-line-analysis)
11. [Hands-On Lab: Detect Attacks with Wireshark](#11-hands-on-lab-detect-attacks-with-wireshark)
12. [Summary & Key Takeaways](#12-summary--key-takeaways)
13. [Quiz](#13-quiz)
14. [Resources](#14-resources)

---

## 1. What is Packet Analysis?

**Packet analysis (packet sniffing)** is the process of capturing and inspecting data packets as they travel across a network.

### Why Packet Analysis Matters in Cybersecurity

| Use Case | What You Can Detect |
|---|---|
| **Network troubleshooting** | Identify connectivity issues, latency |
| **Incident response** | Find malicious traffic, data exfiltration |
| **Threat hunting** | Detect C2 communication, port scans |
| **Forensics** | Reconstruct attacks from captured traffic |
| **Vulnerability verification** | Confirm exploit attempts |
| **Compliance** | Validate encryption, data leakage |

### How Packet Analysis Works

```
[Device A] ──────── Packet ────────→ [Device B]
                  │
                  ↓
           [Wireshark captures]
                  │
                  ↓
         [Analyze packet details]
```

When you capture packets, you see:
- **Source and destination IP addresses**
- **Source and destination ports**
- **Protocol used** (TCP, UDP, HTTP, DNS, etc.)
- **Payload data** (the actual content being sent)
- **Timestamps** (when each packet was sent)

---

## 2. Wireshark Fundamentals

**Wireshark** is the world's most popular network protocol analyzer. It captures packets and presents them in a detailed, searchable format.

### Installation

**Linux (Ubuntu/Debian/Kali):**
```bash
sudo apt update
sudo apt install wireshark -y
sudo usermod -aG wireshark $USER
# Log out and back in
```

**Windows/macOS:**
1. Download from https://www.wireshark.org/download.html
2. Run installer
3. Follow prompts (install Npcap on Windows)

### Wireshark Interface Overview

```
┌─────────────────────────────────────────────────────────────────┐
│ File  Edit  View  Capture  Analyze  Statistics  Telephony  Help │
├─────────────────────────────────────────────────────────────────┤
│ [Interface List]  [Start]  [Stop]  [Restart]  [Filters]        │
├─────────────────────────────────────────────────────────────────┤
│ Packet List Pane (Packets in chronological order)              │
│ ┌──────────────────────────────────────────────────────────┐  │
│ │  No.  Time   Source      Destination    Protocol  Info  │  │
│ │  1    0.00   192.168.1.1 192.168.1.10  TCP       SYN    │  │
│ │  2    0.01   192.168.1.10 192.168.1.1  TCP       SYN-ACK│  │
│ │  3    0.02   192.168.1.1 192.168.1.10  TCP       ACK    │  │
│ └──────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│ Packet Details Pane (Protocol hierarchy and fields)            │
│ ┌──────────────────────────────────────────────────────────┐  │
│ │  Ethernet II                                            │  │
│ │  Internet Protocol Version 4                            │  │
│ │  Transmission Control Protocol                          │  │
│ │  Hypertext Transfer Protocol                            │  │
│ └──────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────┤
│ Packet Bytes Pane (Raw hex and ASCII dump)                     │
│ ┌──────────────────────────────────────────────────────────┐  │
│ │  0000  45 00 00 3c 12 34 40 00 40 06 45 67 0a 00 01 01 │  │
│ │  0010  0a 00 01 0a 00 50 1a 2b 00 00 00 00 00 00 50 02 │  │
│ └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

### Three Panes Explained

| Pane | What It Shows |
|---|---|
| **Packet List** | All captured packets in order. Click any packet to see details. |
| **Packet Details** | Protocol breakdown of the selected packet. Expand each layer. |
| **Packet Bytes** | Raw hex and ASCII representation of the packet. |

### Promiscuous vs Monitor Mode

**Promiscuous Mode:**
- Captures ALL packets on the network segment, not just those destined for your MAC
- Works on wired and wireless networks
- Required for network analysis

**Monitor Mode (Wireless only):**
- Captures ALL wireless traffic on a specific channel
- Does NOT require connecting to the network
- Captures management frames, beacon frames, etc.

**How to enable:**
```
1. Wireshark → Capture → Options
2. Select interface
3. Check "Promiscuous mode" or "Monitor mode"
```

> ⚠️ **Note:** Monitor mode requires a compatible wireless adapter that supports monitor mode.

### Capturing Traffic

**Start a capture:**
1. Open Wireshark
2. Click the interface you want to capture from (eth0, wlan0, etc.)
3. Click the blue shark fin icon to start

**Stop capturing:**
- Click the red square icon to stop

**Save capture:**
- File → Save As → Choose format (.pcapng recommended)

---

## 3. Capture Filters

**Capture filters** limit what packets are saved during capture. They reduce file size and focus on relevant traffic.

> 💡 **Important:** Capture filters use **BPF (Berkeley Packet Filter)** syntax. They run BEFORE packets are saved.

### Capture Filter Syntax

| Element | Syntax | Example |
|---|---|---|
| **Host** | `host [IP]` | `host 192.168.1.10` |
| **Network** | `net [network/mask]` | `net 192.168.1.0/24` |
| **Port** | `port [number]` | `port 80` |
| **Port Range** | `portrange [start-end]` | `portrange 1-1024` |
| **Protocol** | `[protocol]` | `tcp`, `udp`, `icmp`, `arp` |
| **Source** | `src [host/net/port]` | `src host 192.168.1.10` |
| **Destination** | `dst [host/net/port]` | `dst port 80` |
| **Logical Operators** | `and`, `or`, `not` | `tcp and port 80` |

### Capture Filter Examples

```bash
# Capture only traffic from/to 192.168.1.10
host 192.168.1.10

# Capture only traffic to/from the 192.168.1.0/24 network
net 192.168.1.0/24

# Capture only TCP traffic on port 80 (HTTP)
tcp port 80

# Capture only UDP traffic on port 53 (DNS)
udp port 53

# Capture only SSH traffic (port 22)
port 22

# Capture traffic from a specific source IP
src host 192.168.1.10

# Capture traffic to a specific destination port
dst port 443

# Capture all traffic except ARP
not arp

# Capture TCP traffic on port 80 OR port 443
tcp port 80 or tcp port 443

# Capture traffic from 192.168.1.10 to port 80
src host 192.168.1.10 and dst port 80

# Capture all traffic except HTTP and HTTPS
not port 80 and not port 443
```

### When to Use Capture Filters

| Scenario | Capture Filter |
|---|---|
| **Web traffic analysis** | `tcp port 80 or tcp port 443` |
| **DNS investigation** | `udp port 53` |
| **Email traffic** | `port 25 or port 465 or port 587 or port 110 or port 993` |
| **SMB/Windows file sharing** | `port 445 or port 139` |
| **SSH connection** | `port 22` |
| **Ping sweep detection** | `icmp` |
| **ARP investigation** | `arp` |

---

## 4. Display Filters

**Display filters** filter packets already captured. They DO NOT discard packets — they hide packets from view.

> 💡 **Important:** Display filters use a different syntax than capture filters. They are more powerful and user-friendly.

### Display Filter Syntax

| Element | Syntax | Example |
|---|---|---|
| **IP Address** | `ip.addr`, `ip.src`, `ip.dst` | `ip.addr == 192.168.1.10` |
| **Port** | `tcp.port`, `udp.port` | `tcp.port == 80` |
| **Protocol** | `[protocol]` | `http`, `dns`, `arp`, `tcp` |
| **TCP Flags** | `tcp.flags.syn`, `tcp.flags.ack` | `tcp.flags.syn == 1` |
| **HTTP** | `http.request`, `http.response` | `http.request.method == GET` |
| **Length** | `frame.len` | `frame.len > 1500` |
| **Logical Operators** | `and`, `or`, `not`, `&&`, `||`, `!` | `tcp and http` |

### Display Filter Examples

#### Basic Filters

```bash
# Show only TCP traffic
tcp

# Show only UDP traffic
udp

# Show only ICMP traffic
icmp

# Show only ARP traffic
arp

# Show only HTTP traffic
http

# Show only DNS traffic
dns
```

#### IP-Based Filters

```bash
# Traffic from/to 192.168.1.10
ip.addr == 192.168.1.10

# Traffic from 192.168.1.10
ip.src == 192.168.1.10

# Traffic to 192.168.1.10
ip.dst == 192.168.1.10

# Traffic between two hosts
ip.addr == 192.168.1.10 and ip.addr == 192.168.1.20

# All traffic except 192.168.1.10
!ip.addr == 192.168.1.10
```

#### Port-Based Filters

```bash
# TCP port 80
tcp.port == 80

# UDP port 53
udp.port == 53

# Multiple ports
tcp.port == 80 or tcp.port == 443

# Source port
tcp.srcport == 80

# Destination port
tcp.dstport == 443

# Port range
tcp.port in {80, 443, 22, 25}
```

#### HTTP Filters

```bash
# HTTP requests
http.request

# HTTP GET requests
http.request.method == GET

# HTTP POST requests
http.request.method == POST

# HTTP responses
http.response

# HTTP response code 200 (OK)
http.response.code == 200

# HTTP 404 Not Found
http.response.code == 404

# HTTP URL contains "admin"
http.request.uri contains "admin"

# HTTP User-Agent
http.user_agent contains "Mozilla"
```

#### DNS Filters

```bash
# DNS queries
dns.flags.response == 0

# DNS responses
dns.flags.response == 1

# DNS A record requests
dns.qry.type == 1

# DNS AAAA record requests
dns.qry.type == 28

# DNS MX record requests
dns.qry.type == 15

# Specific domain query
dns.qry.name contains "google.com"
```

#### TCP Flags Filters

```bash
# SYN packets (connection initiation)
tcp.flags.syn == 1

# SYN-ACK packets
tcp.flags.syn == 1 and tcp.flags.ack == 1

# ACK packets
tcp.flags.ack == 1

# RST packets (connection reset)
tcp.flags.reset == 1

# FIN packets (connection termination)
tcp.flags.fin == 1

# SYN scan detection (SYN without ACK)
tcp.flags.syn == 1 and tcp.flags.ack == 0

# Suspicious: SYN from same source to many destinations
tcp.flags.syn == 1
```

#### Complex Filters

```bash
# All HTTP traffic from 192.168.1.10
ip.src == 192.168.1.10 and http

# All traffic between 192.168.1.10 and 192.168.1.20
ip.addr == 192.168.1.10 and ip.addr == 192.168.1.20

# All HTTP traffic containing "password"
http contains "password"

# All HTTP traffic containing "login" in URI
http.request.uri contains "login"

# All DNS queries containing "facebook"
dns.qry.name contains "facebook"

# All packets larger than 1500 bytes
frame.len > 1500

# All packets smaller than 64 bytes
frame.len < 64

# All traffic with TTL=1 (traceroute detection)
ip.ttl == 1
```

---

## 5. Colorization Rules

Wireshark uses colors to highlight different types of traffic. You can customize these colors for anomaly detection.

### Default Colorization

| Color | Traffic Type | Meaning |
|---|---|---|
| **Light Green** | HTTP | Web traffic |
| **Light Blue** | UDP | DNS, DHCP, etc. |
| **Dark Blue** | TCP | General TCP |
| **Light Purple** | TCP SYN | Connection initiation |
| **Dark Purple** | TCP RST | Connection reset |
| **Black** | TCP SYN-ACK | Connection accepted |
| **Yellow** | ARP | Address resolution |
| **Orange** | ICMP | Ping, traceroute |
| **Red** | TCP errors | Issues with connections |

### Custom Colorization Rules

**Where to customize:**
```
View → Coloring Rules → Add
```

**Rule Format:**
```
Filter: [display filter]
Color: [choose color]
```

### Example Colorization Rules

| Rule Name | Filter | Color | Purpose |
|---|---|---|---|
| **SYN Flood** | `tcp.flags.syn == 1 and tcp.flags.ack == 0` | Red | Detect SYN scans |
| **HTTP 404** | `http.response.code == 404` | Yellow | Find broken links |
| **HTTP Error 500** | `http.response.code == 500` | Red | Server errors |
| **DNS Query** | `dns.flags.response == 0` | Blue | DNS lookups |
| **ARP Spoofing** | `arp.duplicate-address-frame` | Red | ARP poisoning |
| **Large Packets** | `frame.len > 1500` | Orange | Jumbo frames |
| **TLS Handshake** | `tls.handshake.type == 1` | Green | TLS client hello |
| **SMB Traffic** | `smb` | Purple | Windows file sharing |

---

## 6. Following Streams

Following a stream reconstructs the entire conversation between two hosts.

### TCP Stream

**How to follow a TCP stream:**
1. Right-click any packet in the conversation
2. Select **Follow → TCP Stream**

**What you see:**
- Complete HTTP conversation (request + response)
- SMTP email content
- FTP commands
- SSH session data (encrypted)

**Example: HTTP Stream Output:**
```
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: */*

HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1256

<!DOCTYPE html>
<html>
<head>
<title>Example Page</title>
</head>
<body>
<h1>Welcome!</h1>
</body>
</html>
```

### UDP Stream

UDP streams work similarly but are less reliable because UDP is connectionless.

**How to follow a UDP stream:**
1. Right-click a UDP packet
2. Select **Follow → UDP Stream**

**Use cases:**
- DNS queries and responses
- SNMP traffic
- NTP synchronization

### HTTP Stream

**How to follow HTTP stream:**
1. Right-click an HTTP packet
2. Select **Follow → HTTP Stream**

**What you see:**
- Complete HTTP request and response
- Headers and body
- Cookies and session data

---

## 7. Protocol Analysis Deep Dive

### 7.1 HTTP Protocol Analysis

**HTTP Packet Example:**

```
Frame 4: 74 bytes on wire
Ethernet II
Internet Protocol Version 4, Src: 192.168.1.10, Dst: 93.184.216.34
Transmission Control Protocol, Src Port: 54321, Dst Port: 80
Hypertext Transfer Protocol
    GET /index.html HTTP/1.1\r\n
    Host: example.com\r\n
    User-Agent: Mozilla/5.0\r\n
    Accept: */*\r\n
    \r\n
```

**What to Look For:**

| Field | What It Reveals |
|---|---|
| **Request Method** | GET (retrieve), POST (submit data), PUT (upload) |
| **Host** | Domain being accessed |
| **User-Agent** | Browser/OS information |
| **Cookie** | Session tokens (security risk if HTTP) |
| **Referer** | Where the user came from |
| **Content-Length** | Size of data being sent |

**Security Detection:**
```bash
# Find HTTP passwords (INSECURE!)
http.request.uri contains "password"
http.request.uri contains "login"
http.request.uri contains "user"

# Find HTTP cookies (INSECURE!)
http.cookie

# Find POST requests with form data
http.request.method == POST

# Find sensitive data in HTTP (BAD!)
http contains "password"
http contains "credit"
http contains "ssn"
```

### 7.2 DNS Protocol Analysis

**DNS Query Packet Example:**

```
Domain Name System (query)
    Transaction ID: 0x1234
    Flags: 0x0100 (Standard query)
    Questions: 1
    Answer RRs: 0
    Queries
        example.com: type A, class IN
```

**DNS Response Packet Example:**

```
Domain Name System (response)
    Transaction ID: 0x1234
    Flags: 0x8180 (Standard query response)
    Questions: 1
    Answer RRs: 1
    Queries
        example.com: type A, class IN
    Answers
        example.com: type A, class IN, addr 93.184.216.34
```

**What to Look For:**

| Field | What It Reveals |
|---|---|
| **Query Name** | Domain being looked up |
| **Query Type** | A (IPv4), AAAA (IPv6), MX (mail), CNAME (alias) |
| **Response Code** | 0 (Success), 3 (NXDOMAIN) |
| **Answer** | IP address returned |

**Security Detection:**
```bash
# Find DNS queries for suspicious domains
dns.qry.name contains "malware"
dns.qry.name contains "c2"
dns.qry.name contains "evil"
dns.qry.name contains "phishing"

# Find DNS queries for new domains
dns.qry.name matches ".*\\.bit$"

# Find NXDOMAIN responses (potential DGA detection)
dns.flags.response == 1 and dns.retcode == 3

# Find DNS responses with multiple answers
dns.resp.type == 1 and dns.resp.anscount > 1
```

### 7.3 ARP Protocol Analysis

**ARP Request Packet Example:**

```
Address Resolution Protocol (request)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    Sender MAC: aa:bb:cc:dd:ee:ff
    Sender IP: 192.168.1.10
    Target MAC: 00:00:00_00:00:00
    Target IP: 192.168.1.1
```

**ARP Response Packet Example:**

```
Address Resolution Protocol (reply)
    Opcode: reply (2)
    Sender MAC: 11:22:33:44:55:66
    Sender IP: 192.168.1.1
    Target MAC: aa:bb:cc:dd:ee:ff
    Target IP: 192.168.1.10
```

**Security Detection (ARP Spoofing):**

```bash
# Find ARP replies with changed MAC (spoofing)
arp.opcode == 2 and arp.src.hw_mac != "00:00:00:00:00:00"

# Find duplicate ARP responses (spoofing)
arp.duplicate-address-detected

# Find ARP responses that don't match the source MAC
arp and not arp.src.hw_mac == arp.src.proto_mac

# Find ARP replies for the default gateway
arp.dst.proto_ip == 192.168.1.1

# Find ARP probes (scanning)
arp.opcode == 1 and arp.dst.proto_ip == 0.0.0.0
```

### 7.4 TCP Protocol Analysis

**TCP SYN Packet Example:**

```
Transmission Control Protocol
    Source Port: 54321
    Destination Port: 80
    Sequence Number: 0
    Acknowledgment Number: 0
    Flags: 0x002 (SYN)
    Window Size: 65535
    Checksum: 0x1234
```

**TCP SYN-ACK Packet Example:**

```
Transmission Control Protocol
    Source Port: 80
    Destination Port: 54321
    Sequence Number: 0
    Acknowledgment Number: 1
    Flags: 0x012 (SYN, ACK)
    Window Size: 64240
    Checksum: 0x5678
```

**TCP ACK Packet Example:**

```
Transmission Control Protocol
    Source Port: 54321
    Destination Port: 80
    Sequence Number: 1
    Acknowledgment Number: 1
    Flags: 0x010 (ACK)
    Window Size: 65535
```

**TCP RST Packet Example:**

```
Transmission Control Protocol
    Flags: 0x004 (RST)
```

**Security Detection:**

```bash
# SYN scan (SYN without ACK)
tcp.flags.syn == 1 and tcp.flags.ack == 0

# SYN-ACK scan (detect response to SYN)
tcp.flags.syn == 1 and tcp.flags.ack == 1

# RST scan (connection reset)
tcp.flags.reset == 1

# SYN flood detection
tcp.flags.syn == 1 and tcp.flags.ack == 0 and frame.time_delta < 0.001

# Connection attempts to many ports (port scan)
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.dstport in {22, 23, 25, 80, 443, 3389}
```

### 7.5 ICMP Protocol Analysis

**ICMP Echo Request (Ping) Packet Example:**

```
Internet Control Message Protocol
    Type: 8 (Echo (ping) request)
    Code: 0
    Checksum: 0x1234
    Identifier: 0x0001
    Sequence: 1
    Data (56 bytes)
```

**ICMP Echo Reply Packet Example:**

```
Internet Control Message Protocol
    Type: 0 (Echo (ping) reply)
    Code: 0
    Checksum: 0x1234
    Identifier: 0x0001
    Sequence: 1
    Data (56 bytes)
```

**ICMP Destination Unreachable Packet Example:**

```
Internet Control Message Protocol
    Type: 3 (Destination Unreachable)
    Code: 3 (Port Unreachable)
    Checksum: 0x5678
```

**Security Detection:**

```bash
# Ping sweep detection (many ICMP requests)
icmp.type == 8

# ICMP tunneling detection (large ICMP packets)
icmp and frame.len > 100

# Ping flood detection
icmp.type == 8 and frame.time_delta < 0.001

# Ping of death (oversized packet)
icmp and frame.len > 65535

# ICMP redirect attacks
icmp.type == 5
```

### 7.6 DHCP Protocol Analysis

**DHCP Discover Packet Example:**

```
Bootstrap Protocol (DHCP Discover)
    Message type: Boot Request (1)
    Hardware type: Ethernet
    Hardware address length: 6
    Transaction ID: 0x12345678
    Client hardware address: 11:22:33:44:55:66
    Options
        DHCP Message Type: DHCP Discover
        Client Identifier: 11:22:33:44:55:66
        Parameter Request List: subnet-mask, router, domain-name, domain-name-servers
```

**DHCP Offer Packet Example:**

```
Bootstrap Protocol (DHCP Offer)
    Message type: Boot Reply (2)
    Transaction ID: 0x12345678
    Your (client) IP address: 192.168.1.100
    Server IP address: 192.168.1.1
    Options
        DHCP Message Type: DHCP Offer
        Subnet Mask: 255.255.255.0
        Router: 192.168.1.1
        Domain Name Servers: 8.8.8.8, 8.8.4.4
```

**Security Detection:**

```bash
# DHCP Discover (client asking for IP)
dhcp.msg.type == 1

# DHCP Offer (server offering IP)
dhcp.msg.type == 2

# DHCP Request (client accepting)
dhcp.msg.type == 3

# DHCP ACK (server confirming)
dhcp.msg.type == 5

# Rogue DHCP server detection
dhcp.option.dhcp_server != 192.168.1.1
```

### 7.7 SMB Protocol Analysis

**SMB Protocol Packet Example:**

```
Server Message Block (SMB)
    Command: Negotiate Protocol (0x72)
    Tree Connect (0x75)
        Path: \\192.168.1.20\share
        Username: msfadmin
```

**Security Detection:**

```bash
# SMB traffic (file sharing)
smb

# SMB login attempts
smb.cmd == 0x73  # SMB_COM_SESSION_SETUP_ANDX

# SMB anonymous login
smb.nt_status == 0x00000000  # STATUS_SUCCESS

# SMB fails
smb.nt_status != 0x00000000

# SMB share enumeration
smb.cmd == 0x75  # SMB_COM_TREE_CONNECT
```

### 7.8 FTP Protocol Analysis

**FTP Packet Example:**

```
File Transfer Protocol (FTP)
    USER msfadmin\r\n
    PASS msfadmin\r\n
    RETR secret.txt\r\n
```

**Security Detection:**

```bash
# FTP traffic
ftp

# FTP username (cleartext!)
ftp contains "USER"
ftp.request.argument contains "USER"

# FTP password (cleartext!)
ftp contains "PASS"
ftp.request.argument contains "PASS"

# Anonymous FTP login
ftp contains "anonymous"

# FTP file transfer
ftp.command == "RETR"
```

---

## 8. Statistics and Endpoints

Wireshark provides powerful statistics to understand network traffic patterns.

### Endpoints

**What it shows:** All IP addresses, MAC addresses, or ports that sent/received traffic.

**How to access:** Statistics → Endpoints

**Example Output:**
```
IPv4 Endpoints
| Address        | Packets | Bytes | Ports |
|---|---|---|---|
| 192.168.1.10   | 1,234   | 100K  | 80,443|
| 192.168.1.20   | 567     | 45K   | 22    |
| 192.168.1.1    | 890     | 70K   | 53,80 |
```

**What to Look For:**
- Unusual IP addresses talking to your network
- High traffic volume from unexpected sources

### Protocol Hierarchy

**What it shows:** Distribution of traffic by protocol.

**How to access:** Statistics → Protocol Hierarchy

**Example Output:**
```
Protocol Hierarchy Statistics
| Protocol | Packets | Bytes |
|---|---|---|
| Frame    | 10,000  | 1.2MB |
| Ethernet | 10,000  | 1.2MB |
| IPv4     | 9,800   | 1.1MB |
| UDP      | 3,200   | 200KB |
| DNS      | 2,500   | 150KB |
| TCP      | 6,600   | 900KB |
| HTTP     | 4,200   | 800KB |
| SMB      | 200     | 50KB  |
| ICMP     | 50      | 5KB   |
| ARP      | 200     | 10KB  |
```

**What to Look For:**
- Unusual protocols (e.g., excessive DNS or SMB)
- Traffic patterns that don't match normal behavior

### Conversations

**What it shows:** Traffic between pairs of endpoints.

**How to access:** Statistics → Conversations

**Example Output:**
```
IPv4 Conversations
| Address A      | Address B      | Packets | Bytes |
|---|---|---|---|
| 192.168.1.10   | 192.168.1.20   | 500     | 50KB  |
| 192.168.1.10   | 93.184.216.34  | 1,000   | 120KB |
| 192.168.1.20   | 8.8.8.8        | 200     | 20KB  |
```

**What to Look For:**
- Communication with suspicious external IPs
- Unusually large data transfers

### IO Graph

**What it shows:** Traffic rate over time.

**How to access:** Statistics → I/O Graph

**Use Cases:**
- Detect traffic spikes (DDoS attacks)
- Identify periodic beaconing (C2 communication)
- Find when attacks occurred

---

## 9. tcpdump — Command-Line Capture

**tcpdump** is a command-line packet capture tool. It's available on most Linux distributions and macOS.

### Installation

```bash
# Linux (Ubuntu/Debian/Kali)
sudo apt install tcpdump -y

# macOS (built-in)
# Already installed

# Windows: Use Npcap's npcapdump or Wireshark's tshark
```

### Basic tcpdump Usage

```bash
# Capture on interface eth0
sudo tcpdump -i eth0

# Capture on all interfaces
sudo tcpdump -i any

# Capture specific number of packets
sudo tcpdump -c 100

# Save to file
sudo tcpdump -w capture.pcap

# Read from file
tcpdump -r capture.pcap

# Verbose output
sudo tcpdump -v
sudo tcpdump -vv
sudo tcpdump -vvv
```

### tcpdump Filters (Same as Capture Filters)

```bash
# Capture HTTP traffic
sudo tcpdump -i eth0 port 80

# Capture traffic from/to a host
sudo tcpdump -i eth0 host 192.168.1.10

# Capture only from a source IP
sudo tcpdump -i eth0 src host 192.168.1.10

# Capture only to a destination port
sudo tcpdump -i eth0 dst port 22

# Capture HTTP and HTTPS
sudo tcpdump -i eth0 port 80 or port 443

# Capture all traffic except ARP
sudo tcpdump -i eth0 not arp

# Capture SSH traffic and save to file
sudo tcpdump -i eth0 port 22 -w ssh_traffic.pcap

# Capture DNS queries
sudo tcpdump -i eth0 udp port 53
```

### tcpdump Filter Examples

```bash
# Capture SYN packets (TCP handshake)
sudo tcpdump -i eth0 'tcp[13] & 2 != 0'

# Capture SYN-ACK packets
sudo tcpdump -i eth0 'tcp[13] & 18 == 18'

# Capture RST packets
sudo tcpdump -i eth0 'tcp[13] & 4 != 0'

# Capture packets from 192.168.1.10 to port 80
sudo tcpdump -i eth0 'src host 192.168.1.10 and dst port 80'

# Capture packets larger than 1000 bytes
sudo tcpdump -i eth0 'len > 1000'

# Capture packets with TTL=1 (traceroute)
sudo tcpdump -i eth0 'ip[8] == 1'
```

### tcpdump Output

**Example Output:**
```
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes

10:00:00.123456 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [S], seq 0, win 65535, length 0
10:00:00.123789 IP 93.184.216.34.80 > 192.168.1.10.54321: Flags [S.], seq 0, ack 1, win 64240, length 0
10:00:00.123901 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [.], ack 1, win 65535, length 0
10:00:00.124012 IP 192.168.1.10.54321 > 93.184.216.34.80: Flags [P.], seq 1:78, ack 1, win 65535, length 77: HTTP: GET /index.html HTTP/1.1
10:00:00.124345 IP 93.184.216.34.80 > 192.168.1.10.54321: Flags [.], ack 78, win 64240, length 0
10:00:00.124456 IP 93.184.216.34.80 > 192.168.1.10.54321: Flags [P.], seq 1:369, ack 78, win 64240, length 368: HTTP: HTTP/1.1 200 OK
```

### tcpdump Output Options

| Option | Description |
|---|---|
| `-v` | Verbose output |
| `-vv` | More verbose |
| `-vvv` | Most verbose |
| `-n` | No DNS resolution (IPs only) |
| `-nn` | No DNS and no port resolution |
| `-e` | Show MAC addresses |
| `-q` | Quiet mode (less output) |
| `-s` | Snapshot length (bytes to capture) |

**Example:**
```bash
# No DNS resolution, full details
sudo tcpdump -i eth0 -nn -vv
```

---

## 10. tshark — Command-Line Analysis

**tshark** is the command-line version of Wireshark. It can capture, read, and filter packets.

### Installation

```bash
# Linux (Ubuntu/Debian/Kali) - included with Wireshark
sudo apt install wireshark-common -y

# Use tshark after installing Wireshark
```

### Basic tshark Usage

```bash
# Capture and display packets
sudo tshark -i eth0

# Capture and save to file
sudo tshark -i eth0 -w capture.pcap

# Read from file
tshark -r capture.pcap

# Apply display filter
tshark -r capture.pcap -Y "http"

# Show specific fields
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.port
```

### tshark Display Filters

```bash
# Show only HTTP packets
tshark -r capture.pcap -Y "http"

# Show only DNS queries
tshark -r capture.pcap -Y "dns.flags.response == 0"

# Show only SYN packets
tshark -r capture.pcap -Y "tcp.flags.syn == 1 and tcp.flags.ack == 0"

# Show only packets from 192.168.1.10
tshark -r capture.pcap -Y "ip.src == 192.168.1.10"
```

### tshark Field Extraction

**Show specific fields:**
```bash
# Show source IP, destination IP, and protocol
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e _ws.col.Protocol

# Show source IP, destination IP, and destination port
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport

# Show HTTP URI
tshark -r capture.pcap -Y "http" -T fields -e http.request.uri
```

**Export to CSV:**
```bash
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.port -E header=y -E separator=,
```

### tshark Statistics

```bash
# Protocol hierarchy
tshark -r capture.pcap -z io,phs

# Endpoints
tshark -r capture.pcap -z endpoints,ip

# Conversations
tshark -r capture.pcap -z conv,ip

# IO Graph
tshark -r capture.pcap -z io,stat,1,"COUNT(tcp.flags.syn==1) tcp SYN"
```

### tshark Examples

**Count HTTP requests:**
```bash
tshark -r capture.pcap -Y "http.request" | wc -l
```

**List unique source IPs:**
```bash
tshark -r capture.pcap -T fields -e ip.src | sort | uniq -c | sort -nr
```

**List top 10 talkers:**
```bash
tshark -r capture.pcap -T fields -e ip.src -e ip.dst | sort | uniq -c | sort -nr | head -10
```

**Find suspicious IPs:**
```bash
# IPs with many connections
tshark -r capture.pcap -T fields -e ip.src | sort | uniq -c | sort -nr

# IPs with many unique destination ports
tshark -r capture.pcap -T fields -e ip.src -e tcp.dstport | sort | uniq -c | sort -nr
```

**Extract HTTP URIs:**
```bash
tshark -r capture.pcap -Y "http.request" -T fields -e http.request.uri
```

---

## 11. Hands-On Lab: Detect Attacks with Wireshark

### Lab Overview

In this lab you will capture and analyze traffic to detect common attacks:
1. Port scanning
2. ARP spoofing
3. Suspicious traffic patterns

### Setup

- **Kali Linux** (attacker)
- **Metasploitable 2** (target)
- Both on Host-Only network (192.168.56.0/24)

---

### Part 1: Detect Port Scanning

**Step 1: Start Wireshark capture on Kali**

1. Open Wireshark
2. Select the Host-Only interface (eth0 or similar)
3. Click the blue shark fin to start capture
4. Apply display filter: `tcp.flags.syn == 1 and tcp.flags.ack == 0`

**Step 2: Run Nmap SYN scan from Kali**

```bash
nmap -sS -p- 192.168.56.40
```

**Step 3: Observe Wireshark**

You should see many SYN packets to different ports on the target.

**What You Should See:**
```
No. Time     Source          Destination     Protocol Info
1   0.00     192.168.56.10   192.168.56.40   TCP      54321 → 21 [SYN]
2   0.01     192.168.56.10   192.168.56.40   TCP      54322 → 22 [SYN]
3   0.02     192.168.56.10   192.168.56.40   TCP      54323 → 23 [SYN]
4   0.03     192.168.56.10   192.168.56.40   TCP      54324 → 25 [SYN]
...
```

**Detection Indicators:**

| Indicator | Meaning |
|---|---|
| Multiple SYN packets from same source to different ports | Port scan in progress |
| SYN packets to sequential ports | Sequential scan |
| SYN packets to common ports (22, 80, 443, etc.) | Service scan |
| SYN without ACK | SYN scan (stealth) |

**Step 4: Analyze with statistics**

```
Statistics → I/O Graph → Add filter:
"tcp.flags.syn == 1 and tcp.flags.ack == 0"
```

You should see a spike in SYN traffic during the scan.

**Step 5: Save the capture**

Save as `lab_13_port_scan.pcapng`

**Deliverable:** Screenshot of Wireshark showing port scan traffic.

---

### Part 2: Detect ARP Spoofing

**Step 1: Start Wireshark capture on Kali**

**Step 2: Start ARP spoofing on Kali (if using Bettercap)**

```bash
sudo bettercap
set arp.spoof.targets 192.168.56.40
set arp.spoof.fullduplex true
arp.spoof on
```

**Step 3: Observe ARP traffic**

Apply display filter: `arp`

**What You Should See:**
```
No. Time     Source          Destination     Protocol Info
1   0.00     192.168.56.10   Broadcast       ARP      Who has 192.168.56.40? Tell 192.168.56.1
2   0.01     192.168.56.40   192.168.56.10   ARP      192.168.56.40 is at 11:22:33:44:55:66
3   0.50     192.168.56.10   Broadcast       ARP      Who has 192.168.56.40? Tell 192.168.56.1
4   0.51     192.168.56.40   192.168.56.10   ARP      192.168.56.40 is at 11:22:33:44:55:66
...
```

**Detection Indicators:**

| Indicator | Meaning |
|---|---|
| Repeated ARP requests for same IP | Possible scan |
| ARP replies from multiple MACs for same IP | Possible spoofing |
| ARP replies with different MACs | Possible ARP cache poisoning |
| ARP replies with MAC of another device | MITM attack |

**Step 4: Check ARP table on Kali**

```bash
arp -n
```

**Step 5: Stop ARP spoofing**

```bash
arp.spoof off
```

**Step 6: Save the capture**

Save as `lab_13_arp_spoof.pcapng`

**Deliverable:** Screenshot showing ARP spoofing traffic.

---

### Part 3: Detect Suspicious Traffic Patterns

**Step 1: Start Wireshark capture on Kali**

**Step 2: Perform multiple scans**

```bash
# Nmap SYN scan
nmap -sS -p 1-100 192.168.56.40

# Nmap UDP scan
nmap -sU -p 53,69,123,161 192.168.56.40

# Nmap OS fingerprinting
nmap -O 192.168.56.40

# Nmap Xmas scan
nmap -sX -p 1-100 192.168.56.40
```

**Step 3: Analyze with statistics**

**Protocol Hierarchy:**
```
Statistics → Protocol Hierarchy
Look for: Excessive TCP SYN, UDP, ICMP
```

**Endpoints:**
```
Statistics → Endpoints
Look for: Unusual IP addresses, high traffic volume
```

**Conversations:**
```
Statistics → Conversations
Look for: Large data transfers to unusual IPs
```

**Detection Indicators:**

| Indicator | Meaning |
|---|---|
| SYN packets to many ports | Port scan |
| UDP packets to many ports | UDP scan |
| ICMP requests to many hosts | Ping sweep |
| Packets with unusual flags (FIN, Xmas, Null) | Advanced scan |
| Consistent traffic on unusual ports | Possible C2 communication |

**Step 4: Use display filters**

```bash
# SYN scan detection
tcp.flags.syn == 1 and tcp.flags.ack == 0

# Xmas scan detection
tcp.flags.fin == 1 and tcp.flags.psh == 1 and tcp.flags.urg == 1

# Null scan detection
tcp.flags == 0

# Ping sweep detection
icmp.type == 8

# UDP scan detection
udp
```

---

### Part 4: Real-World Analysis Scenario

**Scenario:** You suspect an attacker is scanning your network. You have a pcap file.

**Step 1: Open the pcap**

```bash
tshark -r suspicious.pcap
```

**Step 2: Identify live hosts**

```bash
# Find all IPs in the capture
tshark -r suspicious.pcap -T fields -e ip.src -e ip.dst | sort | uniq

# Or use Wireshark:
Statistics → Endpoints → IPv4
```

**Step 3: Identify scanning behavior**

```bash
# Find SYN packets (port scan)
tshark -r suspicious.pcap -Y "tcp.flags.syn == 1 and tcp.flags.ack == 0" -T fields -e ip.src -e tcp.dstport | sort | uniq -c

# Find ICMP requests (ping sweep)
tshark -r suspicious.pcap -Y "icmp.type == 8" -T fields -e ip.src -e ip.dst | sort | uniq -c
```

**Step 4: Identify attacker IP**

```bash
# Find IPs with most SYN packets
tshark -r suspicious.pcap -Y "tcp.flags.syn == 1" -T fields -e ip.src | sort | uniq -c | sort -nr | head -5
```

**Step 5: Identify targeted ports**

```bash
# Find most scanned ports
tshark -r suspicious.pcap -Y "tcp.flags.syn == 1" -T fields -e tcp.dstport | sort | uniq -c | sort -nr | head -10
```

---

### Lab Deliverable

Create `lab_13_packet_analysis.md`:


# Lab 13: Packet Analysis with Wireshark

## Part 1: Port Scan Detection

### Captured Traffic
[Insert screenshot of SYN packets]

### Analysis
- Source IP: 192.168.56.10 (Kali)
- Target IP: 192.168.56.40 (Metasploitable)
- Scan type: SYN scan (-sS)
- Ports scanned: [list]

### Indicators
- Multiple SYN packets to different ports
- No ACK responses (stealth scan)

## Part 2: ARP Spoofing Detection

### Captured Traffic
[Insert screenshot of ARP traffic]

### Analysis
- Attacker IP: 192.168.56.10
- Target IP: 192.168.56.40
- Spoofed MAC: [MAC]

### Indicators
- Repeated ARP replies
- Multiple MACs for same IP
- Unusual ARP request frequency

## Part 3: Suspicious Traffic Analysis

### Protocol Hierarchy
- HTTP: [%]
- DNS: [%]
- SMB: [%]
- Other: [%]

### Top Talkers
| IP | Packets | Bytes |
|----|---------|-------|
| [IP] | [count] | [bytes] |

### Unusual Traffic
- [list any suspicious patterns]

## Reflection
- What did you learn about packet analysis?
- How would you detect these attacks in the real world?
- What additional analysis would you perform?

---

## 12. Summary & Key Takeaways

**Wireshark Fundamentals:**
- Three panes: Packet List, Packet Details, Packet Bytes
- Promiscuous mode: capture all traffic on segment
- Monitor mode: capture wireless traffic without connecting

**Capture Filters (BPF syntax):**
- `host`, `net`, `port`, `src`, `dst`
- Used BEFORE capture to limit saved packets
- Example: `tcp port 80`

**Display Filters (Wireshark syntax):**
- `ip.addr`, `tcp.port`, `http.request`, `tcp.flags.syn`
- Used AFTER capture to filter displayed packets
- Example: `ip.addr == 192.168.1.10 and http`

**Colorization Rules:**
- Customize to highlight suspicious traffic
- Examples: SYN flood, HTTP errors, DNS queries

**Stream Following:**
- Reconstructs entire conversations
- HTTP, TCP, UDP streams

**Protocol Analysis:**
- HTTP: requests, methods, URIs, user-agents
- DNS: queries, types, responses
- ARP: requests, replies, spoofing detection
- TCP: SYN, ACK, RST, FIN flags
- UDP: connectionless traffic

**Command-Line Tools:**
- tcpdump: capture packets
- tshark: analyze packets

---

## 13. Quiz

1. What is the difference between promiscuous mode and monitor mode?

2. What is the capture filter syntax to capture only HTTP traffic?

3. What display filter shows only DNS queries (not responses)?

4. How can you detect ARP spoofing in Wireshark?

5. What TCP flags indicate a SYN scan?

6. How can you follow a TCP stream in Wireshark?

7. What command captures SSH traffic to a file using tcpdump?

8. How can you list all unique source IPs from a pcap using tshark?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Promiscuous mode** captures all traffic on a network segment. **Monitor mode** captures wireless traffic without connecting to the network.

2. `port 80` or `tcp port 80`

3. `dns.flags.response == 0`

4. Look for:
   - Multiple ARP replies with different MACs for same IP
   - ARP replies with unexpected MACs
   - Repetitive ARP requests

5. SYN flag = 1, ACK flag = 0 (`tcp.flags.syn == 1 and tcp.flags.ack == 0`)

6. Right-click a packet → Follow → TCP Stream

7. `sudo tcpdump -i eth0 port 22 -w ssh_traffic.pcap`

8. `tshark -r capture.pcap -T fields -e ip.src | sort | uniq`

</details>

---

## 14. Resources

### Official Resources
- [Wireshark Official Site](https://www.wireshark.org)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [Display Filter Reference](https://www.wireshark.org/docs/dfref/)
- [tcpdump Manual](https://www.tcpdump.org/manpages/tcpdump.1.html)

### Additional Reading
- [Wireshark Network Security](https://wiki.wireshark.org/NetworkSecurity)
- [BPF Filter Syntax](https://biot.com/capstats/bpf.html)

### Practice
- [TryHackMe Wireshark Room](https://tryhackme.com/room/wireshark)
- [Malware Traffic Analysis](https://www.malware-traffic-analysis.net/)
- [Chris Sanders Wireshark Tutorials](https://www.chrissanders.org/category/wireshark/)

---

**Previous Module:** [Module 14 — Network Scanning with Nmap ](Module_14_Network_Scanning_with_Nmap.md)

**Next Module:** [Module 16 - Vulnerability Scanning ](Module_16_Vulnerability_Scanning.md)

---

*Module 15 | Phase 3: Scanning & Enumeration | cybersecurity_beginner_to_advance*
