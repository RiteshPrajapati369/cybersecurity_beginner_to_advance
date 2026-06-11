# Module 1: Introduction to Cybersecurity

> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 6–8 hours | 📁 Folder: `01_FOUNDATIONS`

---

## Table of Contents

1. [What is Cybersecurity?](#1-what-is-cybersecurity)
2. [CIA Triad, AAA, and Non-repudiation](#2-cia-triad-aaa-and-non-repudiation)
3. [Types of Hackers](#3-types-of-hackers)
4. [Threat Actors](#4-threat-actors)
5. [Cyber Kill Chain](#5-cyber-kill-chain)
6. [MITRE ATT&CK Framework](#6-mitre-attck-framework)
7. [Attack Vectors and Attack Surfaces](#7-attack-vectors-and-attack-surfaces)
8. [Real-World Breach Case Studies](#8-real-world-breach-case-studies)
9. [Hands-On Lab: Map a Real Attack to MITRE ATT&CK](#9-hands-on-lab-map-a-real-attack-to-mitre-attck)
10. [Summary & Key Takeaways](#10-summary--key-takeaways)
11. [Quiz: Test Your Knowledge](#11-quiz-test-your-knowledge)
12. [Resources](#12-resources)

---

## 1. What is Cybersecurity?

Cybersecurity is the practice of **protecting systems, networks, programs, and data** from digital attacks, unauthorized access, damage, or theft.

In simple terms: just like physical security protects buildings and people, cybersecurity protects digital assets.

### Why Does It Matter?

- Every business, government, hospital, and individual relies on digital infrastructure.
- Data breaches cost companies an average of **$4.45 million** (IBM Cost of a Data Breach Report, 2023).
- Cybercrime is projected to cost the world **$10.5 trillion annually by 2025** (Cybersecurity Ventures).
- Personal data (passwords, bank accounts, health records) is constantly under threat.

### Domains of Cybersecurity

Cybersecurity is a broad field with many specializations:

| Domain | Description |
|---|---|
| Network Security | Protecting network infrastructure from attacks |
| Application Security | Securing software and web applications |
| Endpoint Security | Protecting devices (laptops, phones, servers) |
| Cloud Security | Securing cloud environments (AWS, Azure, GCP) |
| Identity & Access Management | Controlling who can access what |
| Cryptography | Protecting data using encryption |
| Incident Response | Responding to and recovering from attacks |
| Threat Intelligence | Researching and predicting attacker behavior |
| Governance, Risk & Compliance (GRC) | Policies, regulations, and risk management |

---

## 2. CIA Triad, AAA, and Non-repudiation

These are the **core principles** that every cybersecurity concept is built upon. Memorize them.

---

### 2.1 The CIA Triad

The CIA Triad is the **foundational model** for information security. Every security decision maps back to one or more of these three pillars.

```
        Confidentiality
             /\
            /  \
           /    \
          /      \
Integrity -------- Availability
```

#### Confidentiality

Ensuring that **information is only accessible to those authorized** to see it.

**Goal:** Prevent unauthorized disclosure of data.

**Examples:**
- Encrypting a file so only the recipient can read it
- Setting a password on a database
- Using role-based access control so employees only see data they need

**Threats to Confidentiality:**
- Eavesdropping / Sniffing (intercepting network traffic)
- Phishing (tricking users into revealing credentials)
- Insider threats (employee leaking data)
- SQL Injection (dumping database contents)

**Controls:**
- Encryption (AES, RSA, TLS)
- Access control lists (ACLs)
- Multi-factor authentication (MFA)
- Data classification

---

#### Integrity

Ensuring that **data is accurate, complete, and has not been tampered with** — either intentionally or accidentally.

**Goal:** Prevent unauthorized modification of data.

**Examples:**
- A hash verification that confirms a downloaded file hasn't been corrupted
- Digital signatures on emails proving they weren't modified in transit
- Version control in software (tracking every change)

**Threats to Integrity:**
- Man-in-the-Middle (MITM) attacks (modifying data in transit)
- SQL Injection (modifying database records)
- Malware (corrupting or altering files)
- Insider threats (unauthorized data modification)

**Controls:**
- Hashing (MD5, SHA-256) — verify file integrity
- Digital signatures
- Checksums
- Audit logs
- Version control systems

---

#### Availability

Ensuring that **systems and data are accessible when needed** by authorized users.

**Goal:** Prevent denial of service to legitimate users.

**Examples:**
- A hospital's patient records must always be accessible
- A banking website must not go down during peak hours
- Backups ensure data is available even after a disaster

**Threats to Availability:**
- Denial of Service (DoS) / Distributed DoS (DDoS) attacks
- Ransomware (encrypting data and holding it hostage)
- Hardware failures
- Natural disasters

**Controls:**
- Redundancy (backup servers, RAID)
- Load balancing
- Disaster Recovery Plans (DRP)
- DDoS mitigation services (Cloudflare, AWS Shield)
- Uninterruptible Power Supplies (UPS)

---

#### Real-World CIA Triad Examples

| Scenario | Violated Principle |
|---|---|
| Hacker reads your private emails | Confidentiality |
| Hacker changes your bank balance | Integrity |
| DDoS attack takes down a website | Availability |
| Ransomware encrypts hospital files | Availability + Integrity |
| Employee leaks customer database | Confidentiality |

> 💡 **Remember:** Security is about balancing all three. Making a system completely unavailable for maintenance protects confidentiality but hurts availability.

---

### 2.2 AAA Framework

AAA stands for **Authentication, Authorization, and Accounting**. It is the backbone of access control systems.

#### Authentication — *Who are you?*

The process of **verifying the identity** of a user, device, or system.

**Authentication Factors:**
- **Something you know** — Password, PIN, security question
- **Something you have** — Smart card, hardware token (YubiKey), phone (OTP)
- **Something you are** — Fingerprint, retinal scan, face recognition (biometrics)
- **Somewhere you are** — GPS location, IP address restriction
- **Something you do** — Typing pattern, behavior analytics

**Multi-Factor Authentication (MFA):** Using two or more of these factors. Example: password + OTP code from your phone.

**Authentication Protocols:**
- Kerberos (used in Windows Active Directory)
- RADIUS (used for network access)
- LDAP (directory authentication)
- OAuth 2.0 (used by Google, Facebook login)
- SAML (used in enterprise SSO)

---

#### Authorization — *What are you allowed to do?*

After authentication, authorization determines **what resources and actions** the verified identity is permitted to access.

**Authorization Models:**

| Model | Description | Example |
|---|---|---|
| DAC (Discretionary Access Control) | Owner sets permissions | Linux file permissions |
| MAC (Mandatory Access Control) | System enforces labels | Military classification (Top Secret, Secret) |
| RBAC (Role-Based Access Control) | Access based on job role | Admin, User, Guest roles |
| ABAC (Attribute-Based Access Control) | Access based on attributes | Time, location, device type |

**Principle of Least Privilege (PoLP):** Users should only have the **minimum permissions needed** to do their job. A developer should not have admin access to production databases.

---

#### Accounting (Auditing) — *What did you do?*

The process of **tracking and logging user activity** so it can be reviewed, audited, or investigated.

**What accounting logs:**
- Login times and locations
- Files accessed or modified
- Commands executed
- Network connections made
- Failed login attempts

**Why it matters:**
- Detects suspicious behavior
- Provides evidence for forensic investigations
- Meets compliance requirements (HIPAA, PCI-DSS, GDPR)
- Supports incident response

**Tools:**
- Windows Event Logs (Security Event ID 4624 = successful login)
- Linux `/var/log/auth.log`
- Sysmon
- SIEM platforms (Splunk, ELK, Wazuh)

---

### 2.3 Non-repudiation

Non-repudiation means ensuring that **a person cannot deny having performed an action**.

Think of it as digital evidence that proves who did what.

**Examples:**
- A user signs an email with their private key. Later they cannot claim they didn't send it.
- A digital transaction is timestamped and signed — the user can't deny making the purchase.
- A system logs show a specific admin account deleted the database at 3:47 AM.

**How it's achieved:**
- **Digital Signatures** — Uses asymmetric cryptography; the sender signs with their private key
- **Audit Logs** — Timestamped records tied to authenticated identities
- **Certificates** — PKI certificates bind identity to cryptographic keys
- **Blockchain** — Immutable record of transactions

**Why it matters in cybersecurity:**
- Legal proceedings require non-repudiation (proving someone hacked a system)
- Financial systems depend on it (nobody can deny a wire transfer they authorized)
- Compliance frameworks require it

---

## 3. Types of Hackers

The cybersecurity industry uses **color-coded hats** to categorize hackers by their intent, ethics, and authorization level.

### Hat System

```
White Hat → Ethical, authorized
Black Hat → Malicious, unauthorized
Grey Hat → In between — not always authorized, not always malicious
```

### Detailed Breakdown

#### ⬜ White Hat (Ethical Hacker / Penetration Tester)

- **Authorization:** Always has written permission
- **Intent:** Find vulnerabilities before malicious actors do
- **Employed by:** Companies, governments, security firms
- **Activities:** Penetration testing, vulnerability assessments, bug bounty hunting
- **Certifications:** CEH, OSCP, PNPT, GPEN
- **Example:** A bank hires a security firm to test its online banking system

---

#### ⬛ Black Hat (Cracker / Cybercriminal)

- **Authorization:** None — operates illegally
- **Intent:** Financial gain, espionage, sabotage, or just chaos
- **Activities:** Stealing data, ransomware deployment, selling access on dark web
- **Consequences:** Criminal prosecution, prison, fines
- **Example:** The hackers behind the WannaCry ransomware attack (2017)

---

#### 🩶 Grey Hat

- **Authorization:** Operates without permission but may disclose findings
- **Intent:** Usually not malicious, but not strictly ethical either
- **Activities:** Breaking into systems without permission, then notifying the owner
- **Problem:** Even without malicious intent, this is still illegal in most countries
- **Example:** A hacker finds a vulnerability in a company's website, breaks in to prove it, then emails the company demanding a "reward"

---

#### 🟥 Red Team

- **Role:** Offensive security professionals who **simulate real attacks**
- **Authorization:** Fully authorized by the organization
- **Goal:** Test an organization's defenses by acting like a real attacker (APT simulation)
- **Different from penetration testing:** Longer engagements, stealthier, objective-based (not just finding vulnerabilities — actually achieving goals like stealing data)
- **Tools:** Cobalt Strike, Metasploit, custom C2 frameworks

---

#### 🟦 Blue Team

- **Role:** Defensive security professionals who **detect, prevent, and respond** to attacks
- **Works in:** SOC (Security Operations Center)
- **Activities:** Monitoring alerts, analyzing logs, incident response, threat hunting
- **Tools:** SIEM (Splunk, ELK), IDS/IPS (Snort, Suricata), EDR (CrowdStrike)

---

#### 🟪 Purple Team

- **Role:** Bridge between Red Team and Blue Team
- **Goal:** Improve detection and response by having both teams **collaborate in real time**
- **How it works:** Red team attacks while Blue team detects; Purple team analyzes gaps and improves defenses
- **Outcome:** Better detection rules, updated playbooks, stronger defenses

---

#### 🟩 Green Hat (Newbie / Learner)

- **Experience:** New to hacking, actively learning
- **Intent:** Usually legitimate — wants to become a security professional
- **Characteristics:** Eager to learn, may make mistakes, follows tutorials closely
- **Note:** Sometimes used to describe someone green (inexperienced) who may be unaware of what's ethical

---

#### Script Kiddie

- **Experience:** Little to no real technical knowledge
- **How they operate:** Use pre-made tools and scripts written by others, without understanding how they work
- **Intent:** Often just disruption, trolling, or looking cool
- **Danger:** Despite low skill, they can still cause real damage using powerful tools
- **Example:** A teenager downloads a DDoS tool from YouTube and attacks a game server

---

### Summary Table

| Hat | Authorization | Intent | Skill Level |
|---|---|---|---|
| White Hat | Authorized | Ethical — defensive | High |
| Black Hat | Unauthorized | Malicious | Varies |
| Grey Hat | Unauthorized | Mixed | Medium–High |
| Red Team | Authorized | Simulate attacks | Very High |
| Blue Team | N/A | Defend | High |
| Purple Team | N/A | Collaborate | High |
| Green Hat | N/A | Learning | Low–Medium |
| Script Kiddie | Unauthorized | Disruption | Low |

---

## 4. Threat Actors

A **threat actor** is any individual, group, or entity that carries out — or has the capability to carry out — a cyberattack.

Understanding who attackers are helps defenders know **what to expect, how sophisticated the attack will be, and what their motives are**.

---

### 4.1 Advanced Persistent Threat (APT)

**Who:** Nation-state sponsored hacking groups or highly sophisticated criminal organizations.

**Characteristics:**
- Well-funded and extremely skilled
- Patient — they stay inside networks for **months or years** without detection
- Use custom malware and zero-day exploits
- Have specific, high-value targets (governments, defense contractors, critical infrastructure)

**Motivation:** Espionage, sabotage, intellectual property theft, political influence

**Famous APT Groups:**

| Group Name | Alias | Country | Known Attacks |
|---|---|---|---|
| APT28 | Fancy Bear | Russia | DNC Hack (2016), NATO targets |
| APT29 | Cozy Bear | Russia | SolarWinds (2020) |
| APT41 | Double Dragon | China | Dual espionage + cybercrime |
| Lazarus Group | Hidden Cobra | North Korea | WannaCry, $81M Bangladesh Bank heist |
| Equation Group | — | USA (NSA) | Stuxnet, EternalBlue |

**Defense:** Threat intelligence feeds, network segmentation, behavioral monitoring, zero-trust architecture

---

### 4.2 Hacktivists

**Who:** Individuals or groups who hack for political, social, or ideological causes.

**Characteristics:**
- Motivated by ideology, not financial gain
- Often public about their attacks (leak data, deface websites)
- Variable skill level — from script kiddies to very sophisticated

**Motivation:** Political protest, exposing corruption, free speech, environmental causes

**Methods:** DDoS attacks, website defacement, data leaks (doxing)

**Famous Examples:**
- **Anonymous** — Attacked Visa, Mastercard, PayPal after WikiLeaks controversy (Operation Payback); attacked governments worldwide
- **LulzSec** — Hacked Sony, FBI affiliate sites, CIA website
- **Team Cymru** — Anti-child exploitation

**Defense:** Web Application Firewalls (WAF), DDoS mitigation, public-facing asset hardening

---

### 4.3 Cybercriminals

**Who:** Individuals or organized crime groups motivated purely by **financial gain**.

**Characteristics:**
- Operate like businesses — structured, efficient, profit-driven
- Sell stolen data on dark web marketplaces (credit cards, credentials, SSNs)
- Use ransomware-as-a-service (RaaS) models
- May hire specialists (initial access brokers, ransomware coders, money mules)

**Motivation:** Money

**Methods:**
- Ransomware (LockBit, ALPHV/BlackCat, Cl0p)
- Business Email Compromise (BEC)
- Credit card fraud, identity theft
- Banking trojans (Emotet, TrickBot)
- Cryptocurrency theft

**Famous Groups:**
- **LockBit** — Most prolific ransomware group (2022–2024)
- **Cl0p** — MOVEit transfer mass exploitation (2023)
- **FIN7** — Stole $1B+ from restaurants, retailers, hospitality

---

### 4.4 Insider Threats

**Who:** Current or former employees, contractors, partners, or anyone with **legitimate access** to systems.

**Why They're Dangerous:** They already have authorized access — they bypass perimeter defenses entirely.

**Types:**

| Type | Description | Example |
|---|---|---|
| Malicious Insider | Intentionally causes harm | Disgruntled employee deletes databases before quitting |
| Negligent Insider | Causes harm through carelessness | Employee clicks phishing link, installs malware |
| Compromised Insider | Account taken over by attacker | Attacker uses stolen VPN credentials |

**Motivation:** Money, revenge, ideology, coercion, negligence

**Famous Cases:**
- **Edward Snowden** (2013) — NSA contractor leaked classified surveillance programs
- **Chelsea Manning** (2010) — Army intelligence analyst leaked classified military documents to WikiLeaks
- **Ubiquiti (2021)** — Employee stole source code and data, then posed as the attacker to extort the company

**Defense:**
- User and Entity Behavior Analytics (UEBA)
- Least privilege access
- Data Loss Prevention (DLP)
- Offboarding procedures (immediate account deactivation)
- Background checks

---

### 4.5 Nation-State Actors

**Who:** Governments that conduct cyber operations against other nations, organizations, or individuals.

**Characteristics:**
- The most sophisticated and well-resourced threat actors
- Have legal cover within their own country
- Conduct both espionage AND destructive attacks

**Motivations:** Geopolitical advantage, military intelligence, economic espionage, election interference, critical infrastructure disruption

**Notable Operations:**

| Operation | Actor | Target | Impact |
|---|---|---|---|
| Stuxnet (2010) | USA/Israel | Iran nuclear program | Destroyed ~1,000 centrifuges |
| NotPetya (2017) | Russia | Ukraine (then global) | $10B+ in damages |
| SolarWinds (2020) | Russia | US Government, 18,000+ orgs | Massive espionage campaign |
| Volt Typhoon (2023) | China | US critical infrastructure | Pre-positioning for potential conflict |

---

### Threat Actor Comparison

| Actor | Motivation | Skill | Resources | Persistence |
|---|---|---|---|---|
| Nation-State / APT | Espionage, sabotage | Extremely High | Very High | Years |
| Cybercriminals | Financial gain | Medium–High | Medium | Months |
| Hacktivists | Ideology | Low–High | Low | Short bursts |
| Insider Threats | Varies | Varies | Has access | Varies |
| Script Kiddies | Fun, disruption | Low | Low | Hours |

---

## 5. Cyber Kill Chain

The **Cyber Kill Chain** is a framework developed by **Lockheed Martin** (originally from military terminology) that describes the **7 stages of a cyberattack**.

Understanding the kill chain helps defenders know **where in the attack lifecycle they can intervene** to stop an attack.

> 💡 **Key concept:** If you can disrupt any stage of the kill chain, the attack fails.

---

### The 7 Stages

```
1. Reconnaissance
       ↓
2. Weaponization
       ↓
3. Delivery
       ↓
4. Exploitation
       ↓
5. Installation
       ↓
6. Command & Control (C2)
       ↓
7. Actions on Objectives
```

---

### Stage 1: Reconnaissance

**What happens:** The attacker **researches the target** to gather intelligence before launching the attack.

**Two types:**
- **Passive Reconnaissance:** No direct contact with target. Uses publicly available info (OSINT).
  - Google Dorking, LinkedIn, job postings, WHOIS, Shodan, DNS lookups
- **Active Reconnaissance:** Direct interaction with the target system.
  - Port scanning (Nmap), banner grabbing, vulnerability scanning

**What attackers find:**
- Employee names, emails, job roles (LinkedIn)
- Technology stack (job postings reveal "We use Cisco ASA firewalls")
- Open ports and services
- IP ranges and network structure
- Email formats (first.last@company.com)

**Defense:** Minimize public-facing information, monitor for scanning activity, use honeypots

---

### Stage 2: Weaponization

**What happens:** The attacker **creates or acquires the weapon** (malware, exploit) they will use to attack.

**Activities:**
- Combining an exploit with a malicious payload (e.g., a Word document that drops a backdoor when opened)
- Purchasing a ransomware kit from a RaaS provider
- Writing custom malware
- Creating a phishing email with a malicious attachment
- Setting up a malicious website

**No contact with the victim at this stage** — it's all preparation.

**Defense:** Difficult to detect at this stage. Focus is on hardening defenses so the weapon fails on delivery or exploitation.

---

### Stage 3: Delivery

**What happens:** The attacker **delivers the weapon** to the target.

**Delivery methods:**
- Phishing email with malicious attachment or link
- Watering hole attack (compromising a website the target visits)
- USB drop (leaving infected USB drives in parking lot)
- Supply chain attack (compromising software updates)
- Drive-by download (visiting a compromised website triggers automatic download)
- Direct exploitation of a public-facing service

**Defense:**
- Email filtering and sandboxing
- Web proxy filtering
- User awareness training
- Disabling auto-run on USB

---

### Stage 4: Exploitation

**What happens:** The delivered weapon **executes and exploits a vulnerability** to gain initial access.

**Examples:**
- A malicious macro in a Word document runs when the user opens it
- An exploit triggers a buffer overflow in a web server
- A phishing page captures credentials
- A zero-day exploit in a browser triggers code execution

**Types of vulnerabilities exploited:**
- Software vulnerabilities (unpatched CVEs)
- Human vulnerabilities (social engineering)
- Configuration errors (default credentials, open RDP)

**Defense:**
- Patch management (keep systems updated)
- Disable macros in Office documents
- Endpoint Detection and Response (EDR)
- User training

---

### Stage 5: Installation

**What happens:** The attacker **installs malware or a backdoor** to maintain access.

**What gets installed:**
- Remote Access Trojan (RAT) — gives persistent remote access
- Web shell — backdoor on a web server
- Rootkit — hides the attacker's presence deep in the OS
- Scheduled task or registry run key — ensures malware starts on reboot

**Goal:** Establish **persistence** — the ability to re-enter even if the initial access method is discovered.

**Defense:**
- Application whitelisting (AppLocker)
- File Integrity Monitoring (FIM)
- Behavioral detection in EDR
- Hunt for new scheduled tasks, services, registry keys

---

### Stage 6: Command & Control (C2)

**What happens:** The installed malware **connects back to the attacker's server** to receive commands.

**How it works:**
- Malware on victim's machine "phones home" to attacker's C2 server
- Attacker sends commands: "take screenshot," "dump passwords," "move to another machine"
- Communication is often disguised to look like normal traffic (HTTP, HTTPS, DNS tunneling)

**C2 Frameworks used by attackers (and red teamers):**
- Cobalt Strike (most popular — commercial but widely pirated)
- Metasploit Framework
- Covenant (.NET-based)
- Sliver (Go-based, open source)
- Brute Ratel C4 (commercial)

**Defense:**
- DNS filtering and monitoring
- Outbound traffic analysis (detecting beaconing)
- Network segmentation
- Blocking known C2 infrastructure via threat intelligence

---

### Stage 7: Actions on Objectives

**What happens:** The attacker **achieves their final goal**.

**What this looks like depends on attacker type:**
- **Cybercriminal:** Encrypt files with ransomware, demand payment
- **Espionage:** Exfiltrate sensitive documents, source code, or trade secrets
- **Hacktivist:** Deface the website, leak confidential data publicly
- **Nation-state:** Destroy critical infrastructure, position for future attacks
- **Insider:** Delete databases, steal customer data, sabotage systems

**Defense:**
- Data Loss Prevention (DLP) — detect large data transfers
- Honeytokens — fake files that trigger alerts when accessed
- Network traffic monitoring — detect abnormal data exfiltration
- Backup strategy — limit impact of ransomware

---

### Kill Chain Quick Reference

| Stage | Attacker Action | Defender Response |
|---|---|---|
| 1. Reconnaissance | Research the target | Reduce public footprint, monitor for scanning |
| 2. Weaponization | Create exploit + payload | Harden attack surface |
| 3. Delivery | Send phishing / exploit | Email filtering, user training |
| 4. Exploitation | Trigger the vulnerability | Patch management, disable macros, EDR |
| 5. Installation | Install backdoor/RAT | Application whitelisting, FIM |
| 6. C2 | Establish remote control | DNS filtering, outbound monitoring |
| 7. Objectives | Steal data / encrypt / destroy | DLP, backups, IR plan |

---

## 6. MITRE ATT&CK Framework

**MITRE ATT&CK** (Adversarial Tactics, Techniques, and Common Knowledge) is a **globally accessible knowledge base** of real-world adversary behaviors.

It was created by MITRE Corporation and is maintained based on real threat intelligence from actual attacks.

> 🔗 Live framework: [https://attack.mitre.org](https://attack.mitre.org)

---

### Why ATT&CK Matters

The Cyber Kill Chain tells you **what stage** an attack is in. MITRE ATT&CK tells you **exactly how** attackers execute each stage, with specific techniques observed in the wild.

**Used by:**
- Security Operations Centers (SOC) — to write detection rules
- Red Teams — to plan realistic attack simulations
- Threat Intelligence analysts — to track specific threat actor behaviors
- Incident Responders — to understand what happened

---

### Structure of MITRE ATT&CK

ATT&CK is organized as a **matrix** of:

- **Tactics** (the "why" — the attacker's goal at that moment)
- **Techniques** (the "how" — the specific method used)
- **Sub-techniques** (more specific variants of a technique)
- **Procedures** (real-world examples of how specific groups used a technique)

---

### The 14 Enterprise Tactics

| # | Tactic | ID | What It Means |
|---|---|---|---|
| 1 | Reconnaissance | TA0043 | Gather information before the attack |
| 2 | Resource Development | TA0042 | Set up infrastructure for the attack |
| 3 | Initial Access | TA0001 | Get into the network |
| 4 | Execution | TA0002 | Run malicious code |
| 5 | Persistence | TA0003 | Maintain foothold across reboots |
| 6 | Privilege Escalation | TA0004 | Gain higher-level permissions |
| 7 | Defense Evasion | TA0005 | Avoid detection |
| 8 | Credential Access | TA0006 | Steal usernames and passwords |
| 9 | Discovery | TA0007 | Learn the environment |
| 10 | Lateral Movement | TA0008 | Move to other systems on the network |
| 11 | Collection | TA0009 | Gather data of interest |
| 12 | Command & Control | TA0011 | Communicate with compromised systems |
| 13 | Exfiltration | TA0010 | Steal data out of the network |
| 14 | Impact | TA0040 | Damage, destroy, or disrupt |

---

### Technique Example: T1566 — Phishing

- **Tactic:** Initial Access (TA0001)
- **Technique:** Phishing (T1566)
- **Sub-techniques:**
  - T1566.001 — Spearphishing Attachment
  - T1566.002 — Spearphishing Link
  - T1566.003 — Spearphishing via Service (e.g., LinkedIn message)

**Procedure example:** APT29 (Cozy Bear) used spearphishing emails with malicious links to gain initial access to the Democratic National Committee in 2016.

---

### Technique Example: T1003 — OS Credential Dumping

- **Tactic:** Credential Access (TA0006)
- **Technique:** OS Credential Dumping (T1003)
- **Sub-techniques:**
  - T1003.001 — LSASS Memory (mimikatz)
  - T1003.002 — Security Account Manager (SAM)
  - T1003.003 — NTDS (Active Directory database)

---

### ATT&CK Navigator

The **ATT&CK Navigator** is a free web tool that lets you:
- Visualize which techniques an APT group uses
- Map your detections to ATT&CK
- Plan red team operations
- Identify coverage gaps

> 🔗 Tool: [https://mitre-attack.github.io/attack-navigator/](https://mitre-attack.github.io/attack-navigator/)

---

### Comparison: Kill Chain vs ATT&CK

| | Cyber Kill Chain | MITRE ATT&CK |
|---|---|---|
| Stages/Tactics | 7 | 14 |
| Specificity | High-level | Very granular |
| Real-world data | No | Yes |
| Threat actor tracking | No | Yes |
| Best used for | Understanding attack flow | Detection engineering, threat intel |

> Both frameworks are complementary — use the Kill Chain to understand the overall flow, and ATT&CK to understand the specific techniques used.

---

## 7. Attack Vectors and Attack Surfaces

---

### 7.1 What is an Attack Vector?

An **attack vector** is the **path or method** an attacker uses to gain unauthorized access to a system.

Think of it as the "door" through which an attacker enters.

**Common Attack Vectors:**

| Vector | Description | Example |
|---|---|---|
| Phishing | Deceptive emails or messages | CEO fraud email tricks employee |
| Malware | Malicious software | Ransomware delivered via email attachment |
| Unpatched Vulnerabilities | Exploiting known software bugs | EternalBlue exploit against unpatched SMB |
| Weak/Stolen Credentials | Password guessing or credential stuffing | RDP brute force |
| Social Engineering | Manipulating people | Calling IT helpdesk, pretending to be an employee |
| Physical Access | Direct hardware access | USB drop, tailgating into server room |
| Supply Chain | Compromising a trusted third party | SolarWinds Orion update |
| Insider Threat | Authorized user causing harm | Employee installing malware |
| Man-in-the-Middle | Intercepting communications | Rogue Wi-Fi hotspot |
| Zero-Day Exploits | Unknown, unpatched vulnerabilities | Stuxnet used 4 zero-days |

---

### 7.2 What is an Attack Surface?

The **attack surface** is the **total sum of all possible attack vectors** — every point where an attacker could try to enter or extract data.

**A larger attack surface = more risk.**

**Categories of Attack Surface:**

**Digital Attack Surface:**
- Open ports on servers
- Public-facing web applications
- APIs
- Employee email addresses
- Cloud storage buckets
- Remote access services (VPN, RDP, SSH)
- Third-party software and dependencies

**Physical Attack Surface:**
- Unprotected server rooms
- Unlocked workstations
- Exposed USB ports
- Security cameras and badge readers
- Physical documents

**Human Attack Surface:**
- Employees susceptible to phishing
- IT staff susceptible to vishing (voice phishing)
- Contractors with excessive access
- Disgruntled employees

---

### 7.3 Attack Surface Reduction

The goal of security is not just to defend every point — it is to **reduce the attack surface** so there are fewer points to defend.

**Strategies:**
- **Patch management** — Remove known vulnerabilities
- **Least privilege** — Limit what each account can access
- **Disable unused services** — Close ports and services that aren't needed
- **Network segmentation** — Divide the network so a breach in one area doesn't spread
- **Remove shadow IT** — Unauthorized apps and devices expand the surface
- **Regular audits** — Know what's exposed

> 💡 You cannot protect what you don't know exists. **Asset inventory** is the first step in attack surface management.

---

## 8. Real-World Breach Case Studies

Understanding real breaches makes abstract concepts concrete. These are four landmark incidents every cybersecurity professional must know.

---

### 8.1 Target Data Breach (2013)

**What happened:**
Attackers stole credit and debit card data from **40 million customers** and personal data from 70 million more — all through a third-party HVAC vendor.

**Attack Timeline:**
1. Attackers compromised Fazio Mechanical, a refrigeration contractor with access to Target's network
2. Used stolen credentials to enter Target's vendor portal
3. Moved laterally to Point-of-Sale (POS) systems
4. Installed custom malware (BlackPOS) on POS terminals
5. Malware scraped card data from RAM before encryption
6. Data exfiltrated to external FTP servers

**Kill Chain Mapping:**
- Recon → Contractor network researched
- Initial Access → Stolen vendor credentials
- Lateral Movement → From vendor network to POS systems
- Installation → BlackPOS malware on POS terminals
- Exfiltration → Card data sent to attacker servers

**Lessons Learned:**
- Third-party vendor risk is a major attack vector
- Network segmentation: POS systems should never be reachable from vendor networks
- Monitor for unusual outbound connections

**Impact:** $18.5 million settlement, CEO resigned, massive reputational damage

---

### 8.2 Equifax Data Breach (2017)

**What happened:**
Attackers exploited an **unpatched Apache Struts vulnerability** (CVE-2017-5638) to steal personal data of **147 million Americans** — nearly half the US population.

**Attack Timeline:**
1. A critical vulnerability in Apache Struts (CVE-2017-5638) was publicly disclosed in March 2017
2. Equifax failed to patch it (patch was available — they just didn't apply it)
3. Attackers exploited the vulnerability 2 months later in May 2017
4. Spent **76 days** inside Equifax's network undetected
5. Made **9,000 queries** to databases, exfiltrating names, SSNs, dates of birth, addresses, driver's license numbers, credit card numbers

**What failed:**
- **Patch management:** Known vulnerability not patched for 2 months
- **Network monitoring:** 9,000 database queries over 76 days went unnoticed
- **Encryption:** Sensitive data stored unencrypted in some databases
- **TLS inspection:** An expired SSL certificate caused their monitoring tool to be disabled for 19 months

**Lessons Learned:**
- Patch known vulnerabilities quickly, especially critical ones
- Monitor database query volumes for anomalies
- Encrypt sensitive data at rest
- Maintain SSL certificate hygiene

**Impact:** $700 million settlement (largest ever for a data breach at the time), massive public distrust

---

### 8.3 SolarWinds (SUNBURST) Attack (2020)

**What happened:**
Russian SVR intelligence (APT29 / Cozy Bear) conducted the **most sophisticated supply chain attack** ever seen, compromising 18,000+ organizations including US government agencies.

**Attack Timeline:**
1. Attackers compromised SolarWinds' software development pipeline in **late 2019**
2. Inserted backdoor code (SUNBURST) into legitimate Orion software updates
3. Trojanized update was **digitally signed** by SolarWinds — appeared completely legitimate
4. 18,000+ organizations installed the update (including Treasury, State, Homeland Security, Microsoft, Intel, FireEye)
5. After a 12–14 day dormancy period, SUNBURST connected to attackers' C2 infrastructure
6. Attackers used this access for **months**, conducting espionage undetected
7. Discovered in December 2020 — only because FireEye noticed their own red team tools being stolen

**Why this was so dangerous:**
- Attacked the **supply chain** — the update was legitimate and signed
- Affected organizations had no way to distinguish malicious update from normal one
- Bypassed all perimeter defenses because the backdoor was inside trusted software

**Lessons Learned:**
- Supply chain security is critical — vet your software vendors
- Zero-trust architecture limits blast radius even if attackers get in
- Monitor for unusual outbound connections even from trusted systems
- Software build environments need their own security controls

**Impact:** Estimated hundreds of millions in damage; major US government espionage; changed how we think about supply chain security

---

### 8.4 Colonial Pipeline Ransomware Attack (2021)

**What happened:**
The DarkSide ransomware group attacked Colonial Pipeline, causing the **largest fuel pipeline disruption in US history**, leading to panic buying and fuel shortages across the US East Coast.

**Attack Timeline:**
1. Attackers obtained a **single compromised password** for a VPN account (found in a leaked credential database)
2. The VPN account did not have MFA enabled
3. Gained access to Colonial Pipeline's IT network
4. Deployed DarkSide ransomware, encrypting billing and business systems
5. Colonial preemptively shut down the **operational technology (OT)** pipeline systems out of caution
6. 5,500 miles of pipeline supplying 45% of the East Coast's fuel was offline for **6 days**
7. Colonial paid **$4.4 million in Bitcoin** ransom within hours

**What failed:**
- **Single compromised credential** — no MFA on VPN
- **IT/OT segmentation** — business network compromise led to pipeline shutdown
- No offline backup that could restore quickly

**Recovery:**
- FBI recovered ~$2.3 million of the ransom
- DarkSide shut down shortly after (pressure from US government)

**Lessons Learned:**
- MFA is non-negotiable for remote access
- IT/OT network segregation prevents business breaches from affecting operations
- Have an OT incident response plan
- Credential monitoring — check if your credentials appear in breach databases

**Impact:** $4.4 million ransom, national fuel shortage, declared state of emergency in multiple US states, national security implications

---

### Case Study Comparison

| Incident | Initial Vector | What Failed | Key Lesson |
|---|---|---|---|
| Target (2013) | Vendor credentials | Third-party access, segmentation | Vendor risk management |
| Equifax (2017) | Unpatched vulnerability | Patch management, monitoring | Patch fast, encrypt data |
| SolarWinds (2020) | Supply chain compromise | Build pipeline security | Zero trust, supply chain |
| Colonial Pipeline (2021) | Stolen VPN password | No MFA on VPN | MFA everywhere |

---

## 9. Hands-On Lab: Map a Real Attack to MITRE ATT&CK

### Objective

Map the **Colonial Pipeline ransomware attack** to specific MITRE ATT&CK tactics and techniques.

### Instructions

**Step 1: Set up your workspace**

Open a text editor, Google Doc, or Markdown file. Create a table with these columns:
```
| ATT&CK Tactic | Tactic ID | Technique | Technique ID | Evidence from Colonial Pipeline |
```

**Step 2: Research**

Visit [https://attack.mitre.org](https://attack.mitre.org) and look up each of the following:
- How do attackers use stolen credentials for initial access?
- What technique involves using legitimate VPN/remote access?
- How do ransomware groups move through networks?
- What technique describes file encryption for ransom?

**Step 3: Fill in the table**

Use the information from this module and your research. Here is a partial answer to get you started — complete the remaining rows:

| ATT&CK Tactic | Tactic ID | Technique | Technique ID | Evidence |
|---|---|---|---|---|
| Initial Access | TA0001 | Valid Accounts: Domain Accounts | T1078.002 | Attackers used stolen VPN credentials |
| Initial Access | TA0001 | External Remote Services | T1133 | Accessed via VPN with no MFA |
| Credential Access | TA0006 | *(find this)* | *(find this)* | Credentials were found in a breach database |
| Discovery | TA0007 | *(find this)* | *(find this)* | Attackers mapped Colonial's network |
| Impact | TA0040 | Data Encrypted for Impact | T1486 | DarkSide ransomware encrypted systems |

**Step 4: Use the ATT&CK Navigator**

1. Go to [https://mitre-attack.github.io/attack-navigator/](https://mitre-attack.github.io/attack-navigator/)
2. Click **"Create New Layer"** → **"Enterprise ATT&CK"**
3. Find the techniques you identified and highlight them
4. Export your layer as a PNG or JSON

**Step 5: Write a 1-paragraph summary**

In your own words, explain: *How did the Colonial Pipeline attackers move from a single compromised password to shutting down a national fuel pipeline?*

### Expected Deliverable

A completed MITRE ATT&CK mapping table + Navigator screenshot saved as `lab_01_colonial_pipeline_attack_mapping.md` in your notes.

---

## 10. Summary & Key Takeaways

### What You Learned in Module 1

**Cybersecurity Fundamentals:**
- Cybersecurity protects systems, networks, and data from attacks
- The CIA Triad (Confidentiality, Integrity, Availability) is the foundation of all security decisions
- AAA (Authentication, Authorization, Accounting) controls who accesses what and tracks what they do
- Non-repudiation ensures users cannot deny their actions

**Threat Landscape:**
- Hackers are classified by hat color based on intent and authorization
- Threat actors range from individual script kiddies to nation-state APTs with unlimited resources
- Understanding your adversary helps you prioritize defenses appropriately

**Attack Frameworks:**
- The Cyber Kill Chain describes 7 stages of an attack — disrupting any stage stops the attack
- MITRE ATT&CK maps real-world attacker behavior to specific tactics and techniques
- These frameworks are used daily by SOC analysts, red teamers, and threat intelligence teams

**Attack Vectors & Surface:**
- Attack vectors are the methods attackers use to gain access
- Attack surface is everything that could be targeted — reducing it reduces risk
- Real-world breaches are almost always caused by basic failures: unpatched systems, weak credentials, lack of MFA, poor vendor management

**Real-World Application:**
- Target: Third-party vendor access led to 40M card thefts
- Equifax: Unpatched vulnerability led to 147M records stolen
- SolarWinds: Supply chain attack compromised 18,000+ organizations
- Colonial Pipeline: One stolen password with no MFA shut down US fuel supply

---

## 11. Quiz: Test Your Knowledge

**Instructions:** Answer without looking at the material first. Check your answers after.

1. What does the "A" in the CIA Triad stand for?

2. A DDoS attack primarily violates which principle of the CIA Triad?

3. What is the difference between Authentication and Authorization?

4. A hacker breaks into a company without permission but then emails the security team to warn them. What hat color describes this person?

5. Which threat actor type is characterized by staying inside a network for months or years undetected?

6. Name the 7 stages of the Cyber Kill Chain in order.

7. What does MITRE ATT&CK stand for?

8. How many tactics are in the MITRE ATT&CK Enterprise matrix?

9. What was the initial attack vector in the Colonial Pipeline breach?

10. What fundamental security control was missing from Colonial Pipeline's VPN that allowed the attackers in?

11. What is the difference between an attack vector and an attack surface?

12. Which company was compromised to deliver malware to 18,000+ organizations through a software update?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Availability** — Ensuring systems are accessible when needed
2. **Availability** — DDoS denies legitimate users access to systems
3. **Authentication** = verifying identity ("who are you?"); **Authorization** = determining permissions ("what can you do?")
4. **Grey Hat** — Acts without authorization but discloses findings
5. **Advanced Persistent Threat (APT)** — Nation-state or highly sophisticated groups
6. Reconnaissance → Weaponization → Delivery → Exploitation → Installation → Command & Control → Actions on Objectives
7. **Adversarial Tactics, Techniques, and Common Knowledge**
8. **14 tactics**
9. A **compromised VPN password** found in a leaked credential database
10. **Multi-Factor Authentication (MFA)** was not enabled on the VPN
11. **Attack vector** = the specific path/method used to attack; **Attack surface** = the total collection of all possible vectors
12. **SolarWinds** (the Orion software supply chain attack)

</details>

---

## 12. Resources

### Official Documentation
- [MITRE ATT&CK Enterprise Matrix](https://attack.mitre.org/matrices/enterprise/)
- [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [Lockheed Martin Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)

### Case Study Reports
- [Equifax Breach Senate Report (PDF)](https://www.warren.senate.gov/imo/media/doc/2018.09.06%20Equifax%20Report.pdf)
- [CISA Colonial Pipeline Advisory](https://www.cisa.gov/news-events/cybersecurity-advisories/aa21-131a)
- [FireEye SolarWinds Disclosure](https://www.mandiant.com/resources/blog/evasive-attacker-leverages-solarwinds-supply-chain-compromises-with-sunburst-backdoor)

### Practice Platforms
- [TryHackMe — Pre-Security Path](https://tryhackme.com/path/outline/presecurity) — Start here if you're new
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [MITRE ATT&CK Defender Training](https://mitre-engenuity.org/cybersecurity/mad/)

### Recommended Reading
- *Penetration Testing* by Georgia Weidman — Chapter 1 (overview of the field)
- *The Hacker Playbook 3* by Peter Kim — Introduction

---

## Module Progress Checklist

Before moving to Module 2, confirm you can:

- [ ] Explain the CIA Triad and give an example of a threat to each principle
- [ ] Describe the AAA framework and explain why each component matters
- [ ] Distinguish between different hacker hat colors
- [ ] Identify the five major threat actor types and their motivations
- [ ] List all 7 stages of the Cyber Kill Chain
- [ ] Navigate the MITRE ATT&CK website and find a specific technique
- [ ] Explain what happened in Target, Equifax, SolarWinds, and Colonial Pipeline breaches
- [ ] Complete the hands-on lab mapping Colonial Pipeline to ATT&CK

---

**Next Module:** [Module 2 — Cyber Laws, Ethics & Compliance →](Module_02_Cyber_Laws_Ethics_Compliance.md)

---

*Module 1 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
