# Module 3: Networking Deep Dive - Part 1

## Table of Contents
1. [Introduction](#introduction)
2. [OSI Model](#osi-model)
3. [TCP/IP Model](#tcpip-model)
4. [Network Protocols](#network-protocols)
5. [IP Addressing](#ip-addressing)
6. [Subnetting](#subnetting)
7. [Network Devices](#network-devices)
8. [Routing Basics](#routing-basics)
9. [Key Networking Concepts](#key-networking-concepts)
10. [Summary](#summary)

---

## Introduction

Networking is the backbone of cybersecurity. Understanding how data flows through networks, how devices communicate, and where vulnerabilities can exist is critical for any cybersecurity professional. This module provides a comprehensive foundation in networking concepts that will be essential throughout your cybersecurity journey.

### Why Networking Matters in Cybersecurity
- **Attack Surface**: Networks are where most cyber attacks originate
- **Threat Detection**: Understanding network traffic helps identify suspicious activity
- **Defense Mechanisms**: Firewalls, IDS/IPS systems rely on networking knowledge
- **Protocol Analysis**: Knowing protocols helps detect anomalies and exploits
- **Incident Response**: Network analysis is crucial for investigating breaches

---

## OSI Model

The **Open Systems Interconnection (OSI) Model** is a conceptual framework that describes how network communications occur. It consists of 7 layers that standardize network functions.

### The 7 Layers of the OSI Model

#### **Layer 7: Application Layer**
- **Function**: User applications and services
- **Protocols**: HTTP, HTTPS, FTP, SMTP, DNS, SSH, Telnet, POP3, IMAP
- **Devices**: Servers, clients
- **Data Unit**: Data
- **Cybersecurity Context**: 
  - Vulnerable to application-level attacks (SQL injection, XSS, CSRF)
  - WAF (Web Application Firewalls) operate here
  - User authentication vulnerabilities exist at this layer

**Example**: When you visit a website using HTTPS, the browser (application) communicates at Layer 7.

#### **Layer 6: Presentation Layer**
- **Function**: Data translation, encryption, compression
- **Protocols**: SSL/TLS (encryption), JPEG, MPEG, ASCII
- **Data Unit**: Data
- **Cybersecurity Context**:
  - Encryption happens here (though TLS/SSL often spans layers 6-7)
  - Data formatting issues can lead to vulnerabilities
  - Codec exploits are possible

**Example**: Compression and encryption of data before transmission.

#### **Layer 5: Session Layer**
- **Function**: Manages sessions/dialogs between applications
- **Protocols**: NetBIOS, RPC, PPTP
- **Data Unit**: Data
- **Cybersecurity Context**:
  - Session hijacking attacks
  - Session fixation vulnerabilities
  - Session timeout issues

**Example**: Maintaining a connection while you're logged into a banking website.

#### **Layer 4: Transport Layer**
- **Function**: End-to-end communication and reliability
- **Protocols**: TCP, UDP, SCTP
- **Devices**: Firewalls, load balancers
- **Data Unit**: Segments (TCP) / Datagrams (UDP)
- **Cybersecurity Context**:
  - Port scanning attacks (Nmap)
  - DDoS attacks target this layer
  - TCP/UDP flooding
  - Firewall rules operate here

**Key Difference**:
- **TCP (Transmission Control Protocol)**: Connection-oriented, reliable, ordered delivery (slower)
- **UDP (User Datagram Protocol)**: Connectionless, faster, no guarantee of delivery

**Example**: TCP ensures all data packets arrive in order; UDP is used for video streaming where speed matters more than perfection.

#### **Layer 3: Network Layer**
- **Function**: Routing and logical addressing
- **Protocols**: IP (IPv4, IPv6), ICMP, IGMP, IPSec
- **Devices**: Routers, Layer 3 switches
- **Data Unit**: Packets
- **Cybersecurity Context**:
  - IP spoofing attacks
  - DDoS attacks
  - Routing attacks
  - IPSec provides encryption at this layer

**Example**: Routers use IP addresses to determine the best path for data to reach its destination.

#### **Layer 2: Data Link Layer**
- **Function**: MAC addressing and physical transmission
- **Protocols**: Ethernet, PPP, Frame Relay, Wi-Fi (802.11)
- **Devices**: Switches, network interface cards (NICs), bridges
- **Data Unit**: Frames
- **Cybersecurity Context**:
  - ARP spoofing attacks
  - MAC flooding
  - VLAN hopping
  - Switch security issues

**Example**: Your computer's MAC address identifies it on a local network.

#### **Layer 1: Physical Layer**
- **Function**: Actual physical transmission of data
- **Protocols**: Ethernet cables, Fiber optics, Radio waves, DSL, Dial-up
- **Devices**: Cables, hubs, repeaters, modems
- **Data Unit**: Bits (0s and 1s)
- **Cybersecurity Context**:
  - Physical security concerns
  - Cable tapping
  - Electromagnetic eavesdropping

**Example**: The actual copper wires, fiber optic cables, or wireless signals transmitting data.

### OSI Model Memory Aid
**"Please Do Not Throw Sausage Pizza Away"**
- **P**hysical
- **D**ata Link
- **N**etwork
- **T**ransport
- **S**ession
- **P**resentation
- **A**pplication

---

## TCP/IP Model

The **TCP/IP Model** (also called the Internet Protocol Suite) is a more practical, simplified version of the OSI model with 4 or 5 layers.

### 4-Layer TCP/IP Model

| Layer | OSI Equivalent | Protocols | Purpose |
|-------|---|---|---|
| **Application** | Layers 5-7 | HTTP, FTP, SMTP, DNS, SSH, HTTPS | User applications and services |
| **Transport** | Layer 4 | TCP, UDP | Reliable/unreliable data delivery |
| **Internet** | Layer 3 | IP, ICMP, IGMP | Routing and logical addressing |
| **Link** | Layers 1-2 | Ethernet, PPP, Wi-Fi | Physical transmission |

### 5-Layer TCP/IP Model (More Detailed)

| Layer | Protocols | Devices |
|-------|-----------|---------|
| **Application** | HTTP, HTTPS, FTP, SMTP, DNS, SSH, Telnet | Servers, clients, web browsers |
| **Transport** | TCP, UDP, SCTP | Firewalls, load balancers |
| **Internet** | IPv4, IPv6, ICMP, IPSec, IGMP | Routers, Layer 3 switches |
| **Data Link** | Ethernet, PPP, Wi-Fi, Frame Relay | Switches, NICs, bridges |
| **Physical** | Cables, fiber optics, radio waves | Cables, hubs, repeaters |

### TCP/IP vs OSI: Key Differences

| Aspect | TCP/IP | OSI |
|--------|--------|-----|
| **Layers** | 4-5 layers | 7 layers |
| **Development** | Practical, internet-focused | Theoretical, general-purpose |
| **Usage** | Industry standard | Educational/reference model |
| **Session/Presentation** | Merged with Application | Separate layers |

---

## Network Protocols

### HTTP/HTTPS (Hypertext Transfer Protocol)
- **Layer**: Application (Layer 7)
- **Port**: HTTP (80), HTTPS (443)
- **Purpose**: Web browsing and data transfer
- **Difference**: HTTPS encrypts data using SSL/TLS
- **Cybersecurity Note**: HTTPS is essential for protecting sensitive data like passwords and credit cards

```
Request: GET / HTTP/1.1
Host: www.example.com
```

### DNS (Domain Name System)
- **Layer**: Application (Layer 7)
- **Port**: 53 (UDP/TCP)
- **Purpose**: Converts domain names to IP addresses
- **Example**: www.google.com → 142.250.185.46
- **Cybersecurity Threat**: DNS spoofing, DNS poisoning, DNS amplification attacks

### SMTP/POP3/IMAP (Email Protocols)
- **SMTP**: Simple Mail Transfer Protocol (sending emails) - Port 25, 587, 465
- **POP3**: Post Office Protocol (retrieving emails) - Port 110
- **IMAP**: Internet Message Access Protocol (managing emails) - Port 143
- **Cybersecurity**: Email spoofing, phishing, credential theft

### FTP/SFTP (File Transfer Protocol)
- **FTP**: Port 21 (unencrypted, deprecated)
- **SFTP**: SSH File Transfer Protocol - Port 22 (encrypted)
- **Purpose**: Transferring files between systems
- **Cybersecurity**: SFTP preferred over FTP due to encryption

### SSH (Secure Shell)
- **Layer**: Application (Layer 7)
- **Port**: 22
- **Purpose**: Secure remote access and command execution
- **Features**: Encrypted communication, public-key authentication
- **Cybersecurity**: Better than Telnet (unencrypted); resistant to eavesdropping

### Telnet
- **Port**: 23
- **Purpose**: Remote terminal access (DEPRECATED)
- **Why Deprecated**: All data sent in plaintext, including passwords
- **Cybersecurity Risk**: Extremely vulnerable, should never be used

### ICMP (Internet Control Message Protocol)
- **Layer**: Network (Layer 3)
- **Purpose**: Diagnostics and error reporting
- **Tools**: Ping, Traceroute
- **Cybersecurity**: ICMP floods, ping sweeps for reconnaissance

### SNMP (Simple Network Management Protocol)
- **Port**: 161 (UDP)
- **Purpose**: Monitoring and managing network devices
- **Cybersecurity**: SNMP v1/v2 send credentials in plaintext; use SNMP v3 with encryption

### IPSec (Internet Protocol Security)
- **Layer**: Network (Layer 3)
- **Purpose**: Encryption and authentication at the network layer
- **Components**: Authentication Header (AH), Encapsulating Security Payload (ESP)
- **Modes**: Transport mode, Tunnel mode
- **Use Case**: VPNs, secure site-to-site communication

### DHCP (Dynamic Host Configuration Protocol)
- **Port**: 67/68 (UDP)
- **Purpose**: Automatically assigns IP addresses to devices
- **Cybersecurity**: DHCP spoofing, DHCP starvation attacks

---

## IP Addressing

### IPv4 (Internet Protocol Version 4)

#### Structure
- **Format**: Four octets separated by dots (Dotted Decimal Notation)
- **Example**: 192.168.1.1
- **Range**: 0.0.0.0 to 255.255.255.255
- **Total Addresses**: 2^32 = 4.3 billion addresses

#### Address Classes (Classful Addressing)

| Class | Range | Default Subnet Mask | # of Networks | # of Hosts | Use |
|-------|-------|---|---|---|---|
| **A** | 1.0.0.0 - 126.255.255.255 | 255.0.0.0 | 128 | 16.7M | Large networks |
| **B** | 128.0.0.0 - 191.255.255.255 | 255.255.0.0 | 16,384 | 65,536 | Medium networks |
| **C** | 192.0.0.0 - 223.255.255.255 | 255.255.255.0 | 2M | 254 | Small networks |
| **D** | 224.0.0.0 - 239.255.255.255 | N/A | Multicast | - | Multicast |
| **E** | 240.0.0.0 - 255.255.255.255 | N/A | Reserved | - | Reserved |

#### Special IP Addresses

| Address | Purpose |
|---------|---------|
| **0.0.0.0** | Current network (this network) |
| **255.255.255.255** | Broadcast address (all devices) |
| **127.0.0.1** | Localhost/Loopback |
| **169.254.x.x** | Link-local addresses (automatic) |
| **224.0.0.0 - 239.255.255.255** | Multicast addresses |

#### Private IP Ranges (Non-routable on Internet)

| Class | Range | Subnet Mask |
|-------|-------|---|
| **A** | 10.0.0.0 - 10.255.255.255 | 10.0.0.0/8 |
| **B** | 172.16.0.0 - 172.31.255.255 | 172.16.0.0/12 |
| **C** | 192.168.0.0 - 192.168.255.255 | 192.168.0.0/16 |

### IPv6 (Internet Protocol Version 6)

#### Structure
- **Format**: Eight 16-bit hexadecimal fields separated by colons
- **Example**: 2001:0db8:85a3:0000:0000:8a2e:0370:7334
- **Shortened**: 2001:db8:85a3::8a2e:370:7334
- **Total Addresses**: 2^128 (essentially unlimited)

#### Key Features
- **128-bit address space**: Over 340 undecillion unique addresses
- **No NAT needed**: Every device can have a globally unique address
- **Built-in security**: IPSec is mandatory in IPv6
- **Simplified header**: Easier to process than IPv4
- **Auto-configuration**: Devices can auto-configure addresses

#### IPv6 Special Addresses

| Address | Purpose |
|---------|---------|
| **::1** | Loopback (equivalent to 127.0.0.1) |
| **::** | All zeros address |
| **fe80::/10** | Link-local addresses |
| **ff00::/8** | Multicast addresses |
| **fc00::/7** | Unique local addresses (private) |

#### IPv4-Mapped IPv6 Addresses
- Format: `::ffff:192.0.2.1`
- Used for IPv4-to-IPv6 transitions

---

## Subnetting

Subnetting is the process of dividing a large network into smaller subnetworks.

### Why Subnetting?
- **Reduce network traffic**: Broadcast domains become smaller
- **Improve security**: Segment network by department/function
- **Efficient IP usage**: Use IP addresses more effectively
- **Better organization**: Manage networks logically

### CIDR Notation (Classless Inter-Domain Routing)

CIDR notation combines the IP address with the number of network bits.

**Format**: `IP/Prefix Length`

**Example**: `192.168.1.0/24`
- IP Address: 192.168.1.0
- /24 means first 24 bits are the network, remaining 8 bits are for hosts

### Subnet Mask Conversion

#### Binary to Decimal
```
255       .     255       .     255       .     0
11111111  .  11111111  .  11111111  .  00000000
```

#### CIDR to Subnet Mask
| CIDR | Subnet Mask | # of Hosts |
|------|---|---|
| /32 | 255.255.255.255 | 1 |
| /31 | 255.255.255.254 | 2 |
| /30 | 255.255.255.252 | 4 |
| /29 | 255.255.255.248 | 8 |
| /28 | 255.255.255.240 | 16 |
| /27 | 255.255.255.224 | 32 |
| /26 | 255.255.255.192 | 64 |
| /25 | 255.255.255.128 | 128 |
| /24 | 255.255.255.0 | 256 |
| /23 | 255.255.254.0 | 512 |
| /22 | 255.255.252.0 | 1,024 |
| /16 | 255.255.0.0 | 65,536 |
| /8 | 255.0.0.0 | 16.7M |

### Subnetting Example

**Given**: 192.168.1.0/24 network with subnet mask 255.255.255.0

**Step 1**: Calculate number of hosts per subnet
- Each octet = 8 bits
- Last octet (host bits) = 8
- Total hosts per subnet = 2^8 = 256

**Step 2**: Calculate usable hosts
- Network address: 192.168.1.0 (first address - reserved)
- Broadcast address: 192.168.1.255 (last address - reserved)
- Usable hosts: 256 - 2 = 254

**Step 3**: Subnet breakdown
- **Subnet 1**: 192.168.1.0/24
  - Network: 192.168.1.0
  - First host: 192.168.1.1
  - Last host: 192.168.1.254
  - Broadcast: 192.168.1.255

### Subnetting a Class C Network

**Scenario**: Divide 192.168.1.0/24 into 4 subnets

**Solution**: Use /26 (instead of /24)
- 2^2 = 4 subnets
- 2^6 = 64 addresses per subnet
- 62 usable hosts per subnet

**Subnets**:
1. 192.168.1.0/26 (Hosts: .1 to .62)
2. 192.168.1.64/26 (Hosts: .65 to .126)
3. 192.168.1.128/26 (Hosts: .129 to .190)
4. 192.168.1.192/26 (Hosts: .193 to .254)

---

## Network Devices

### Router
- **Function**: Connects different networks and routes traffic between them
- **OSI Layer**: Network (Layer 3)
- **Key Task**: Uses routing tables to forward packets based on destination IP
- **Ports**: WAN (to internet), LAN (to internal network)
- **Example**: Your home router connects your local network to the internet

### Switch
- **Function**: Connects devices within the same network and forwards frames
- **OSI Layer**: Data Link (Layer 2) or Network (Layer 3) for advanced switches
- **Key Task**: Uses MAC address table (CAM table) to forward Ethernet frames
- **Ports**: Multiple ethernet ports for device connections
- **Advantage over Hub**: Creates point-to-point connections (reduces collision domain)

### Firewall
- **Function**: Controls incoming and outgoing network traffic based on rules
- **Types**: 
  - **Stateless**: Examines each packet independently
  - **Stateful**: Tracks connection states
  - **Application-level**: Inspects application data (Deep Packet Inspection - DPI)
- **OSI Layer**: Operates at multiple layers (2-7)
- **Rules**: Allow/Deny based on IP, port, protocol

### Load Balancer
- **Function**: Distributes network traffic across multiple servers
- **Benefit**: Improves performance, availability, and fault tolerance
- **Methods**: Round-robin, least connections, IP hash
- **Layer**: Network (Layer 3) or Application (Layer 7)

### Proxy Server
- **Function**: Acts as intermediary between clients and servers
- **Types**:
  - **Forward Proxy**: Clients connect through proxy to internet
  - **Reverse Proxy**: External traffic goes through proxy to internal servers
- **Benefits**: Caching, filtering, anonymity, content inspection

### VPN Concentrator
- **Function**: Manages multiple VPN connections
- **Protocol**: IPSec, SSL/TLS
- **Use**: Remote workers securely accessing corporate network

### Access Point (AP)
- **Function**: Provides wireless connectivity
- **Standard**: 802.11 (WiFi)
- **Frequency**: 2.4 GHz, 5 GHz, 6 GHz (WiFi 6E)
- **Security**: WPA3 encryption recommended

### Intrusion Detection/Prevention System (IDS/IPS)
- **IDS**: Detects suspicious network activity and alerts
- **IPS**: Detects and blocks suspicious activity
- **OSI Layer**: Network and Application layers
- **Methods**: Signature-based, anomaly-based, behavior-based

---

## Routing Basics

### What is Routing?
Routing is the process of forwarding data packets from a source to a destination across one or more networks using routing tables and protocols.

### Routing Table
A routing table contains:
- **Destination**: Target IP address/network
- **Netmask**: Subnet mask for the destination
- **Gateway**: Next hop router
- **Interface**: Network interface to use
- **Metric**: Cost/distance (lower is preferred)

### Static vs Dynamic Routing

#### Static Routing
- **Manual configuration** of routes
- **Pros**: Simple, secure, predictable
- **Cons**: Not scalable, manual updates required, no failover
- **Use Case**: Small networks, specific routes

**Example**:
```
Route add 10.0.0.0 mask 255.255.255.0 192.168.1.1
```

#### Dynamic Routing
- **Automatic discovery** and configuration of routes
- **Protocols**: RIP, OSPF, BGP, EIGRP
- **Pros**: Scalable, automatic failover, adapts to changes
- **Cons**: More complex, CPU/bandwidth overhead

### Common Routing Protocols

#### RIP (Routing Information Protocol)
- **Type**: Distance-vector routing
- **Max Hops**: 15
- **Metric**: Hop count
- **Update Interval**: 30 seconds
- **Limitation**: Slow convergence, inefficient for large networks
- **Status**: Largely deprecated

#### OSPF (Open Shortest Path First)
- **Type**: Link-state routing
- **Metric**: Cost (bandwidth-based)
- **Convergence**: Fast
- **Scalability**: Good for large networks
- **Features**: Supports VLSM, load balancing
- **Status**: Widely used in enterprise networks

#### BGP (Border Gateway Protocol)
- **Type**: Path-vector routing
- **Scope**: Inter-autonomous system routing (Internet backbone)
- **Metric**: AS path, various policies
- **Scalability**: Internet-scale
- **Use**: ISPs, large enterprises

#### EIGRP (Enhanced Interior Gateway Routing Protocol)
- **Type**: Hybrid (distance-vector + link-state)
- **Metric**: Bandwidth, delay, reliability, load
- **Convergence**: Very fast
- **Scalability**: Enterprise networks
- **Proprietary**: Cisco (though now more open)

### Routing Decision Process

```
1. Packet arrives at router
2. Router extracts destination IP
3. Router looks up destination in routing table
4. Router matches longest prefix match (LPM)
5. Router forwards packet to next hop via specified interface
6. Process repeats at each router until destination reached
```

---

## Key Networking Concepts

### Bandwidth vs Latency

#### Bandwidth
- **Definition**: Maximum data transfer rate (capacity)
- **Unit**: Bits per second (bps), Mbps, Gbps
- **Analogy**: Width of a highway
- **Cybersecurity**: Relevant to DDoS attack magnitude

#### Latency
- **Definition**: Time delay for data to travel from source to destination
- **Unit**: Milliseconds (ms)
- **Analogy**: Distance between cities
- **Cybersecurity**: Affects real-time threat detection capabilities

### Throughput
- **Definition**: Actual data transfer rate in practice
- **Usually less than bandwidth** due to overhead, congestion
- **Measurement**: Bits per second (bps)
- **Formula**: Throughput ≤ Bandwidth - Overhead

### Packet Loss
- **Definition**: Percentage of packets that fail to reach destination
- **Causes**: Network congestion, errors, hardware failure
- **Impact**: Data corruption, retransmission overhead
- **Acceptable Rate**: < 1% for most applications

### Jitter
- **Definition**: Variation in packet arrival time
- **Impact**: VoIP quality, video streaming
- **Measurement**: Milliseconds

### MTU (Maximum Transmission Unit)
- **Definition**: Largest packet size that can be transmitted without fragmentation
- **Default Ethernet**: 1500 bytes
- **Jumbo Frames**: Up to 9000 bytes
- **Cybersecurity**: Path MTU discovery can be exploited

### DNS Resolution Process

```
1. User enters URL in browser
2. Browser queries local DNS cache
3. If not cached, queries recursive resolver
4. Resolver queries root nameserver
5. Root directs to TLD (Top Level Domain) nameserver
6. TLD directs to authoritative nameserver
7. Authoritative nameserver returns IP address
8. IP address returned to browser
9. Browser connects to web server using IP
```

### ARP (Address Resolution Protocol)

**Purpose**: Maps IP addresses to MAC addresses on local network

**Process**:
```
1. Device A wants to communicate with Device B
2. Device A knows B's IP (192.168.1.5) but not MAC
3. Device A sends ARP request broadcast: "Who has 192.168.1.5?"
4. Device B responds with its MAC address
5. Device A caches MAC address in ARP table
6. Communication proceeds at Data Link layer using MAC addresses
```

**Cybersecurity Threat**: ARP spoofing (attacker sends fake ARP responses)

### MAC Addresses

**Format**: 48 bits, written as 6 pairs of hexadecimal digits
**Example**: `00:1A:2B:3C:4D:5E`

**Structure**:
- **First 3 pairs (24 bits)**: OUI (Organizationally Unique Identifier) - manufacturer
- **Last 3 pairs (24 bits)**: Device-specific identifier

**Special MAC Addresses**:
- `FF:FF:FF:FF:FF:FF` - Broadcast address (all devices on local network)
- `00:00:00:00:00:00` - Not used

### Network Segmentation / VLAN

**Purpose**: Divide network into isolated segments for security and management

**VLAN (Virtual LAN)**:
- Logically separates networks on same physical switch
- Tagged with VLAN ID (1-4094)
- Different VLANs can't communicate without a router

**Benefits**:
- Reduced broadcast domain
- Improved security (isolate sensitive devices)
- Easier management
- Flexible network design

**Example**: 
- VLAN 10: Finance department
- VLAN 20: HR department
- Routers between VLANs can enforce security policies

### NAT (Network Address Translation)

**Purpose**: Maps private IP addresses to public IP addresses

**Types**:
- **Static NAT**: One-to-one permanent mapping
- **Dynamic NAT**: One-to-many temporary mapping
- **PAT (Port Address Translation)**: Many private IPs to one public IP

**How it Works**:
```
Private Network: 192.168.1.0/24
Public Address: 203.0.113.5

Device 192.168.1.100:5000 → Router → Public 203.0.113.5:5000
Device 192.168.1.101:5000 → Router → Public 203.0.113.5:5001
```

**Cybersecurity Implications**:
- Hides internal network structure
- Makes port scanning harder
- Can complicate logging and forensics

### Proxy vs VPN

#### Proxy
- Operates at Application layer
- Replaces client IP with proxy IP
- Faster, application-specific
- Limited encryption options
- Only for configured applications

#### VPN (Virtual Private Network)
- Operates at Network/Transport layer
- Encrypts entire connection
- Slower but more comprehensive
- Encrypts all traffic
- System-wide protection

### Quality of Service (QoS)

**Purpose**: Prioritizes certain types of traffic

**Methods**:
- **Traffic shaping**: Rate limiting
- **Priority queuing**: Prioritizes certain traffic
- **Weighted fair queuing**: Allocates bandwidth proportionally

**Use Cases**:
- VoIP prioritization over web browsing
- Video streaming optimization
- DDoS mitigation

---

## Cybersecurity Applications of Networking Knowledge

### Reconnaissance
- **Port scanning**: Identifying open ports and services
- **Network mapping**: Understanding network topology
- **DNS queries**: Gathering information about target
- **WHOIS lookups**: Finding owner information

### Vulnerability Discovery
- **Protocol analysis**: Finding protocol weaknesses
- **Configuration review**: Identifying misconfigurations
- **Packet sniffing**: Identifying unencrypted data transmission
- **Man-in-the-Middle (MITM)**: Intercepting communications

### Attack Detection
- **Anomalous traffic patterns**: Detecting data exfiltration
- **Port scanning detection**: Identifying reconnaissance activity
- **Protocol violations**: Detecting exploit attempts
- **Bandwidth anomalies**: Detecting DDoS attacks

### Defense Implementation
- **Firewall rules**: Blocking malicious traffic
- **IDS/IPS deployment**: Detecting and preventing attacks
- **Network segmentation**: Limiting attack spread
- **VPN deployment**: Protecting remote access
- **DNS filtering**: Blocking malicious domains

---

## Summary

Understanding networking is fundamental to cybersecurity. Key takeaways:

1. **OSI Model**: 7 layers describing network communication
2. **TCP/IP**: Practical internet protocol suite
3. **Protocols**: HTTP, HTTPS, DNS, SSH, etc., each with security implications
4. **IP Addressing**: IPv4 (4.3 billion addresses) and IPv6 (practically unlimited)
5. **Subnetting**: Dividing networks for security and efficiency
6. **Network Devices**: Routers, switches, firewalls, IDS/IPS
7. **Routing**: Static and dynamic routing protocols
8. **Security Applications**: From reconnaissance to defense implementation

This foundation will prepare you for advanced networking topics, network security, and penetration testing in subsequent modules.

---

## Key Terms to Remember

- **Broadcast**: Message sent to all devices on network
- **Unicast**: Message sent to single device
- **Multicast**: Message sent to group of devices
- **Collision Domain**: Network segment where collisions can occur
- **Broadcast Domain**: Network segment receiving broadcasts
- **Default Gateway**: Router through which packets leave local network
- **DNS Server**: Translates domain names to IP addresses
- **DHCP Server**: Assigns IP addresses dynamically
- **Port**: Logical channel number (0-65535) for specific service
- **Socket**: Combination of IP address and port
- **Handshake**: Negotiation process to establish connection

---

## Review Questions

1. Describe the 7 layers of the OSI model and provide an example protocol for each layer.
2. What are the main differences between TCP and UDP?
3. Explain the concept of subnetting and provide a real-world example.
4. What is the difference between static and dynamic routing?
5. How does ARP work, and what security threats does it face?
6. Compare and contrast the OSI model and TCP/IP model.
7. What is NAT and how does it affect network security?
8. Explain VLAN segmentation and its security benefits.
9. What protocols should you avoid using in production environments and why?
10. How would you design a secure network architecture using networking concepts learned?

---

## References and Further Reading

- RFC 791: Internet Protocol (IPv4)
- RFC 2460: Internet Protocol, Version 6 (IPv6)
- RFC 793: Transmission Control Protocol (TCP)
- RFC 768: User Datagram Protocol (UDP)
- CCNA Routing and Switching: Study Guide
- CompTIA Network+ Certification Study Guide
- Networking fundamentals on Cisco Learning Network
