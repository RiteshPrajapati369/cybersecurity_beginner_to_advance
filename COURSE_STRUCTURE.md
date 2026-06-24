# Cybersecurity Beginner to Advanced - Full Course Structure (Complete Edition)

## Course Overview
Zero to job-ready professional. No gaps continous learning for 1-1.5 years. Covers Offensive (Red Team), Defensive (Blue Team), Management, and Career.

---

## PHASE 1: ABSOLUTE FOUNDATIONS (Modules 1-9)

## Module 1: Introduction to Cybersecurity
- [ ] What is Cybersecurity? (CIA Triad, AAA, Non-repudiation)
- [ ] Types of Hackers (White, Black, Grey, Red, Blue, Purple, Green, Script Kiddie)
- [ ] Threat Actors (APT, Hacktivists, Cyber Criminals, Insiders, Nation-States)
- [ ] Cyber Kill Chain (7 stages explained)
- [ ] MITRE ATT&CK Framework (tactics, techniques, procedures)
- [ ] Attack Vectors and Attack Surfaces
- [ ] Real-world breach case studies (Target, Equifax, SolarWinds, Colonial Pipeline)
- [ ] Hands-on: Map a real attack to MITRE ATT&CK

## Module 2: Cyber Laws, Ethics & Compliance
- [ ] Global Cyber Laws (Budapest Convention, Council of Europe)
- [ ] Regional Laws (GDPR - Europe, CFAA - USA, IT Act - India, Cybersecurity Law - China)
- [ ] Industry Compliance (HIPAA - Healthcare, PCI-DSS - Payments, SOX - Finance, FISMA - Government)
- [ ] Common Cyber Crimes and Punishments (by country comparison)
- [ ] Bug Bounty Programs (legal hunting, responsible disclosure)
- [ ] NIST Cybersecurity Framework, ISO 27001
- [ ] Ethics in Hacking (Do no harm, permission boundaries)
- [ ] Hands-on: Write a responsible disclosure policy template

## Module 3: Networking Deep Dive (Part 1)
- [ ] OSI Model (7 layers with real protocols, devices at each layer)
- [ ] TCP/IP Suite (comparison with OSI)
- [ ] TCP vs UDP (differences, use cases, headers)
- [ ] TCP 3-way Handshake and 4-way Termination
- [ ] IPv4 vs IPv6 (address formats, transition mechanisms)
- [ ] Subnetting (CIDR, VLSM, FLSM, calculations)
- [ ] MAC Addresses, ARP (how ARP works, ARP table)
- [ ] Hands-on: Analyze handshake and ARP with Wireshark

## Module 4: Networking Deep Dive (Part 2)
- [ ] DNS (hierarchy, resolution process, record types: A, AAAA, CNAME, MX, TXT, NS)
- [ ] HTTP/HTTPS (methods, status codes, headers, versions 1.1/2/3)
- [ ] FTP, SFTP, FTPS (active vs passive)
- [ ] SSH (authentication, tunneling, port forwarding)
- [ ] SMTP, POP3, IMAP (email protocols)
- [ ] DHCP (DORA process)
- [ ] SNMP (community strings, versions)
- [ ] Hands-on: Configure and capture all major protocols

## Module 5: Cryptography & PKI (Public Key Infrastructure)
- [ ] Symmetric Encryption (AES, DES, 3DES, ChaCha20, RC4)
- [ ] Asymmetric Encryption (RSA, ECC, Diffie-Hellman)
- [ ] Hash Functions (MD5, SHA1, SHA256, SHA3, bcrypt, scrypt, Argon2)
- [ ] Digital Signatures (signing, verification)
- [ ] Digital Certificates (X.509, CA, intermediate, root)
- [ ] PKI (Certificate Authority, Registration Authority, Validation Authority)
- [ ] SSL/TLS (handshake, cipher suites, perfect forward secrecy)
- [ ] Hands-on: Decrypt hashes (CrackStation, Hashcat), inspect SSL cert

## Module 6: Windows Security & Administration
- [ ] Windows Architecture (kernel, user mode, processes, threads, handles)
- [ ] Windows Registry (hives, keys, values - forensic importance)
- [ ] Windows Services (privileges, auto-start)
- [ ] CMD and PowerShell for Security (Get-Process, Get-Service, Get-EventLog, AuditPol, WMIC)
- [ ] Windows Permissions (NTFS, Share, Effective, Inheritance, Owner)
- [ ] Windows Security Features (UAC, Windows Defender, Firewall, BitLocker, AppLocker)
- [ ] Windows Logging (Security, System, Application, Setup, PowerShell, Sysmon)
- [ ] Active Directory (Domain, Forest, OU, DC, LDAP, Kerberos, Group Policy)
- [ ] Hands-on: Audit Windows security, extract event logs

## Module 7: Linux Security & Administration
- [ ] Linux Architecture (kernel, shell, filesystem, FHS)
- [ ] Essential Commands for Security (find, grep, awk, sed, cut, sort, uniq, wc, xargs)
- [ ] File Permissions (rwx, SUID, SGID, Sticky bit, ACLs, umask)
- [ ] Process Management (ps, top, htop, kill, nice, renice, nohup, systemd, journalctl)
- [ ] Linux Services (systemctl, init.d, listing, securing)
- [ ] Linux Logs (/var/log: auth, syslog, kern, dmesg, btmp, wtmp, secure, httpd, audit)
- [ ] Linux Security (SELinux, AppArmor, iptables/nftables, fail2ban)
- [ ] Wine (running Windows tools on Linux)
- [ ] Hands-on: Harden Linux (SSH config, disable root, set firewall)

## Module 8: Virtualization & Home Lab Setup
- [ ] What is Virtualization? (why every cybersecurity pro needs a lab)
- [ ] Minimum Hardware Requirements (RAM, storage, CPU, BIOS settings)
- [ ] Installing VirtualBox (step by step)
- [ ] Downloading Kali Linux (pre-built OVA — not ISO)
- [ ] Importing Kali Linux into VirtualBox
- [ ] First Boot & Update (change default password, apt update)
- [ ] Downloading Metasploitable 2 (vulnerable target)
- [ ] Importing Metasploitable 2 (create VM with existing disk)
- [ ] Host-Only Networking (isolate VMs from internet)
- [ ] Verify Connectivity (ping, simple Nmap scan)
- [ ] Snapshots (save and restore clean state)
- [ ] Hands-on: Build working lab with 2 VMs + Host-Only network

## Module 9: Linux Basic Commands
- [ ] Linux File System Navigation (pwd, ls, cd, tree)
- [ ] File Management Commands (touch, mkdir, cp, mv, rm, find)
- [ ] Viewing and Editing Files (cat, less, head, tail, nano, vim)
- [ ] File Permissions and Ownership (chmod, chown, SUID, SGID, Sticky Bit)
- [ ] Process Management (ps, top, htop, kill, jobs, fg, bg)
- [ ] Network Commands (ip, ifconfig, ping, ss, netstat, nslookup, dig, curl, wget)
- [ ] User and Group Management (whoami, id, sudo, useradd, passwd)
- [ ] System Information Commands (uname, df, du, free, history)
- [ ] Searching and Filtering (grep, awk, sed, pipes, redirections)
- [ ] Compression and Archiving (tar, zip, unzip)
- [ ] Package Management (apt, dpkg)
- [ ] Hands-on: Linux Command Practice


## PHASE 2: RECONNAISSANCE & OSINT (Modules 10-13)

## Module 10: OSINT (Open Source Intelligence) - Part 1
- [ ] OSINT Methodology (passive vs active, legal boundaries)
- [ ] OSINT Framework (overview, tool categories)
- [ ] Google Dorking (intitle:, inurl:, filetype:, site:, cache:, related:, info:, allinurl:, allintitle:)
- [ ] Google Hacking Database (GHDB) - 100+ dorks
- [ ] Custom search engines (Bing, Yahoo, Yandex, Baidu, DuckDuckGo)
- [ ] Reverse Image Search (Google Images, TinEye, Yandex)
- [ ] Metadata Analysis (ExifTool, FOCA, Metagoofil)
- [ ] Hands-on: Find exposed documents, login pages, sensitive files

## Module 11: OSINT (Open Source Intelligence) - Part 2
- [ ] People Intelligence (Sherlock, Maigret, WhatsMyName, Holehe)
- [ ] Social Media OSINT (Twitter: snscrape; LinkedIn, Instagram, Facebook, TikTok, YouTube)
- [ ] Email OSINT (Hunter.io, EmailRep, HaveIBeenPwned, DeHashed, IntelX)
- [ ] Username OSINT (WhatsMyName, Namechk, KnowEm)
- [ ] Phone OSINT (PhoneInfoga, carrier lookup, SpyDialer)
- [ ] Domain OSINT (whois, nslookup, dig, SecurityTrails, DNSDumpster)
- [ ] Certificate Transparency Logs (crt.sh - find subdomains via SSL certificates)
- [ ] Code OSINT (GitHub dorking, search code, commits)
- [ ] Hands-on: Full OSINT investigation on a target (ethical authorization)

## Module 12: Advanced OSINT Tools & Automation
- [ ] Shodan (Internet-connected device search, banner grabbing, vulnerability discovery)
- [ ] Maltego (Graph-based link analysis, visualizing relationships between entities)
- [ ] SpiderFoot (Automated OSINT correlation, queries 200+ data sources)
- [ ] Recon-ng (Modular OSINT framework, automation, workspaces)
- [ ] TheHarvester (Email and subdomain harvesting from Google, LinkedIn, Bing)
- [ ] Censys (Internet-wide asset discovery, SSL certificate analysis)
- [ ] BuiltWith (Website technology profiling: CMS, frameworks, hosting)
- [ ] FOCA (Metadata extraction from public documents)
- [ ] Hands-on: Use Shodan, Maltego, and SpiderFoot to map an organization's digital footprint

## Module 13: Anonymity, Privacy & Operational Security
- [ ] Surface Web vs Deep Web vs Dark Web (comparison, sizes, content)
- [ ] TOR Browser (setup, configuration, hidden services, .onion sites)
- [ ] Dark Web Search Engines (Ahmia, Torch, Haystak, Dark.fail, Not Evil)
- [ ] I2P (Invisible Internet Project)
- [ ] Freenet (decentralized)
- [ ] OnionShare (secure file sharing)
- [ ] Tails OS (amnesiac operating system)
- [ ] VPNs vs Proxies vs TOR vs I2P (comparison, use cases, limitations)
- [ ] OpSec best practices (OPSEC, compartmentalization, minimizing footprint)
- [ ] Hands-on: Browse Dark Web safely, share a file via OnionShare
---

## PHASE 3: SCANNING & ENUMERATION (Modules 14-17)

## Module 14: Network Scanning with Nmap
- [ ] Nmap Fundamentals (installation, syntax, help)
- [ ] Host Discovery (-sn, -Pn, -PS, -PA, -PU, -PY)
- [ ] Port Scanning Techniques (-sS, -sT, -sU, -sF, -sN, -sX, -sA, -sW, -sM, -sZ)
- [ ] Service and Version Detection (-sV, intensity levels, --version-light, --version-all)
- [ ] OS Fingerprinting (-O, --osscan-guess, --osscan-limit)
- [ ] Nmap Scripting Engine (NSE) - categories: discovery, vuln, exploit, auth, brute, default
- [ ] Timing and Performance (-T0 to -T5, --min-rate, --max-rate)
- [ ] Output Formats (-oN, -oX, -oG, -oA)
- [ ] Masscan (ultra-fast scanning)
- [ ] Zenmap (GUI for Nmap)
- [ ] Hands-on: Full network enumeration, use NSE vuln scripts

## Module 15: Packet Analysis with Wireshark
- [ ] Wireshark Fundamentals (capturing, interfaces, promiscuous vs monitor mode)
- [ ] Capture Filters (syntax: host, net, port, src, dst, protocol)
- [ ] Display Filters (syntax: ip.addr, tcp.port, http.request, tcp.flags.syn)
- [ ] Colorization Rules (customize for anomaly detection)
- [ ] Following TCP/UDP/HTTP Streams
- [ ] Protocol Analysis (HTTP, DNS, ARP, TCP, UDP, ICMP, DHCP, SMB, FTP)
- [ ] Statistics and Endpoints
- [ ] tcpdump (command-line capture)
- [ ] tshark (command-line analysis)
- [ ] Hands-on: Detect port scans, ARP spoofing, suspicious traffic

## Module 16: Vulnerability Scanning
- [ ] Vulnerability Assessment Methodology (pre-scan, scan, post-scan, reporting)
- [ ] OpenVAS (setup, configuration, scanning, reading results)
- [ ] Nessus Essentials (free version, policy creation, scan templates)
- [ ] Qualys (free community edition)
- [ ] CVSS (Common Vulnerability Scoring System) - v3.0, v4.0
- [ ] CVE (Common Vulnerabilities and Exposures) - understanding CVE IDs
- [ ] False Positives vs False Negatives (identification, verification)
- [ ] Authenticated vs Unauthenticated Scanning
- [ ] Vulnerability Prioritization (severity, exploitability, asset criticality)
- [ ] Hands-on: Scan Metasploitable, produce vulnerability report

## Module 17: Steganography
- [ ] Steganography vs Cryptography vs Watermarking
- [ ] Image Steganography (LSB, DCT, palette-based, Steghide, OpenStego, StegSolve)
- [ ] Audio Steganography (LSB, phase coding, echo hiding, DeepSound)
- [ ] Video Steganography
- [ ] Document Steganography (whitespace, font changes, metadata)
- [ ] Detecting Steganography (Stegdetect, Stegexpose, binwalk, foremost)
- [ ] Steganalysis (visual, statistical, structural)
- [ ] Hands-on: Hide and extract data from images, detect hidden data

---

## PHASE 4: WEB APPLICATION SECURITY (Modules 18-21)

## Module 18: Web Fundamentals & OWASP Top 10
- [ ] HTTP Protocol Deep Dive (methods: GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD)
- [ ] HTTP Headers (request: Host, User-Agent, Cookie, Referer; response: Set-Cookie, Location, Server)
- [ ] HTTP Status Codes (1xx, 2xx, 3xx, 4xx, 5xx - memorize critical ones)
- [ ] Cookies (session cookies, secure flag, HttpOnly, SameSite)
- [ ] Sessions and Tokens (JWT, OAuth, SAML)
- [ ] Same-Origin Policy (SOP) and CORS (Cross-Origin Resource Sharing)
- [ ] OWASP Top 10 2021 (full breakdown)
- [ ] OWASP Top 10 2024 (new changes preview)
- [ ] Hands-on: Analyze HTTP traffic, identify security headers

## Module 19: Web Proxy Tools (Burp Suite & OWASP ZAP)
- [ ] Burp Suite Community Edition (setup, proxy, certificate)
- [ ] Burp Suite Features (Target, Proxy, Intruder, Repeater, Decoder, Comparer, Sequencer, Scanner)
- [ ] Burp Extensions (BApp Store: Turbo Intruder, Logger++, Active Scan++, Autorize)
- [ ] OWASP ZAP (setup, automated scanning, manual testing, HUD, API)
- [ ] ZAP Features (Spider, Active Scan, Passive Scan, Fuzzer, Breakpoints)
- [ ] Comparing Burp vs ZAP (when to use which)
- [ ] Hands-on: Intercept and modify requests, brute-force login with Intruder

## Module 20: Web Vulnerabilities - Part 1 (Injection & XSS)
- [ ] SQL Injection (Union, Boolean Blind, Time-based, Error-based, Out-of-band)
- [ ] NoSQL Injection (MongoDB, CouchDB)
- [ ] Command Injection (OS, application)
- [ ] LDAP Injection
- [ ] XPath Injection
- [ ] SQLmap (automated exploitation: --level, --risk, --os-shell, --tamper)
- [ ] Cross-Site Scripting (Reflected, Stored, DOM-based, Blind)
- [ ] XSS BeEF Framework (browser exploitation)
- [ ] Hands-on: Exploit SQL injection, steal cookies with XSS

## Module 21: Web Vulnerabilities - Part 2 (Other Critical Vulnerabilities)
- [ ] CSRF (Cross-Site Request Forgery) - how it works, anti-CSRF tokens
- [ ] IDOR (Insecure Direct Object Reference) - parameter tampering
- [ ] LFI/RFI (Local/Remote File Inclusion) - wrappers: php://filter, php://input, expect://
- [ ] SSRF (Server-Side Request Forgery) - internal network scanning
- [ ] XXE (XML External Entity) - file read, SSRF
- [ ] File Upload Vulnerabilities (bypass techniques: double extension, MIME, magic bytes)
- [ ] Path Traversal/Directory Traversal (../ bypass techniques)
- [ ] Race Conditions
- [ ] SSTI (Server-Side Template Injection) - Jinja2, Twig, Freemarker
- [ ] Hands-on: Exploit all OWASP Top 10 on DVWA, Juiceshop, PortSwigger labs

---

## PHASE 5: NETWORK ATTACKS (Modules 22-24)

## Module 22: Denial of Service (DoS) & Distributed Denial of Service (DDoS)
- [ ] DoS vs DDoS (differences, scale, impact)
- [ ] Types of DoS/DDoS Attacks:
    - Volume-based (UDP flood, ICMP flood, amplification)
    - Protocol-based (SYN flood, Ping of Death, Smurf, Fragmentation)
    - Application Layer (HTTP flood, Slowloris, RUDY, DNS query flood)
    - Amplification Attacks (DNS, NTP, Memcached, CLDAP, SSDP)
- [ ] Botnets (Mirai, Emotet, Necurs) - IoT botnets
- [ ] DDoS Attack Tools (LOIC, HOIC, Slowloris, HULK, GoldenEye)
- [ ] DDoS Mitigation:
    - On-premise (scrubbing centers, rate limiting)
    - Cloud-based (Cloudflare, AWS Shield, Akamai, Imperva, Arbor Networks)
    - Best practices (anycast, blackholing, rate limiting, WAF)
- [ ] Real-world DDoS Attacks (Dyn 2016, GitHub 2018, AWS 2020)
- [ ] Hands-on: Simulate Slowloris attack (lab only), analyze DDoS in Wireshark

## Module 23: Man-in-the-Middle (MITM) & Network Eavesdropping
- [ ] MITM Attack Overview (how it works, where it happens)
- [ ] ARP Spoofing/Poisoning (gratuitous ARP, ARP cache poisoning)
- [ ] DNS Spoofing (dnsspoof, Ettercap, BetterCAP)
- [ ] DHCP Spoofing/Rogue DHCP Server
- [ ] LLMNR/NBT-NS Poisoning (Responder)
- [ ] ICMP Redirect Attacks
- [ ] SSL/TLS Stripping (HSTS bypass)
- [ ] Session Hijacking (cookie stealing, session fixation)
- [ ] Sniffing Tools (Ettercap, BetterCAP, dsniff, Cain & Abel)
- [ ] Defenses (ARP spoofing detection, DNSSEC, HTTPS, TLS, MACsec, 802.1X)
- [ ] Hands-on: ARP spoofing, capture traffic, DNS spoof a domain

## Module 24: DNS Attacks & Abuse
- [ ] How DNS Works (recursive query, iterative query, caching)
- [ ] DNS Attack Types:
    - DNS Spoofing/Cache Poisoning
    - DNS Tunneling (exfiltrating data, C2 communication)
    - DNS Amplification (DDoS attack)
    - DNS Hijacking (router compromise, registrar hijack)
    - Phantom Domain Attack
    - DNS Rebinding (bypassing CORS/SOP)
    - NXDOMAIN Attack
    - Random Subdomain Attack
- [ ] DNS Security (DNSSEC, DNS over TLS, DNS over HTTPS)
- [ ] Tools (dnschef, dnscat2, Iodine)
- [ ] Hands-on: DNS tunneling simulation, analyze DNS attacks

---

## PHASE 6: PASSWORD ATTACKS (Modules 25-26)

## Module 25: Online Password Attacks
- [ ] Brute-Force vs Dictionary vs Hybrid Attack (differences, when to use)
- [ ] Wordlists (RockYou, SecLists, CrackStation, Weakpass, Custom generation)
- [ ] Crunch (wordlist generator)
- [ ] CeWL (Custom wordlist from website)
- [ ] Hydra (SSH, FTP, HTTP-GET, HTTP-POST, RDP, MySQL, SMTP, SMB, Telnet)
- [ ] Medusa (parallel brute-forcing, modular)
- [ ] Ncrack (high-performance, supports Cisco, RDP, SSH, HTTP)
- [ ] Patator (modular, Python-based)
- [ ] Rate Limiting Bypass (delays, proxy rotation, IP rotation)
- [ ] Password Spraying (vertical vs horizontal)
- [ ] Hands-on: Brute-force SSH, HTTP login form with Hydra

## Module 26: Offline Password Cracking
- [ ] Hash Types (LM, NTLM, NetNTLMv1/v2, MD5, SHA1, SHA256, bcrypt, scrypt, Argon2, MySQL, PostgreSQL)
- [ ] Hash Identification (hashid, hash-identifier, online tools)
- [ ] Hash Extraction (Windows SAM, /etc/shadow, memory dumps)
- [ ] John the Ripper (unshadow, single crack, wordlist, incremental, rules, external)
- [ ] John Modes (wordlist, single, incremental, markov, external)
- [ ] Hashcat (dictionary, brute-force, mask, combinator, hybrid, rule-based)
- [ ] Hashcat Attack Types (0=MD5, 1000=NTLM, 1800=bcrypt, 3200=bcrypt, 5500=NetNTLMv2)
- [ ] GPU Acceleration (CPU vs GPU speed comparison)
- [ ] Rainbow Tables (precomputed hashes, Ophcrack, CrackStation tables)
- [ ] Salting and Peppering (cracking salted hashes, hashcat --show)
- [ ] Rules in Hashcat/John (best64, OneRuleToRuleThemAll, dive.rule)
- [ ] Hands-on: Crack /etc/shadow, NTLM, NetNTLMv2, bcrypt

---

## PHASE 7: EXPLOITATION (Modules 27-31)

## Module 27: Metasploit Framework (Full)
- [ ] Metasploit Architecture (msfconsole, modules: exploits, payloads, auxiliary, encoders, nops, post)
- [ ] Metasploit Terminology (vulnerability, exploit, payload, shellcode, listener, session)
- [ ] Information Gathering with Auxiliary Modules (port scanners, service enumerators)
- [ ] Exploit Selection and Configuration (show options, set RHOSTS, set LHOST, check)
- [ ] Payload Types (reverse shell, bind shell, meterpreter, stageless, staged)
- [ ] Meterpreter Commands (sysinfo, getuid, hashdump, screenshot, keyscan, shell, upload, download)
- [ ] Post-Exploitation Modules (enumeration, persistence, privilege escalation, lateral movement)
- [ ] MSFvenom (payload generation: windows/meterpreter/reverse_tcp, linux/x86/shell_reverse_tcp)
- [ ] Encoding and Evasion (shikata_ga_nai, custom encoders, packing)
- [ ] Database Integration (workspaces, hosts, services, notes, creds)
- [ ] Resource Scripts (automation with .rc files)
- [ ] Hands-on: Exploit EternalBlue (MS17-010), get Meterpreter session

## Module 28: Buffer Overflow & Binary Exploitation
- [ ] Memory Layout (text, data, bss, heap, stack, registers: EIP, ESP, EBP, EAX, EBX)
- [ ] What is Buffer Overflow (stack-based, heap-based)
- [ ] Stack Buffer Overflow (overwrite EIP, JMP ESP, shellcode execution)
- [ ] Finding Offset (pattern_create, pattern_offset)
- [ ] Bad Character Detection (identifying badchars for shellcode)
- [ ] Generating Shellcode (msfvenom, custom)
- [ ] NOP Sled (snow technique)
- [ ] Return-to-libc (bypass NX)
- [ ] Protections and Bypasses:
    - NX (Non-eXecutable stack) → ROP (Return Oriented Programming)
    - ASLR (Address Space Layout Randomization) → information leak, brute force
    - Stack Canaries → overflow without overwriting canary
- [ ] Hands-on: Classic buffer overflow (vulnserver, OSCP style) - Windows

## Module 29: Shells, Payloads & Reverse Engineering
- [ ] Types of Shells (reverse, bind, web, encrypted, IPv6)
- [ ] Netcat Shells (nc -e /bin/bash, nc -lvp 4444)
- [ ] Socat (advanced nc alternative)
- [ ] PowerCat (PowerShell Netcat)
- [ ] Web Shells (PHP, ASPX, JSP)
- [ ] Staged vs Stageless Payloads
- [ ] Shell Upgrading (python -c 'import pty; pty.spawn("/bin/bash")', stty raw -echo)
- [ ] SMB/HTTP Payload Delivery
- [ ] Hands-on: Create reverse shell, upgrade to full TTY

## Module 30: Evasion Techniques & Anti-Virus Bypass
- [ ] Why AV Evasion Matters (signature-based, heuristic, behavioral)
- [ ] AV Detection Methods (static analysis, dynamic analysis, emulation, cloud lookups)
- [ ] Packers and Crypters (UPX, Hyperion, Themida)
- [ ] Code Obfuscation (variable renaming, junk code, string encryption)
- [ ] Payload Encoding (base64, XOR, AES)
- [ ] Polymorphic vs Metamorphic Code
- [ ] Custom Payloads (avoiding known signatures)
- [ ] Bypassing Windows Defender (AMSI bypass, disabling)
- [ ] Bypassing EDR (hook unhooking, direct syscalls, PPID spoofing)
- [ ] Tools (Shellter, Veil-Evasion, PE-Crypter, ScareCrow)
- [ ] Hands-on: Bypass Windows Defender with custom payload

## Module 31: Social Engineering & Phishing
- [ ] Social Engineering Principles (Cialdini's 6: authority, scarcity, liking, reciprocity, social proof, commitment)
- [ ] Types (Phishing, Vishing, Smishing, Spear Phishing, Whaling, Pretexting, Baiting, Tailgating, Quid Pro Quo)
- [ ] SEToolkit (website cloning, credential harvesting, spear-phishing payload generation)
- [ ] Gophish (phishing campaign management, email templates, landing pages, reporting)
- [ ] Evilginx2 (2FA bypass, reverse proxy)
- [ ] Modlishka (reverse proxy phishing)
- [ ] Creating Convincing Emails (spoofing SPF/DKIM/DMARC basics)
- [ ] Phishing Frameworks Comparison
- [ ] Hands-on: Run complete phishing simulation in lab

---

## PHASE 8: POST-EXPLOITATION (Modules 32-34)

## Module 32: Privilege Escalation (Linux)
- [ ] Why Privilege Escalation Matters (user → root)
- [ ] Enumeration Methodology (automated vs manual)
- [ ] Kernel Exploits (Dirty Cow, PwnKit, Overlayfs)
- [ ] SUID Binaries (GTFOBins, exploit misconfigured SUID)
- [ ] Sudo (sudo -l, CVE-2021-3156, env_keep, LD_PRELOAD)
- [ ] Cron Jobs (writable scripts, wildcards, PATH hijacking)
- [ ] Capabilities (Linux capabilities, exploit misconfigured caps)
- [ ] writable /etc/passwd (adding root user)
- [ ] NFS (no_root_squash, root_squash bypass)
- [ ] Docker (in docker group, mounting host filesystem)
- [ ] Tools (LinPEAS, LinEnum, Linux Smart Enumeration, PSPY)
- [ ] Hands-on: Escalate www-data → root using 3 different methods

## Module 33: Privilege Escalation (Windows)
- [ ] Windows Privilege Escalation (user → SYSTEM)
- [ ] Enumeration (systeminfo, whoami /all, net users, net localgroup, schtasks, wmic, reg query)
- [ ] Kernel Exploits (MS16-032, MS17-010, CVE-2019-1388, PrintNightmare)
- [ ] Unquoted Service Paths (exploitation method)
- [ ] Weak Service Permissions (Service Control Manager, sc config)
- [ ] DLL Hijacking (PATH, missing DLL, planting)
- [ ] AlwaysInstallElevated (MSI installers as SYSTEM)
- [ ] Scheduled Tasks (writable scripts, running as SYSTEM)
- [ ] Windows Credentials (saved passwords, Credential Manager, mimikatz)
- [ ] Potato家族 (Rotten, Juicy, Sweet, Ghost) - SeImpersonate privilege
- [ ] Tools (WinPEAS, PowerUp, BeRoot, Seatbelt, PrivescCheck)
- [ ] Hands-on: Escalate NT AUTHORITY\SYSTEM via 3 methods

## Module 34: Lateral Movement, Pivoting & Persistence
- [ ] Lateral Movement (moving across network)
- [ ] Techniques (Pass-the-Hash, Pass-the-Ticket, WMI, PsExec, WinRM, SMBExec, Schtasks, RDP)
- [ ] WMI for Lateral Movement (wmic process call create)
- [ ] PsExec (Sysinternals, Impacket PsExec)
- [ ] WinRM (Evil-WinRM, PowerShell)
- [ ] Pass-the-Hash (mimikatz sekurlsa::pth)
- [ ] Pass-the-Ticket (Kerberos tickets, mimikatz)
- [ ] Overpass-the-Hash (NTLM hash to Kerberos)
- [ ] Pivoting (SSH tunneling, port forwarding, chisel, ligolo-ng, Metasploit pivoting)
- [ ] ProxyChains (routing through pivot)
- [ ] Persistence Mechanisms:
    - Linux (cron jobs, .bashrc, SSH keys, systemd services, backdoored binaries)
    - Windows (registry run keys, scheduled tasks, WMI event subscription, startup folder, services, DLL persistence)
- [ ] Hands-on: Pivot from one machine to another, establish persistence

---

## PHASE 9: ADVANCED OFFENSIVE (Modules 35-38)

## Module 35: Active Directory Hacking (Full)
- [ ] AD Architecture (Domains, Trees, Forests, OUs, Trusts, Domain Controllers, Sites)
- [ ] AD Authentication (Kerberos, NTLM, LDAP, SMB)
- [ ] AD Enumeration (BloodHound, SharpHound, ldapsearch, PowerView, ADExplorer)
- [ ] Kerberoasting (Request TGS, crack with Hashcat)
- [ ] AS-REP Roasting (Users without pre-auth, crack)
- [ ] Pass-the-Hash (as above)
- [ ] Pass-the-Ticket (Golden Ticket, Silver Ticket, Diamond Ticket)
- [ ] DCShadow (persistence)
- [ ] DCSync (mimikatz lsadump::dcsync)
- [ ] SMB Relay (NTLM relay)
- [ ] LLMNR/NBT-NS Poisoning (Responder)
- [ ] GPO Abuse (Group Policy modification)
- [ ] ACL Abuse (AdminSDHolder, Extended Rights)
- [ ] Hands-on: Attack AD, Kerberoast, DCSync

## Module 36: Wi-Fi & Wireless Hacking
- [ ] 802.11 Standards (Wi-Fi 4,5,6,6E,7 - channels, frequencies, bandwidth)
- [ ] Wi-Fi Security (WEP, WPA, WPA2-PSK, WPA2-Enterprise, WPA3-SAE, WPA3-Enterprise, OWE)
- [ ] Monitor Mode vs Managed Mode
- [ ] Aircrack-ng Suite (airmon-ng, airodump-ng, aircrack-ng, aireplay-ng, airbase-ng, airdecap-ng)
- [ ] Capturing 4-Way Handshake (airodump-ng, bettercap)
- [ ] Cracking WPA/WPA2 (dictionary, brute-force, PMKID attack)
- [ ] PMKID Attack (no client required, eapol capture)
- [ ] WPS Attack (Pixie Dust, brute-force)
- [ ] Evil Twin Attack (Rogue AP, hostapd-mana, Airgeddon)
- [ ] Wi-Fi Deauthentication Attack (aireplay-ng -0, mdk4)
- [ ] Defenses (WPA3, MFP, 802.1X, rogue AP detection)
- [ ] Tools (Aircrack-ng, Reaver, Wash, BetterCAP, Wifite, Airgeddon)
- [ ] Hands-on: Capture handshake, crack WPA2, create evil twin

## Module 37: Cloud Security (AWS & Azure)
- [ ] Cloud Models (IaaS, PaaS, SaaS, FaaS, CaaS)
- [ ] Shared Responsibility Model (AWS vs Azure vs GCP)
- [ ] AWS Security (IAM, S3, EC2, Lambda, RDS, VPC, CloudTrail, GuardDuty)
- [ ] Common AWS Misconfigurations (public S3, over-permissive IAM, open security groups)
- [ ] AWS Attack Techniques (Metadata SSRF, IAM privilege escalation, S3 bucket enumeration)
- [ ] Azure Security (Entra ID, Blob Storage, VMs, Key Vault, Azure AD, App Services)
- [ ] Common Azure Misconfigurations (open Blob Storage, guest users, privilege escalation)
- [ ] Azure Attack Techniques (Pass-the-PRT, Azure AD Connect, Application impersonation)
- [ ] Cloud Enumeration Tools (ScoutSuite, Prowler, CloudBrute, Pacu)
- [ ] Defenses (CSPM, CWPP, CASB)
- [ ] Hands-on: Identify S3 bucket misconfigurations, enumerate Azure tenant

## Module 38: Mobile Security (Android & iOS)
- [ ] Android Architecture (kernel, HAL, ART, permissions, sandbox)
- [ ] Android Security (root vs non-root, bootloader, verified boot, SafetyNet)
- [ ] Android App Analysis (APKTool, jadx, MobSF, Frida, Objection)
- [ ] Android Vulnerabilities (insecure storage, IPC, weak SSL, deep links, WebView)
- [ ] iOS Architecture (kernel, XNU, sandbox, entitlements, app signing)
- [ ] iOS Security (jailbreak types: tethered, untethered, semi-tethered)
- [ ] iOS App Analysis (Frida, Objection, Hopper, Ghidra for iOS)
- [ ] iOS Vulnerabilities (insecure data storage, URL schemes, keychain)
- [ ] Static vs Dynamic Analysis
- [ ] Hands-on: Analyze Android APK for security issues

---

## PHASE 10: BLUE TEAM & DEFENSE (Modules 39-44)

## Module 39: Denial of Service (DoS/DDoS) - Defense & Mitigation
- [ ] DDoS Detection (traffic spikes, anomaly detection, baseline)
- [ ] On-Premise Mitigation (rate limiting, blackholing/RTBH, scrubbing centers, traffic diversion)
- [ ] Cloud-Based Mitigation (Cloudflare, AWS Shield Advanced, Akamai Prolexic, Imperva, Google Cloud Armor)
- [ ] Anycast Network (how it helps DDoS mitigation)
- [ ] Rate Limiting Strategies (per IP, per session, per API key)
- [ ] SYN Cookies (mitigating SYN flood)
- [ ] BGP Flowspec (traffic filtering at ISP level)
- [ ] WAF for Application Layer Attacks (Slowloris, HTTP flood)
- [ ] DDoS Response Plan (playbook example)
- [ ] Real-world DDoS Mitigation Case Studies
- [ ] Hands-on: Configure rate limiting, analyze DDoS logs

## Module 40: Firewalls & IDS/IPS
- [ ] Firewall Types (Packet filtering, Stateful, Next-Gen, WAF, Cloud Firewall)
- [ ] iptables/nftables (Linux firewall: rules, chains, tables)
- [ ] Windows Firewall (inbound/outbound rules, advanced security)
- [ ] pfSense (open-source firewall/router)
- [ ] IDS vs IPS (difference, placement, active vs passive)
- [ ] Snort (rules writing, deployment, alert analysis)
- [ ] Suricata (multi-threaded, GPU acceleration)
- [ ] Zeek/Bro (network analysis framework)
- [ ] WAF (ModSecurity, Cloudflare WAF, AWS WAF)
- [ ] Hands-on: Write Snort rule, detect SQL injection, block with iptables

## Module 41: Log Analysis & SIEM
- [ ] Why Logs Matter (detection, investigation, compliance)
- [ ] Linux Logs (/var/log: auth.log, syslog, kern.log, dpkg.log, apt, nginx, mysql)
- [ ] Windows Event Logs (Security, System, Application, PowerShell, Setup, ForwardedEvents)
- [ ] Sysmon (advanced logging: process creation, network connections, file changes)
- [ ] Auditd (Linux auditing)
- [ ] Log Rotation and Retention
- [ ] SIEM Architecture (data collection, normalization, indexing, correlation, alerting, dashboard)
- [ ] ELK Stack (Elasticsearch, Logstash, Kibana, Beats)
- [ ] Wazuh (open-source SIEM + XDR)
- [ ] Splunk (SPL queries, dashboards, alerts)
- [ ] Writing Detection Queries (find brute force, port scans, malware)
- [ ] Hands-on: Build ELK stack, ingest logs, write detection rules

## Module 42: Malware Analysis & Reverse Engineering
- [ ] Malware Types (Virus, Worm, Trojan, Ransomware, Rootkit, Bootkit, Dropper, Downloader, Keylogger, Adware, Spyware, RAT)
- [ ] Malware Delivery (phishing, drive-by, supply chain, removable media)
- [ ] Static Analysis (strings, hashes, PE structure, import table, PE-bear, Detect It Easy, pestudio)
- [ ] Dynamic Analysis (sandbox: Cuckoo, CAPE, Any.Run, Joe Sandbox)
- [ ] Behavioral Analysis (registry changes, file changes, network connections)
- [ ] Basic Reverse Engineering (x86/x64 assembly, Ghidra, IDA Free, x64dbg, OllyDbg)
- [ ] Malware Persistence (registry, scheduled tasks, startup)
- [ ] Malware Evasion (anti-VM, anti-debug, anti-sandbox)
- [ ] Safe Malware Handling (isolated VM, REMnux, FLARE VM)
- [ ] Hands-on: Analyze real malware sample (safe one, e.g., WannaCry simulation)

## Module 43: Incident Response
- [ ] Incident Response Lifecycle (NIST 800-61: Preparation, Detection & Analysis, Containment, Eradication, Recovery, Post-Incident)
- [ ] IR Team Roles (Incident Commander, Technical Lead, Forensic Lead, Communications, Legal, Management)
- [ ] Incident Classification (SEV1, SEV2, SEV3, SEV4)
- [ ] Detection Methods (alerts, users, threat intel, proactive hunting)
- [ ] Analysis (Triage, timeline creation, IOC extraction)
- [ ] Containment Strategies (network isolation, firewall block, account disable, DNS sinkhole)
- [ ] Eradication (malware removal, patch, credential reset)
- [ ] Recovery (restore from backup, monitoring)
- [ ] Post-Incident (lessons learned, IR playbook update, KPIs)
- [ ] IR Playbooks (ransomware, phishing, data exfiltration, DoS/DDoS, insider threat)
- [ ] Hands-on: Simulate ransomware incident, run through full IR process

## Module 44: Digital Forensics
- [ ] Forensic Principles (Locard's Exchange Principle, order of volatility, chain of custody)
- [ ] Disk Forensics (dd, FTK Imager, Autopsy, The Sleuth Kit)
- [ ] File Carving (foremost, scalpel, photorec)
- [ ] Memory Forensics (Volatility 3: processes, network, files, registry)
- [ ] Network Forensics (PCAP analysis, Wireshark, Zeek, tcpdump)
- [ ] Browser Forensics (history, downloads, bookmarks, cache, cookies)
- [ ] Email Forensics (headers analysis, phishing investigation)
- [ ] Registry Forensics (autostart, recently used, USB history)
- [ ] Timeline Analysis (Plaso, log2timeline)
- [ ] Anti-Forensics (timestomping, log wiping, encryption)
- [ ] Hands-on: Investigate compromised machine, recover deleted files, analyze memory dump

---

## PHASE 11: THREAT HUNTING & PROACTIVE DEFENSE (Modules 45-46)

## Module 45: Threat Hunting
- [ ] Threat Hunting vs Incident Response (proactive vs reactive)
- [ ] Hunting Maturity Model (HM0 to HM4)
- [ ] Hypothesis-Driven Hunting (assume compromise, investigate)
- [ ] Hunting Techniques (IoC-based, behavior-based, anomaly-based)
- [ ] Hunting Data Sources (logs, netflow, EDR, endpoints)
- [ ] MITRE ATT&CK for Hunting (TTP mapping)
- [ ] Hunting with EDR (CrowdStrike, SentinelOne, Defender for Endpoint)
- [ ] Hunting with SIEM (Splunk, ELK)
- [ ] Hunting Tools (Velociraptor, osquery, KAPE)
- [ ] Hands-on: Hunt for lateral movement, persistence, data exfiltration

## Module 46: Red Team vs Blue Team vs Purple Team
- [ ] Red Team (offensive, stealth, objective-based, APT simulation)
- [ ] Blue Team (defensive, monitoring, hunting, IR)
- [ ] Purple Team (collaboration, knowledge transfer, improving detection)
- [ ] Red Team vs Penetration Testing (differences: scope, stealth, time, objectives)
- [ ] Red Team Kill Chain vs MITRE ATT&CK (tactical mapping)
- [ ] TIBER-EU Framework (EU red teaming framework)
- [ ] Building a Red Team (tools, TTPs, reporting)
- [ ] Building a Blue Team (tools, detection, IR, hunting)
- [ ] Tabletop Exercises (scenario-based)
- [ ] Hands-on: Run purple team exercise, improve detection rules

---

## PHASE 12: RISK, REPORTING & CAREER (Modules 47-49)

## Module 47: Risk Management & Business Continuity
- [ ] Risk Management Framework (NIST RMF: Identify, Protect, Detect, Respond, Recover)
- [ ] Risk Assessment (qualitative vs quantitative, SLE, ARO, ALE, ROI)
- [ ] Risk Treatment (Avoid, Transfer, Mitigate, Accept)
- [ ] Business Impact Analysis (BIA - critical functions, RTO, RPO, MTPD, WRT)
- [ ] Disaster Recovery (DR - DR plan, hot site, cold site, warm site, backup strategies)
- [ ] Business Continuity (BC - alternate processes, work-from-home, supply chain)
- [ ] Hands-on: Write basic risk assessment for mock company

## Module 48: Reporting & Communication
- [ ] Why Reporting is the Most Important Skill (clients read reports, not technical results)
- [ ] Types of Reports (vulnerability assessment, penetration test, red team, incident report)
- [ ] Report Structure (Executive Summary, Scope, Methodology, Findings, Risk Ratings, Recommendations, Appendix)
- [ ] Executive Summary (non-technical, business impact, bottom line)
- [ ] Finding Write-Up (vulnerability name, CVE, CVSS, description, reproduction steps, impact, recommendation)
- [ ] Risk Ratings (Critical, High, Medium, Low, Informational - criteria for each)
- [ ] Proof of Concept (screenshots, commands, video, logs)
- [ ] Remediation Guidance (clear, actionable, prioritized)
- [ ] Report Templates (Dradis, Serpico, MagicTree, PwnDoc)
- [ ] Verbal Communication (debriefing, explaining technical issues to non-technical)
- [ ] Hands-on: Write full pentest report with 3 findings

## Module 49: Career Path, Certifications & Job Preparation
- [ ] Career Paths:
    - Offensive (Junior Pentester → Senior Pentester → Red Teamer → Principal)
    - Defensive (SOC Analyst → Threat Hunter → IR Lead → SOC Manager)
    - Engineering (Security Engineer → Architect → DevOps Security)
    - GRC (Compliance Analyst → Risk Manager → CISO)
    - Research (Malware Analyst → Reverse Engineer → Exploit Developer)
- [ ] Certification Roadmap (Realistic order):
    - Beginner: CompTIA Security+, Network+, Linux+
    - Intermediate: TCM Security PNPT (best for practical), eJPT, CySA+, Blue Team Level 1
    - Advanced: OSCP (gold standard for pentesting), OSWP, GPEN, CRTP
    - Expert: OSCE3, OSEP, OSED, OSWE, GXPN, CISSP (management)
- [ ] Building Portfolio (GitHub, blog, home lab, writeups, CVEs, HTB rank)
- [ ] Resume Writing for Cybersecurity (keywords, projects, experience)
- [ ] LinkedIn Networking (how to connect, what to post)
- [ ] Interview Preparation (technical questions, practical tests, HR screening)
- [ ] Salary Negotiation (market rates by role, country, experience)
- [ ] Hands-on: Build LinkedIn profile, write resume, prepare for interview questions

---

## PHASE 13: CAPSTONE PROJECTS (Final Assessment)

- [ ] Project 1: Build Complete Home Lab (Kali, Windows 10, Ubuntu, Metasploitable, AD lab)
- [ ] Project 2: HTB Starting Point + 10 Easy Machines (document walkthroughs)
- [ ] Project 3: Full Penetration Test Report (Recon → Exploitation → Post-Exploitation → Reporting)
- [ ] Project 4: Vulnerability Assessment Report (Scan + manual verification + recommendations)
- [ ] Project 5: Build Home SOC (ELK + Wazuh + Sysmon) + Generate Alerts + Investigate
- [ ] Project 6: Incident Response Simulation (Attack → Detection → IR Playbook → Forensic Analysis)
- [ ] Project 7: Write 3 Security Tooling Scripts (Python/Bash for automation)
- [ ] Project 8: Write Blog Posts for 5 CTF Solutions (demonstrate communication)

---

## RESOURCES SECTION

## Practice Platforms
- TryHackMe (beginner friendly)
- HackTheBox (advanced)
- PortSwigger Web Security Academy (best for web)
- PicoCTF (student friendly)
- OverTheWire (wargames)
- VulnHub (VM challenges)
- PentesterLab

## Must-Have Books
- The Web Application Hacker's Handbook (Stuttard & Pinto)
- Penetration Testing: A Hands-On Introduction to Hacking (Georgia Weidman)
- The Hacker Playbook 3 (Peter Kim)
- Red Team Field Manual (RTFM)
- Blue Team Field Manual (BTFM)
- Practical Malware Analysis (Sikorski & Honig)

## Tools Collection (Preinstalled in Kali)
- All tools mentioned above are available in Kali Linux

## YouTube Channels
- John Hammond, IppSec, The Cyber Mentor, NetworkChuck, STÖK, Hackersploit
