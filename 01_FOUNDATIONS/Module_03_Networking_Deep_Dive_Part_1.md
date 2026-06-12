# Module 3: Networking Deep Dive (Part 1)

## Table of Contents
1. [Introduction](#introduction)
2. [OSI Model](#osi-model)
3. [TCP/IP Suite and Comparison](#tcpip-suite-and-comparison)
4. [TCP vs UDP](#tcp-vs-udp)
5. [TCP Handshake and Termination](#tcp-handshake-and-termination)
6. [IPv4 vs IPv6](#ipv4-vs-ipv6)
7. [Subnetting](#subnetting)
8. [MAC Addresses and ARP](#mac-addresses-and-arp)
9. [Hands-on: Wireshark Analysis](#hands-on-wireshark-analysis)
10. [Summary](#summary)

---

## Introduction

Networking is the foundation of cybersecurity. Every attack, defense, and investigation travels on a network.
If you want to understand how threats move, how to block them, and how to investigate incidents, you must understand how data moves, how devices identify each other, and how network sessions are established.

This module takes you through:
- OSI layers with protocols and devices
- TCP/IP protocols and how they map to OSI
- TCP vs UDP behavior and headers
- The TCP 3-way handshake and 4-way termination
- IPv4 and IPv6 differences and transition strategies
- Subnetting with CIDR, FLSM, and VLSM
- MAC addresses, ARP, and ARP tables
- Hands-on Wireshark analysis for handshake and ARP

---

## OSI Model

The OSI model is a conceptual framework that describes how data travels through a network. It divides networking into 7 layers, each handling a specific function. Understanding these layers helps you identify where attacks occur and where to apply defenses.

### Layer 1: Physical Layer (The Hardware)
- **Purpose**: Transmit raw bits (1s and 0s) over a physical medium
- **Data unit**: Bits
- **Example technologies**: 
  - Copper Ethernet cabling (Cat5, Cat6, Cat7)
  - Fiber optic cables (single-mode, multi-mode)
  - Wi-Fi radio frequencies (2.4 GHz, 5 GHz, 6 GHz)
  - DSL, cellular modems
- **Devices**: 
  - Network cables, connectors, fiber transceivers
  - Repeaters (extend signal distance)
  - Hubs (simple devices that broadcast to all ports)
  - Wireless access points (broadcast radio signals)
- **Real-world example**: When you plug an Ethernet cable into your laptop, that cable and the connector are Layer 1. The Wi-Fi signal from your router to your phone is Layer 1.
- **Speeds**: 
  - Fast Ethernet: 100 Mbps
  - Gigabit Ethernet: 1,000 Mbps (1 Gbps)
  - 10 Gigabit Ethernet: 10,000 Mbps (10 Gbps)
- **Security vulnerabilities**:
  - Physical cable cutting (data center attacks)
  - Wireless signal jamming
  - Packet sniffing on shared media (old Ethernet hubs)
  - Side-channel attacks (measuring power consumption, electromagnetic emissions)
- **Defense**: Physical security (locked data centers), encrypted wireless, segmented networks

### Layer 2: Data Link Layer (MAC and Frames)
- **Purpose**: Organize bits into frames and deliver them to devices on the same network (local network delivery)
- **Data unit**: Frame (typically 64-1518 bytes)
- **Key concept**: Uses MAC addresses to find devices on the local network
- **Example protocols**: 
  - Ethernet (most common LAN protocol, IEEE 802.3)
  - ARP (Address Resolution Protocol, maps IP to MAC)
  - Point-to-Point Protocol (PPP)
  - Wi-Fi 802.11 (multiple standards: 802.11a, b, g, n, ac, ax)
  - VLAN tagging (802.1Q) for segmenting networks
- **Devices**: 
  - Network switches (connect devices on the same network)
  - Bridges (connect different media types)
  - Network Interface Cards (NICs)
  - Wi-Fi access points (from Layer 2 perspective)
- **Real-world example**: Your computer needs to talk to the printer on your home network. Your computer knows the printer's IP (192.168.1.5), but to actually send the data, it needs the MAC address. ARP finds that. Then the switch on your router uses Layer 2 to deliver frames between them.
- **Security vulnerabilities**:
  - **ARP spoofing**: Attacker sends fake ARP messages to intercept traffic
  - **MAC flooding**: Send thousands of fake MAC addresses to overwhelm a switch's memory
  - **VLAN hopping**: Break out of a VLAN to access other networks
  - **Ethernet frame manipulation**: Craft malicious frames
- **Defense**: Port security on switches, static ARP entries, VLAN segmentation, monitor ARP traffic

### Layer 3: Network Layer (Routing and IP)
- **Purpose**: Route packets from source to destination across networks (possibly multiple hops)
- **Data unit**: Packet (contains IP headers + data)
- **Key concept**: Uses IP addresses to identify networks and devices across the internet
- **Example protocols**: 
  - IPv4 (current standard, 32-bit addresses)
  - IPv6 (next-generation, 128-bit addresses)
  - ICMP (ping, traceroute)
  - IPsec (encryption and authentication at the network layer)
  - OSPF, BGP (routing protocols that routers use to communicate with each other)
- **Devices**: 
  - Routers (decide where packets should go next)
  - Layer 3 switches (routers built into switches)
  - Firewalls (filter based on IP addresses and ports)
- **Real-world example**: You visit google.com. Your packet needs to go from your home network (192.168.1.0/24) to Google's network (somewhere on the internet). Routers along the way read your packet's destination IP and forward it to the next router until it reaches Google.
- **Hop limit**: TTL (Time To Live) field prevents packets from circulating forever. Decrements by 1 at each router.
- **Security vulnerabilities**:
  - **IP spoofing**: Attacker forges source IP address
  - **DDoS attacks**: Flood a target with millions of packets from many sources
  - **Routing attacks**: Corrupt routing tables to redirect traffic
  - **Man-in-the-middle**: Position yourself between two hosts to intercept traffic
- **Defense**: Firewalls, ACLs (Access Control Lists), DDoS mitigation, encrypted tunnels (VPN)

### Layer 4: Transport Layer (TCP/UDP and Ports)
- **Purpose**: Provide end-to-end communication and ensure reliable (TCP) or fast (UDP) delivery
- **Data unit**: Segment (TCP) or Datagram (UDP)
- **Key concepts**: 
  - TCP: Connection-oriented, reliable, ordered, error-checking
  - UDP: Connectionless, fast, unreliable, minimal overhead
  - Ports: Identify which application should receive the data (0-65,535)
- **Example protocols**: 
  - TCP (web, email, file transfer, remote login)
  - UDP (DNS, streaming, gaming, VoIP)
  - SCTP (telecom, reliable UDP-like)
  - DCCP (streaming media with some reliability)
- **Port ranges**:
  - Well-known ports (0-1023): HTTP (80), HTTPS (443), SSH (22), SMTP (25), DNS (53)
  - Registered ports (1024-49151): Often used by specific applications
  - Dynamic/private ports (49152-65535): Temporary, used by clients
- **Devices**: 
  - Firewalls (filter by port and protocol)
  - Load balancers (distribute traffic across servers)
  - NAT gateways (translate between private and public IPs)
- **Real-world example**: Your browser connects to www.example.com on port 443 (HTTPS). Your machine picks a random port from the dynamic range (like 54,321) for its side. The server sees your request coming from your IP:54321 to their IP:443.
- **Security vulnerabilities**:
  - **Port scanning**: Attacker probes ports to find open services
  - **SYN flood**: Attack TCP by sending many SYN packets without completing handshake
  - **UDP flood**: Overwhelm with UDP packets
  - **Exploit vulnerable services**: Old or unpatched software on open ports
- **Defense**: Firewalls with strict policies, close unnecessary ports, patch services, rate limiting

### Layer 5: Session Layer (Managing Conversations)
- **Purpose**: Establish, maintain, and cleanly terminate conversations between applications
- **Key concepts**: 
  - Session = conversation between two applications
  - Sessions have state (authenticated, authenticated as user X, etc.)
  - Session timeout prevents indefinite resource use
- **Example protocols**: 
  - TLS/SSL session establishment (handshake)
  - NetBIOS sessions (older Microsoft networks)
  - RPC (Remote Procedure Call) sessions
  - PPTP (virtual private network tunneling)
  - SMTP session management
- **Devices**: 
  - Proxy servers (intercept and manage sessions)
  - Session border controllers (manage SIP sessions for VoIP)
  - Firewalls with stateful inspection
- **Real-world example**: When you log into your email, the server creates a session for you. It remembers "this is john@example.com and they're authenticated." You don't need to enter your password on every click. When you log out or the session times out (usually 30 minutes), the session ends.
- **Session tokens/cookies**: 
  - Browser stores a session ID (cookie)
  - Server uses that ID to recall who you are
  - If an attacker steals your cookie, they can impersonate you
- **Security vulnerabilities**:
  - **Session hijacking**: Attacker steals or intercepts your session cookie
  - **Fixation attacks**: Attacker sets your session ID to a known value
  - **Indefinite sessions**: Sessions that never timeout allow long-term attacks
  - **Cleartext session tokens**: Unencrypted cookies over HTTP can be sniffed
- **Defense**: HTTPS only (encrypt cookies), short session timeouts, secure cookie flags (HttpOnly, Secure), regenerate session IDs after login

### Layer 6: Presentation Layer (Formatting and Encryption)
- **Purpose**: Translate, format, encrypt, and compress data so both sides understand it
- **Key concepts**: 
  - Handles character encoding (ASCII, Unicode, UTF-8)
  - Handles data format conversion (endianness)
  - Encrypts sensitive data
  - Compresses data to save bandwidth
- **Example protocols**: 
  - SSL/TLS (encryption for HTTPS, SSH, VPN)
  - JPEG, PNG, GIF (image compression formats)
  - MPEG (video compression)
  - MIME (email content type declaration)
  - Unicode, ASCII (character encoding)
- **Devices**: 
  - SSL/TLS offload accelerators (encrypt/decrypt at network edge)
  - VPN concentrators (encrypt data tunnels)
  - Media gateways (convert between formats)
- **Real-world example**: You send an encrypted email. Your client encrypts the message, compresses it, and sends it. The server receives the encrypted, compressed data and decrypts/decompresses it for viewing.
- **Security considerations**:
  - Encryption strength (256-bit AES vs weak RC4)
  - Compression algorithms (some enable attacks like CRIME)
  - Character encoding issues (UTF-8 vs ASCII, international characters)
- **Security vulnerabilities**:
  - **Weak encryption**: Using outdated algorithms (DES, MD5)
  - **Compression oracle attacks**: Compress then encrypt can leak information
  - **Character encoding exploits**: Unicode exploits for bypassing filters
- **Defense**: Use strong encryption (AES-256, TLS 1.3), disable compression in TLS, validate input encoding

### Layer 7: Application Layer (The User's View)
- **Purpose**: Directly serve user and application needs (web, email, file transfer, etc.)
- **Key concepts**: 
  - What the user directly interacts with
  - Home for most business logic and vulnerabilities
- **Example protocols**: 
  - HTTP/HTTPS (web browsing)
  - DNS (translating names to IPs)
  - SMTP (sending email)
  - POP3/IMAP (receiving email)
  - FTP/SFTP (file transfer)
  - SSH (secure remote access)
  - Telnet (insecure remote access, legacy)
  - LDAP (directory services, Active Directory)
  - SNMP (network management)
- **Devices**: 
  - Web servers (Apache, Nginx, IIS)
  - Mail servers (Exchange, Postfix)
  - DNS servers (BIND, Windows DNS)
  - FTP servers
  - SSH servers
  - Client applications (browsers, mail clients)
- **Real-world example**: When you log into your company's intranet, you're using HTTP/HTTPS (Layer 7) to send your username and password. The web server validates those credentials, possibly checking against LDAP (also Layer 7). Then it serves you a personalized page.
- **Security vulnerabilities** (this is where most attacks happen):
  - **SQL injection**: Inject database commands through user input
  - **Cross-Site Scripting (XSS)**: Inject JavaScript into pages
  - **Cross-Site Request Forgery (CSRF)**: Trick users into making unwanted requests
  - **Phishing**: Trick users into revealing credentials
  - **Buffer overflows**: Overflow input buffers in vulnerable code
  - **Malware delivery**: Malicious downloads, drive-by downloads
  - **Broken authentication**: Weak passwords, session management issues
- **Defense**: Input validation, output encoding, principle of least privilege, security updates, firewalls at Layer 7 (WAF - Web Application Firewall), user education

### OSI Model Quick Reference
| Layer | Name | Data Unit | Purpose | Examples |
|---|---|---|---|---|
| 7 | Application | Data | User services | HTTP, DNS, SMTP |
| 6 | Presentation | Data | Encrypt, format, compress | SSL/TLS, JPEG |
| 5 | Session | Data | Maintain conversations | TLS sessions, RPC |
| 4 | Transport | Segment/Datagram | End-to-end delivery | TCP, UDP |
| 3 | Network | Packet | Routing | IPv4, IPv6, ICMP |
| 2 | Data Link | Frame | Local delivery | Ethernet, ARP |
| 1 | Physical | Bit | Raw transmission | Cables, radio |

### Understanding Data Flow with OSI

When you send an HTTPS email to a friend:

1. **Layer 7 (Application)**: Email client composes message
2. **Layer 6 (Presentation)**: Message encrypted (TLS), compressed
3. **Layer 5 (Session)**: Establish secure session with mail server
4. **Layer 4 (Transport)**: Segment into TCP segments, port 587
5. **Layer 3 (Network)**: Add source/destination IP addresses
6. **Layer 2 (Data Link)**: Add source/destination MAC addresses, form frames
7. **Layer 1 (Physical)**: Convert to electrical signals, send over wire

**Receiving**: This process reverses, stripping off headers layer by layer.

---

## TCP/IP Suite and Comparison

The TCP/IP suite is the practical model used by the internet. It is simpler than OSI and combines some layers.

### TCP/IP layers
- Application: HTTP, HTTPS, DNS, SMTP, FTP, SSH
- Transport: TCP, UDP
- Internet: IPv4, IPv6, ICMP, IGMP
- Link (Network Access): Ethernet, Wi-Fi, PPP

### Comparison with OSI

| TCP/IP Layer | OSI Equivalent | Common Protocols | Typical Devices |
|---|---|---|---|
| Application | Session + Presentation + Application | HTTP, DNS, SMTP, SSH, FTP | Web browsers, mail servers |
| Transport | Transport | TCP, UDP | Firewalls, load balancers |
| Internet | Network | IPv4, IPv6, ICMP | Routers |
| Link | Physical + Data Link | Ethernet, Wi-Fi, ARP | Switches, NICs, hubs |

### Why TCP/IP matters in cybersecurity
- Most security tools work on TCP/IP: firewalls, IDS/IPS, VPNs
- TCP/IP maps to real network behavior, whereas OSI is a teaching model
- Understanding both models helps you diagnose where attacks occur

---

## TCP vs UDP

TCP and UDP are the two main transport protocols. Choosing between them is critical for application design and understanding network attacks.

### TCP: Transmission Control Protocol (Reliable)

**Core Characteristics**:
- **Connection-oriented**: Must establish a connection before sending data (3-way handshake)
- **Reliable delivery**: Guarantees data arrives without loss or duplication
- **Ordered delivery**: Packets arrive in the order sent
- **Flow control**: Sender doesn't overwhelm receiver
- **Congestion control**: Backs off when network is congested
- **Error checking**: Detects and corrects corrupted data
- **Overhead**: 20-60 bytes of header per packet

**TCP Header Structure** (20 bytes minimum):
```
Source Port (16 bits) | Destination Port (16 bits)
Sequence Number (32 bits)
Acknowledgment Number (32 bits)
Data Offset (4 bits) | Reserved (6 bits) | Flags (6 bits) | Window Size (16 bits)
Checksum (16 bits) | Urgent Pointer (16 bits)
Options (0-40 bytes) | Padding
```

**TCP Flags** (the 6 control bits):
- **SYN**: Synchronize (start connection)
- **ACK**: Acknowledgment (confirm receipt)
- **FIN**: Finish (end connection)
- **RST**: Reset (abruptly close connection)
- **PSH**: Push (send data immediately, don't wait for buffer to fill)
- **URG**: Urgent (data needs immediate attention)

**Sequence Numbers**: Each byte of data is numbered. Allows:
- Detecting missing packets
- Reassembling out-of-order packets
- Preventing replay attacks

**Example Use Cases**:
- **HTTP/HTTPS**: Web browsing (accuracy critical)
- **SMTP**: Email sending (must not lose messages)
- **POP3/IMAP**: Email retrieval (need all emails)
- **FTP/SFTP**: File transfer (corrupted files are useless)
- **SSH**: Remote terminal (every keystroke matters)
- **Telnet**: Remote login (legacy, insecure)
- **MySQL, PostgreSQL**: Database connections (consistency critical)

**TCP Performance**:
- Initial setup: 3-way handshake adds 1 extra round trip (~100ms on internet)
- Each lost packet triggers retransmission (slower)
- Slower for latency-sensitive apps
- Better for large data transfers (ensures completeness)

**Why TCP Matters in Security**:
- Session tracking: Firewalls can see full conversations
- Sequence numbers can be predicted (old vulnerability)
- Connection state helps detect scans and attacks
- Retransmission can be exploited (duplicate packets)

---

### UDP: User Datagram Protocol (Fast)

**Core Characteristics**:
- **Connectionless**: Send data immediately without handshake
- **Unreliable delivery**: Doesn't guarantee arrival
- **No ordering**: Packets might arrive out of order
- **No flow control**: Sender can overwhelm receiver
- **Minimal overhead**: 8 bytes of header per packet
- **Low latency**: No connection setup delay
- **Broadcasting**: Can send to multiple recipients

**UDP Header Structure** (8 bytes):
```
Source Port (16 bits) | Destination Port (16 bits)
Length (16 bits) | Checksum (16 bits)
```

That's it! Much simpler than TCP.

**Example Use Cases**:
- **DNS**: Domain name lookups (fast, if lost, just retry)
- **DHCP**: IP address assignment (fire-and-forget)
- **NTP**: Time synchronization (periodic updates)
- **VoIP**: Voice over IP (occasional lost packets acceptable)
- **Online gaming**: Real-time movement (old data is useless)
- **Video streaming**: Netflix, YouTube (buffer hides losses)
- **Live streaming**: Twitch, YouTube Live (occasional glitch acceptable)
- **Multiplayer games**: Counter-Strike, Fortnite (speed > accuracy)
- **SNMP**: Network monitoring (periodic checks, not critical)
- **CoAP**: Internet of Things (very limited bandwidth)

**UDP Performance**:
- No connection setup: Send immediately
- No retransmission: Lost packets are gone (no slowdown)
- Faster for latency-sensitive apps
- Lower bandwidth (smaller headers)
- But higher packet loss tolerance required

**Why UDP Matters in Security**:
- UDP floods: Easy DDoS attack (just spam packets)
- Stateless: Harder for firewalls to track
- Spoofing: Easy to fake source IP with UDP
- DNS hijacking: DNS uses UDP, replies can be intercepted
- NTP amplification attacks: Use NTP servers to amplify traffic

---

### TCP vs UDP: Detailed Comparison

| Feature | TCP | UDP |
|---------|-----|-----|
| **Connection Setup** | 3-way handshake | None |
| **Reliability** | 100% delivery guaranteed | Best effort, can lose packets |
| **Packet Order** | Guaranteed | Not guaranteed |
| **Speed** | Slower (due to checks) | Faster (minimal checks) |
| **Header Size** | 20+ bytes | 8 bytes |
| **Flow Control** | Yes (sender respects receiver's speed) | No |
| **Congestion Control** | Yes (backs off when network busy) | No |
| **Broadcasting** | No | Yes (can send to all) |
| **Use Case** | Accuracy critical | Speed critical |
| **Protocol Type** | Stateful | Stateless |
| **Retransmission** | Yes (resend lost packets) | No (lost = lost) |
| **Latency** | Higher (20-200ms+) | Lower (< 10ms typical) |
| **Bandwidth Overhead** | Higher (all the confirmations) | Lower (minimal) |

---

### Choosing TCP vs UDP

**Use TCP when**:
- Data integrity is critical (banking, medical)
- You need guaranteed delivery (file transfer, email)
- Order matters (transferring files in order)
- You can tolerate some latency (web browsing is fine with 200ms delay)
- You need connection state tracking (firewall rules)

**Use UDP when**:
- Speed is critical (gaming, VoIP)
- Occasional loss is acceptable (streaming video)
- Broadcasting or multicasting needed
- Minimal overhead required (IoT, sensor networks)
- Connectionless is simpler (DNS queries)

**Real-world hybrid approach**:
Some modern applications use both:
- **QUIC protocol**: TCP-like reliability over UDP (used by HTTP/3)
- **Game servers**: TCP for login, TCP for account data, UDP for live gameplay
- **Streaming services**: TCP to download video metadata and quality info, UDP for actual video stream

---

### TCP and UDP Packet Size Limits

**TCP**: 
- Maximum Segment Size (MSS) typically 1460 bytes (1500-byte Ethernet frame minus headers)
- Application can send larger, TCP breaks into segments
- If packet too large, retransmitted individually

**UDP**:
- Maximum datagram size: 65,535 bytes total (including 8-byte header)
- Practical limit: 1472 bytes (to fit in one Ethernet frame)
- Datagrams larger than MTU will be fragmented at Layer 3
- Fragmentation makes UDP unreliable (any fragment loss = whole datagram lost)
- Best practice: Keep UDP packets under 1472 bytes

---

### TCP Window Size and Performance

TCP uses a "sliding window" for flow control:
- Sender can send up to "window size" bytes before waiting for acknowledgment
- Receiver tells sender "I can accept 65,536 bytes"
- Larger window = better performance for high-bandwidth links
- On slow networks, window size may bottleneck throughput

**Example**: 
- Latency: 100ms round trip
- Window size: 65,536 bytes
- Max throughput: 65,536 bytes / 0.1 seconds = 655 KB/sec
- Even with 100 Mbps connection, slow latency limits speed

This is why TCP Windowscaling and TCP Fast Open were developed.

---

## TCP Handshake and Termination

TCP must establish a reliable connection before exchanging application data. This process is critical for security and performance.

### 3-Way TCP Handshake

The handshake synchronizes sequence numbers and confirms both sides are ready.

**Step 1: SYN (Client → Server)**
- Client sends a packet with:
  - **SYN flag** set (Synchronization flag)
  - **Sequence number**: X (random initial number, e.g., 1000)
  - **MSS (Maximum Segment Size)**: Usually 1460 bytes
  - **Window Size**: How many bytes client can receive
- Purpose: "Hey server, I want to connect. My initial sequence is 1000."
- Packet size: ~60 bytes

**Step 2: SYN-ACK (Server → Client)**
- Server responds with:
  - **SYN flag** set (server is also synchronizing)
  - **ACK flag** set (acknowledging client's sequence)
  - **Sequence number**: Y (random, e.g., 2000)
  - **Acknowledgment number**: X+1 (e.g., 1001) - "I received up to sequence 1000"
  - **MSS**: Server's maximum segment size
  - **Window Size**: How many bytes server can receive
- Purpose: "Got your request. My initial sequence is 2000. I confirm I received sequence 1000."
- Packet size: ~60 bytes

**Step 3: ACK (Client → Server)**
- Client sends:
  - **ACK flag** set
  - **Sequence number**: X+1 (e.g., 1001)
  - **Acknowledgment number**: Y+1 (e.g., 2001) - "I received up to sequence 2000"
  - Often includes first piece of application data
- Purpose: "Confirmed. I received your sequence 2000. Connection is open."
- Packet size: ~40 bytes + application data

**Why This Design**:
1. **Synchronize sequences**: Both sides know the other's starting sequence number
2. **Prevent duplicate packets**: Old packets with old sequence numbers are rejected
3. **Verify reachability**: Server confirms it can send back to client
4. **Establish parameters**: Agree on MSS, window size, options

**Sequence Number Details**:
- Sequence number is per-direction (different for each direction)
- Increments by the number of bytes sent
- Wraps around after 4,294,967,295 (2^32)
- Used to detect:
  - Missing packets
  - Out-of-order packets
  - Duplicate packets
  - Old packets from previous connections

**Example with real numbers**:
```
Client                                Server
|                                      |
|------ SYN (seq=1000) -------->      |
|                                |
|      <------ SYN-ACK (seq=2000, ack=1001) ------|
|                                |
|------ ACK (seq=1001, ack=2001) ----->|
|------ DATA (seq=1001) -------->      |  [Connection established]
|      <------ ACK (ack=1009) ------|
|
```

**Timing**:
- Handshake typically takes 50-200ms for local network
- On internet: 100-300ms typical
- High-latency connections (satellite): 500ms+

---

### 4-Way TCP Termination (Graceful Close)

TCP uses a four-step process to properly close connections:

**Step 1: FIN (Client → Server)**
- Client sends packet with **FIN flag** set
- Sequence number and acknowledgment number continue normally
- Meaning: "I'm done sending. No more data from me."
- The client can still receive data from the server

**Step 2: ACK (Server → Client)**
- Server acknowledges the FIN
- ACK flag set with proper acknowledgment number
- Meaning: "I received your FIN. I acknowledge you're done sending."
- Server can still send data

**Step 3: FIN (Server → Client)**
- Server sends packet with **FIN flag** set
- Meaning: "I'm also done sending. No more data from me either."
- Client can no longer receive data from server

**Step 4: ACK (Client → Server)**
- Client acknowledges server's FIN
- ACK flag set with proper acknowledgment number
- Meaning: "I received your FIN. Connection is closed."

**Why It's 4 Steps**:
- TCP is **bidirectional**: Each side has its own send and receive streams
- Each side must close its own send stream independently
- You can close sending while still receiving (half-open connection)
- Allows sender to retransmit any remaining data before closing

**Example**:
```
Client                                Server
|                                      |
|---------- FIN (seq=X) --------->     |
|                                |
|      <--------- ACK (ack=X+1) -------|
|  [Client is done sending,           |
|   but can still receive]             |
|                                |
|      <--------- FIN (seq=Y) ---------|  [Server is done sending]
|                                |
|---------- ACK (ack=Y+1) --------->   |
|                                |  [Connection fully closed]
```

**TIME_WAIT State**:
- After closing, client enters TIME_WAIT state
- Waits 2× Maximum Segment Lifetime (MSL), typically 30-120 seconds
- Purpose: 
  - Ensure delayed packets from old connection don't confuse new connection
  - Allow final ACK to be retransmitted if lost
- This is why you might see "Address already in use" if you restart a server

---

### TCP Reset (RST) - Abrupt Close

A TCP **RST (Reset)** flag forcibly closes a connection:

**When RST is Sent**:
- Receiving data on a non-existent connection
- Receiving data on a closed port
- Protocol violation
- Administrative action (kill connection)
- Error condition

**Effect**:
- Immediate termination (no TIME_WAIT)
- Both sides stop immediately
- Unsent data is lost
- No graceful cleanup

**Security Implications**:
- Attacker can RST connections they observe (DoS)
- Firewalls sometimes send RST to block connections

---

### Security Implications of TCP Handshake/Termination

**SYN Flood Attack**:
- Attacker sends thousands of SYN packets with fake source IPs
- Server allocates resources for each (memory, connection slot)
- Server never receives ACK (Step 3 never happens)
- Server runs out of resources, denies legitimate connections
- **Defense**: SYN cookies, rate limiting, SYN proxy

**TCP Sequence Number Prediction** (Old Attack):
- If attacker knows sequence numbers, they can inject data
- Modern systems use randomized, unpredictable sequence numbers
- **Defense**: Randomized ISN (Initial Sequence Number)

**Connection Hijacking**:
- Attacker sniffs a connection and injects forged packets
- Requires predicting correct sequence numbers
- **Defense**: Encrypted connections (HTTPS, SSH)

**Half-Open Connections**:
- Attacker sends SYN then never completes handshake
- Server holds resources for unfinished connections
- Can be used as part of reconnaissance or DoS
- **Defense**: Connection timeout (usually 30-60 seconds)

**RST Injection**:
- Attacker sends RST to terminate legitimate connections
- Requires guessing sequence numbers (harder with randomization)
- **Defense**: Encrypted connections, proper firewall rules

**TCP Retransmission**:
- Lost packets are retransmitted
- Attacker can observe retransmission patterns
- Can be exploited by jamming or delaying
- **Defense**: Encryption prevents understanding of content

---

### Handshake Variations

**TCP Fast Open (TFO)**:
- Experimental feature (RFC 7413)
- Client can send data with the initial SYN packet
- Reduces latency by 1 round trip
- Requires pre-established connection token

**Simultaneous Open**:
- Both sides send SYN at same time
- Rare in practice, but TCP handles it
- Results in 3 packets instead of 3

---

## IPv4 vs IPv6

IP addresses identify devices on networks. IPv4 is the current standard but running out of addresses. IPv6 is the future.

### IPv4: The Current Standard

**Format**: Four decimal numbers 0-255 separated by dots
- Example: `192.168.1.100`
- Each number is an **octet** (8 bits)
- Total: 32 bits

**Total address space**: 2^32 = 4,294,967,296 addresses (~4.3 billion)
- Sounds like a lot, but:
  - Many addresses reserved for special purposes
  - Organizations request large blocks (don't use all)
  - Growth of IoT, mobile, cloud = explosive demand
  - **Result**: IPv4 addresses officially exhausted (IANA) as of 2011

**IPv4 Address Classes** (historical, rarely used today):
- Class A: 1.0.0.0 - 126.255.255.255 (16 million addresses each)
- Class B: 128.0.0.0 - 191.255.255.255 (65,536 addresses each)
- Class C: 192.0.0.0 - 223.255.255.255 (256 addresses each)
- Class D: 224.0.0.0 - 239.255.255.255 (Multicast)
- Class E: 240.0.0.0 - 255.255.255.255 (Reserved)

**Modern IPv4 Uses CIDR** (Classless Inter-Domain Routing):
- Notation: `192.168.1.0/24`
- `/24` means first 24 bits are network, last 8 bits are hosts
- `/16` means first 16 bits are network, last 16 bits are hosts
- `/32` is a single host

**Special IPv4 Addresses**:
- `0.0.0.0/8`: "This network" (only used as source during boot)
- `10.0.0.0/8`: Private network (large, up to ~16 million addresses)
- `127.0.0.0/8`: Loopback (127.0.0.1 = localhost, your own machine)
- `169.254.0.0/16`: Link-local (auto-configured when DHCP fails)
- `172.16.0.0/12`: Private network (medium, ~65,000 addresses)
- `192.168.0.0/16`: Private network (small, ~65,000 addresses, most common)
- `224.0.0.0/4`: Multicast (broadcast to multiple recipients)
- `255.255.255.255`: Broadcast (send to all devices on local network)

**IPv4 Private Addresses** (Not routable on internet):
These are the three ranges used in homes and offices:
- `10.0.0.0 - 10.255.255.255` (Class A private): Large organizations
- `172.16.0.0 - 172.31.255.255` (Class B private): Medium organizations
- `192.168.0.0 - 192.168.255.255` (Class C private): Small networks, homes

**IPv4 Header** (20 bytes minimum):
```
Version (4 bits) | Header Length (4 bits) | ToS (8 bits) | Total Length (16 bits)
Identification (16 bits) | Flags (3 bits) | Fragment Offset (13 bits)
TTL (8 bits) | Protocol (8 bits) | Header Checksum (16 bits)
Source IP (32 bits)
Destination IP (32 bits)
Options (variable)
```

**TTL (Time To Live)**:
- Decrements by 1 at each router hop
- Prevents packets from circulating forever
- Typical initial value: 64, 128, or 255
- When TTL reaches 0, packet is discarded
- Used by `tracert` command to map route

---

### IPv6: The Next Generation

**Format**: Eight groups of four hexadecimal digits separated by colons
- Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- Each group is 16 bits (2 bytes)
- Total: 128 bits
- Can use uppercase or lowercase hex

**Total address space**: 2^128 = ~3.4 × 10^38 addresses
- That's 7.9 × 10^28 addresses per person on Earth
- **Practically unlimited for any foreseeable future**

**IPv6 Notation Shortcuts**:
1. **Leading zeros**: `2001:0db8` can be `2001:db8`
2. **Consecutive zeros**: `2001:0db8:0000:0000:0000:8a2e:0370:7334` becomes `2001:db8::8a2e:370:7334`
   - The `::` replaces one sequence of all-zero groups
   - Can only use `::` once per address
3. **Compressed form**: `2001:db8::1` (equivalent to `2001:0db8:0000:0000:0000:0000:0000:0001`)

**IPv6 Address Types**:

1. **Unicast** (single recipient):
   - **Global unicast** (`2000::/3`): Publicly routable, like IPv4 public
   - **Link-local** (`fe80::/10`): Only works on local network, auto-assigned
   - **Unique local** (`fd00::/8`): Private, not routable on internet
   - **Loopback** (`::1`): Local host (like 127.0.0.1)

2. **Multicast** (`ff00::/8`): Send to multiple recipients
   - Example: `ff02::1` (all nodes on link)
   - Example: `ff02::2` (all routers on link)

3. **Anycast** (nearest recipient): Multiple servers with same address

**IPv6 Header** (40 bytes, simpler than IPv4):
```
Version (4 bits) | Traffic Class (8 bits) | Flow Label (20 bits)
Payload Length (16 bits) | Next Header (8 bits) | Hop Limit (8 bits)
Source Address (128 bits)
Destination Address (128 bits)
Extension Headers (optional)
```

**IPv6 Advantages**:
- **Larger address space**: No NAT needed
- **Simpler header**: Faster processing
- **IPSec built-in**: Optional in IPv4, mandatory in IPv6
- **Auto-configuration**: SLAAC assigns IPv6 without DHCP
- **Better multicast**: More efficient group communication
- **Flow labels**: Better QoS support
- **No fragmentation at source**: Hosts can't fragment; routers must

**IPv6 Auto-Configuration (SLAAC)**:
Without DHCP, an IPv6 host can:
1. Generate link-local address (fe80::...)
2. Check if it's unique on network
3. Request prefix from router (Router Advertisement)
4. Combine prefix + interface ID = global IPv6 address

---

### IPv4 vs IPv6: Detailed Comparison

| Feature | IPv4 | IPv6 |
|---------|------|------|
| Address bits | 32 | 128 |
| Total addresses | ~4.3 billion | ~3.4 × 10^38 |
| Notation | Decimal dots (192.168.1.1) | Hex colons (2001:db8::1) |
| Header size | 20-60 bytes (variable) | 40 bytes (fixed) |
| Broadcast | Yes (255.255.255.255) | No (uses multicast instead) |
| NAT needed | Usually yes | Usually no |
| Security protocol | Optional (IPSec) | Built-in (IPSec) |
| Address resolution | ARP (protocol) | NDP (neighbor discovery protocol) |
| Auto-config | Needs DHCP | SLAAC built-in |
| Fragmentation | By sender or routers | Only by sender |
| Checksum | Yes, in header | No, relies on Layer 2/4 |
| Multicast | Limited | Enhanced |
| QoS support | Basic | Better (flow labels) |
| Compatibility | Widespread | Growing |
| Current usage | ~99% | ~1% (growing) |

---

### IPv4 Subnetting and CIDR

**CIDR Notation** (`/prefix`):
- `/24` = 255.255.255.0 = 256 addresses, 254 usable
- `/25` = 255.255.255.128 = 128 addresses, 126 usable
- `/26` = 255.255.255.192 = 64 addresses, 62 usable
- `/27` = 255.255.255.224 = 32 addresses, 30 usable
- `/28` = 255.255.255.240 = 16 addresses, 14 usable
- `/29` = 255.255.255.248 = 8 addresses, 6 usable
- `/30` = 255.255.255.252 = 4 addresses, 2 usable (often for router-to-router)
- `/32` = Single host

**Calculating addresses**: 2^(32 - prefix length) = total addresses

**Examples**:
- `192.168.1.0/24`: 192.168.1.0 to 192.168.1.255 (use 192.168.1.1 to 192.168.1.254)
- `10.0.0.0/8`: 10.0.0.0 to 10.255.255.255 (huge!)
- `172.16.0.0/12`: 172.16.0.0 to 172.31.255.255 (medium)

---

### IPv6 Transition Mechanisms

Since not all internet infrastructure supports IPv6 yet, several transition mechanisms exist:

**1. Dual Stack**:
- Devices run both IPv4 and IPv6 simultaneously
- Most common approach
- Devices have both IPv4 and IPv6 addresses

**2. Tunneling**:
- IPv6 packets carried inside IPv4 packets
- Techniques:
  - **6to4**: Automatic tunnel, IPv4 address embedded in IPv6
  - **Teredo**: Tunnel through NAT and firewalls
  - **ISATAP**: Intra-Site Automatic Tunnel Addressing Protocol

**3. Translation**:
- Convert IPv4 to IPv6 and vice versa at gateway
- Techniques:
  - **NAT64**: Translates IPv6 addresses to IPv4
  - **DNS64**: Modifies DNS responses to provide translated addresses

**4. Application-level Proxy**:
- Application gateway handles conversion
- Example: Proxy translates HTTPS from IPv6 to IPv4 web server

**Real-world adoption**:
- Major sites (Google, Facebook, Apple) support IPv6
- ISPs slowly deploying
- Dual stack is transitional standard
- Full IPv6 adoption: estimated 2030s

---

## Subnetting

Subnetting divides a network into smaller sub-networks. This helps manage traffic, improve security, and reduce broadcast overhead.

### CIDR: Classless Inter-Domain Routing
CIDR uses a prefix length to define the network portion.
- /24 means 24 bits of network, 8 bits of hosts
- /26 means 26 bits of network, 6 bits of hosts

**Address count formula**:
- Total addresses: 2^(32 - prefix)
- Usable hosts: 2^(32 - prefix) - 2

**Examples**:
- /24 → 256 total, 254 usable
- /26 → 64 total, 62 usable
- /30 → 4 total, 2 usable

### FLSM: Fixed Length Subnet Mask
FLSM creates equal-size subnets. Use it when every subnet needs the same number of hosts.

**Example**: Split 192.168.10.0/24 into four equal subnets:
- 192.168.10.0/26 → 62 hosts
- 192.168.10.64/26 → 62 hosts
- 192.168.10.128/26 → 62 hosts
- 192.168.10.192/26 → 62 hosts

### VLSM: Variable Length Subnet Mask
VLSM creates subnets of different sizes to match actual host requirements.

**Example requirement**:
- 60 hosts
- 30 hosts
- 10 hosts

**Step 1**: Allocate the largest subnet.
- 60 hosts → needs /26 (62 usable)

**Step 2**: Allocate remaining space.
- 30 hosts → needs /27 (30 usable)
- 10 hosts → needs /28 (14 usable)

**Example allocation**:
- 192.168.10.0/26 → 192.168.10.1–62
- 192.168.10.64/27 → 192.168.10.65–94
- 192.168.10.96/28 → 192.168.10.97–110

### Subnet calculation example
Network: 10.0.0.0/24
Goal: Create two subnets for at least 50 hosts each.

1. /26 gives 62 usable hosts.
2. Subnets:
   - 10.0.0.0/26 → usable hosts 10.0.0.1–10.0.0.62, broadcast 10.0.0.63
   - 10.0.0.64/26 → usable hosts 10.0.0.65–10.0.0.126, broadcast 10.0.0.127

### Why subnetting matters in security
- Limits broadcast domains
- Segments traffic by department or function
- Reduces attack surface
- Helps enforce firewall rules

---

## MAC Addresses and ARP

### MAC address overview
A MAC address is a hardware identifier burned into a network interface card.
- Format: 00:1A:2B:3C:4D:5E
- Length: 48 bits
- Used on the Data Link layer
- Unique to each NIC

### How ARP works
ARP resolves an IP address to a MAC address on the same subnet.

1. Host A needs to send to IP 192.168.1.10.
2. Host A broadcasts an ARP request:
   - "Who has 192.168.1.10? Tell 192.168.1.5"
3. Host B replies with an ARP reply:
   - "192.168.1.10 is at 00:1A:2B:3C:4D:5E"
4. Host A stores that mapping in its ARP table and sends the packet to Host B's MAC.

### ARP table
The ARP table stores IP-to-MAC mappings.
Common fields:
- IP address
- MAC address
- Type (dynamic or static)

**View the ARP table**:
- Windows: rp -a
- Linux/macOS: ip neigh or rp -n

**Example entry**:
- 192.168.1.10 00-1A-2B-3C-4D-5E dynamic

### ARP security risks
- **ARP spoofing/poisoning**: Attacker sends fake ARP replies to intercept traffic.
- **ARP cache poisoning**: Victim stores a bogus IP-to-MAC mapping.
- **Mitigation**: use static ARP entries for critical systems, segment networks, and monitor ARP traffic.

### Gratuitous ARP
A host announces its own IP/MAC mapping without being asked.
Uses:
- Detect IP conflicts
- Inform switches and routers of a new MAC address

---

## Hands-on: Wireshark Analysis

Wireshark lets you observe real network traffic. Use it to inspect TCP handshakes, TCP termination, and ARP resolution.

### Capture setup
1. Install Wireshark from https://wireshark.org.
2. Run Wireshark as administrator.
3. Choose your active interface.
4. Click Start.

### Analyze TCP handshake
Use filters:
- 	cp.flags.syn == 1 && tcp.flags.ack == 0 for SYN packets
- 	cp.flags.syn == 1 && tcp.flags.ack == 1 for SYN/ACK packets
- 	cp.flags.ack == 1 && tcp.flags.syn == 0 for ACK packets

Step:
1. Open a browser and visit a website.
2. Find the SYN packet from your machine.
3. Follow the TCP stream to see the full handshake.

### Analyze TCP termination
Use filter:
- 	cp.flags.fin == 1 or 	cp.flags.rst == 1

Observe:
- FIN from one host
- ACK from the other
- FIN reply
- Final ACK

### Analyze ARP
Use filter:
- rp

Observe:
- ARP request broadcasts
- ARP reply unicasts
- IP-to-MAC resolution

### What to look for
- The client’s initial SYN and sequence number
- The server’s SYN/ACK and acknowledgment number
- The final ACK that completes the handshake
- The FIN/ACK sequence that closes the session
- ARP requests asking "Who has..."
- ARP replies providing the MAC address

### Practical Wireshark tips
- Use Follow TCP Stream to view the entire session.
- Use Statistics → Protocol Hierarchy to see traffic breakdown.
- Use Expert Info to find retransmissions or malformed packets.

---

## Summary

This module gives you the core networking knowledge every cybersecurity practitioner needs:
- OSI vs TCP/IP: how layers map and which protocols belong where
- TCP vs UDP: when reliability matters and when speed matters more
- TCP handshake/termination: how connections are created and closed
- IPv4 vs IPv6: address differences and transition strategies
- Subnetting: CIDR, FLSM, VLSM, and how to calculate subnets
- MAC addresses and ARP: how local communication works and where attacks begin
- Wireshark: how to inspect handshake and ARP traffic directly

### Key takeaways
- Networking is fundamental to both attacks and defenses.
- Understanding protocols and layers lets you pinpoint where a threat lives.
- Subnetting and address planning improve security and performance.
- ARP is a local network weakness that is often abused.
- Hands-on packet analysis with Wireshark turns theory into evidence.

---

## Next steps
- Practice subnetting problems and write out subnet tables.
- Capture and analyze TCP handshakes in Wireshark.
- Use rp -a to inspect your local ARP cache.
- Compare IPv4 and IPv6 addresses on your machine.
- Explore how firewalls filter TCP and UDP traffic.


---

**Previous Module:** [← Module 1 — Introduction to Cybersecurity](Module_01_Introduction_to_Cybersecurity.md)

**Next Module:** [Module 4 — Networking Deep Dive Part 2 →](Module_04_Networking_Deep_Dive_Part_2.md)

---

*Module 3 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
