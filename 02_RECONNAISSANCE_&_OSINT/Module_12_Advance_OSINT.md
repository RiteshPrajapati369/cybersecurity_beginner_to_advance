# Module 12: Advanced OSINT Tools & Automation

> **Phase 2 — Reconnaissance & OSINT**
> 🟠 Difficulty: Intermediate | ⏱ Estimated Time: 6–8 hours | 📁 Folder: `02_OSINT`

---

## Table of Contents

1. [Why Advanced OSINT Tools Matter](#1-why-advanced-osint-tools-matter)
2. [Shodan — The Search Engine for Hackers](#2-shodan--the-search-engine-for-hackers)
3. [Maltego — Visual Link Analysis](#3-maltego--visual-link-analysis)
4. [SpiderFoot — Automated OSINT Scanner](#4-spiderfoot--automated-osint-scanner)
5. [Recon-ng — Modular Reconnaissance Framework](#5-recon-ng--modular-reconnaissance-framework)
6. [TheHarvester — Email and Subdomain Harvesting](#6-theharvester--email-and-subdomain-harvesting)
7. [Censys — Internet-Wide Asset Discovery](#7-censys--internet-wide-asset-discovery)
8. [BuiltWith — Website Technology Profiling](#8-builtwith--website-technology-profiling)
9. [FOCA — Metadata Extraction](#9-foca--metadata-extraction)
10. [Hands-On Lab: Full OSINT Investigation with Advanced Tools](#10-hands-on-lab-full-osint-investigation-with-advanced-tools)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz](#12-quiz)
13. [Resources](#13-resources)

---

## 1. Why Advanced OSINT Tools Matter

Basic OSINT (Google Dorking, manual searches) gives you a starting point. **Advanced OSINT tools** automate the process, uncover hidden connections, and reveal infrastructure you didn't know existed.

### The Problem with Manual OSINT

| Issue | Impact |
|---|---|
| **Time-consuming** | Searching 10+ sources manually takes hours |
| **Missed connections** | Human brain can't visualize complex relationships |
| **Incomplete data** | You don't know what you don't know |
| **No automation** | Can't run scans at scale |

### What Advanced Tools Give You

| Benefit | Example |
|---|---|
| **Automation** | SpiderFoot runs 200+ modules with one command  |
| **Visualization** | Maltego shows relationships as interactive graphs  |
| **Infrastructure discovery** | Shodan finds devices you didn't know existed  |
| **Correlation** | Connect email → domain → IP → employee → social media |

### The OSINT Tool Stack

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ADVANCED OSINT TOOL STACK                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  DISCOVERY LAYER (Find what's out there)                    │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │   │
│  │  │ Shodan   │  │ Censys   │  │theHarvest│  │ BuiltWith│   │   │
│  │  │ Devices  │  │ Hosts    │  │ Emails   │  │ Tech     │   │   │
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              ↓                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  ANALYSIS LAYER (Connect the dots)                          │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐                  │   │
│  │  │ Maltego  │  │SpiderFoot│  │ Recon-ng │                  │   │
│  │  │ Visual   │  │ Auto     │  │ Modular  │                  │   │
│  │  │ Links    │  │ Scan     │  │ Framework│                  │   │
│  │  └──────────┘  └──────────┘  └──────────┘                  │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              ↓                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  REPORTING LAYER (Document findings)                        │   │
│  │  ┌──────────┐  ┌──────────┐                                 │   │
│  │  │ PDF      │  │ CSV/JSON │                                 │   │
│  │  │ Reports  │  │ Exports  │                                 │   │
│  │  └──────────┘  └──────────┘                                 │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Shodan — The Search Engine for Hackers

**Shodan** is a search engine that indexes internet-connected devices. While Google indexes websites, Shodan indexes servers, routers, webcams, industrial control systems, and more.
It can be used in both browsing method as https://shodan.io and terminal method by installing it and using it's API. _I recommend to try both as beginner._

### What Shodan Reveals

| Information | Example |
|---|---|
| **Open ports** | 22 (SSH), 80 (HTTP), 443 (HTTPS) |
| **Running services** | Apache 2.4.49, OpenSSH 8.2p1 |
| **Operating system** | Linux 5.4, Windows 10 |
| **SSL/TLS certificates** | Domain names, issuers |
| **Geolocation** | City, country, coordinates |
| **Vulnerabilities** | CVEs affecting the service  |

### Installation

**Shodan CLI:**
```bash
pip3 install shodan
shodan init YOUR_API_KEY
```

**Get API key:**
1. Go to https://shodan.io
2. Create an account
3. Go to Account → API Key

### Basic Shodan Commands

```bash
# Search for a specific service
shodan search apache

# Search for a specific version
shodan search "Apache 2.4.49"

# Find devices in a specific country
shodan search "country:US"

# Get detailed information about a host
shodan host 8.8.8.8

# Download results
shodan download results "apache"

# Parse downloaded results
shodan parse --fields ip_str,port,org results.json.gz
```

### Shodan Search Filters

| Filter | Purpose | Example |
|---|---|---|
| `org:` | Organization | `org:"Google"` |
| `city:` | City | `city:"New York"` |
| `country:` | Country code | `country:US` |
| `port:` | Port number | `port:22` |
| `product:` | Service product | `product:"Apache"` |
| `version:` | Version | `version:"2.4.49"` |
| `hostname:` | Hostname | `hostname:example.com` |
| `ssl:` | SSL certificate | `ssl.cert.subject.cn:example.com` |

### Example: Corporate Recon with Shodan

```bash
# Find all devices belonging to an organization
shodan search "org:ACME Corporation"

# Find login pages
shodan search "http.title:login org:ACME"

# Find open RDP (high-risk)
shodan search "port:3389 org:ACME"

# Find open SSH
shodan search "port:22 org:ACME"
```

### Example Output Analysis

```bash
shodan host 104.21.33.72
```

**Output:**
```
IP: 104.21.33.72
Organization: Cloudflare
Country: US
City: San Francisco

Services:
- Port 22 (SSH): OpenSSH 8.2p1
- Port 80 (HTTP): Apache httpd 2.4.49
- Port 443 (HTTPS): Apache httpd 2.4.49 (SSL)

Vulnerabilities:
- CVE-2021-41773: Path traversal in Apache 2.4.49
- CVE-2021-42013: RCE in Apache 2.4.49
```

**What This Tells Us:**
- The server runs Apache 2.4.49 (vulnerable to path traversal and RCE)
- The organization uses Cloudflare
- SSH is exposed (potential brute-force target)

### Shodan Web Interface

1. Go to https://shodan.io
2. Enter a search query
3. View results:
   - Map view — See geographical distribution
   - List view — Detailed host information
   - Filter by country, port, organization

---

## 3. Maltego — Visual Link Analysis

**Maltego** is a powerful data visualization tool that transforms complex information into interactive graphs. It pulls from over 40 public sources (DNS records, social media, WHOIS data) to show relationships between entities.
_It requires high specific of device because it give a full graphican connection so if you have low specific device then for now you can skip this part_.
```
#High specification like
8-12GB RAM and atleast 4 GB graphics card to run

```
### What Maltego Can Do

| Input | What It Finds |
|---|---|
| **Domain** | Subdomains, IP addresses, name servers, email addresses |
| **Email** | Social media accounts, domains owned by that email |
| **IP Address** | Hostnames, geolocation, open ports |
| **Name** | Social media profiles, news mentions, websites |
| **Company** | Employees, domains, infrastructure |

### Installation

**Download Maltego:**
```bash
# Download the .deb package
wget https://www.paterva.com/maltego/downloads/maltego.deb

# Install
sudo dpkg -i maltego.deb
sudo apt-get install -f

# Launch
maltego
```

**Get a License:**
1. Open Maltego
2. Click "Register"
3. Create an account
4. Choose "Community Edition" (free)
5. Get your license key via email

### Basic Maltego Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│  MALTEGO INVESTIGATION WORKFLOW                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. CREATE A NEW GRAPH                                              │
│     File → New → Blank Graph                                       │
│                                                                     │
│  2. ADD AN ENTITY                                                   │
│     Drag "Domain" entity onto the canvas                           │
│     Type: example.com                                              │
│                                                                     │
│  3. RUN A TRANSFORM                                                 │
│     Right-click domain → Run Transform                             │
│     → "To IP Address"                                              │
│     → "To DNS Name" (subdomains)                                   │
│     → "To Email Address"                                           │
│                                                                     │
│  4. EXPAND THE GRAPH                                                │
│     Click "Run All Transforms"                                     │
│     Maltego automatically discovers:                              │
│     - Subdomains, IPs, name servers                                │
│     - Email addresses, social profiles                            │
│                                                                     │
│  5. ANALYZE THE GRAPH                                               │
│     Look for:                                                      │
│     - Unexpected relationships                                      │
│     - Exposed infrastructure                                       │
│     - Employee information                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Example: Investigation of a Target Domain

**Starting point:** `example.com`

**Transforms to run:**
1. `Domain → IP Address` → Finds hosting IP
2. `Domain → DNS Name` → Finds subdomains
3. `IP Address → Geolocation` → Finds server location
4. `IP Address → WHOIS` → Finds organization
5. `Domain → Email Address` → Finds admin emails

**What the graph shows:**
- **Domain:** example.com
- **Subdomains:** www, mail, dev, api, internal
- **IP:** 192.168.1.1 (shared with 3 other domains)
- **Hosting:** AWS (US-East)
- **Email:** admin@example.com, webmaster@example.com
- **Relationships:** admin@example.com appears in 12 other domains
- **Social:** LinkedIn profile of the admin user

---

## 4. SpiderFoot — Automated OSINT Scanner

**SpiderFoot** is an automated OSINT tool that queries over 200 data sources to collect intelligence about a target .

### What SpiderFoot Does

| Module Category | Example Sources |
|---|---|
| **Domain** | Whois, DNS, SecurityTrails |
| **IP** | Shodan, Censys, GeoIP |
| **Email** | Hunter.io, HaveIBeenPwned |
| **Social** | LinkedIn, Twitter, GitHub |
| **Leaks** | Pastebin, Dark Web sites |

### Installation

```bash
# Clone the repository
git clone https://github.com/smicallef/spiderfoot.git
cd spiderfoot

# Install dependencies
pip3 install -r requirements.txt

# Run SpiderFoot
python3 sf.py
```

**Access Web Interface:**
1. Open browser
2. Go to `http://127.0.0.1:5001`
3. Create a new scan

### Scanning with SpiderFoot

**Web Interface:**
1. Click "New Scan"
2. Enter target (domain, IP, or email)
3. Select scan type:
   - **Footprint:** Passive scanning (no direct interaction)
   - **Discover:** More aggressive (may touch target)
4. Click "Run Scan"

**Command Line:**
```bash
# Run a scan on example.com
spiderfoot -s example.com -o scan_report.html

# Generate a JSON report
spiderfoot -s example.com -o scan_report.json
```

### Example Scan Results

**Target:** `example.com`

**What SpiderFoot Found:**

| Category | Finding |
|---|---|
| **Subdomains** | www.example.com, mail.example.com, dev.example.com |
| **IP Addresses** | 192.168.1.1, 192.168.1.2 |
| **Emails** | admin@example.com, support@example.com |
| **Services** | Apache 2.4.49, OpenSSH 8.2p1 |
| **Technologies** | WordPress 6.2, PHP 7.4, MySQL |
| **Linked Profiles** | LinkedIn page, GitHub repositories |
| **Leaks** | credentials found in a 2021 breach |

**Correlation:** The email `admin@example.com` was found in 3 different data breaches and is associated with 5 other domains.

### When to Use SpiderFoot

| Scenario | Why |
|---|---|
| **Initial reconnaissance** | Get a broad overview quickly |
| **Automated monitoring** | Run periodic scans for new exposures |
| **Threat hunting** | Identify malicious infrastructure |
| **Compliance checks** | Find exposed data |

---

## 5. Recon-ng — Modular Reconnaissance Framework

**Recon-ng** is a full-featured web reconnaissance framework with over 80 modules. It's similar to Metasploit but designed for OSINT .

### Why Recon-ng

| Feature | Benefit |
|---|---|
| **Modular** | Load only what you need |
| **Workspaces** | Organize different investigations |
| **API Integration** | Shodan, Censys, Hunter.io |
| **Reporting** | Export to CSV, JSON, PDF |
| **Database** | Stores results for later analysis |

### Installation

```bash
# Clone the repository
git clone https://github.com/lanmaster53/recon-ng.git
cd recon-ng

# Install dependencies
pip3 install -r REQUIREMENTS

# Run Recon-ng
python3 recon-ng
```

### Basic Recon-ng Commands

```bash
# Show available modules
marketplace list

# Install a module
marketplace install recon/domains-hosts/hackertarget

# Load a module
modules load recon/domains-hosts/hackertarget

# Set options
options set SOURCE example.com

# Run the module
run

# Show results
show hosts
show contacts
```

### Example: Domain Reconnaissance

```bash
# Step 1: Launch Recon-ng
recon-ng

# Step 2: Load a domain-to-host module
marketplace install recon/domains-hosts/hackertarget
modules load recon/domains-hosts/hackertarget

# Step 3: Set target
options set SOURCE example.com

# Step 4: Run
run

# Step 5: See results
show hosts

# Output:
# IP Address: 192.168.1.1
# Hostname: example.com
# Type: Web Server
```

### Example: Email Harvesting

```bash
# Load email harvesting module
marketplace install recon/domains-contacts/hunter_io
modules load recon/domains-contacts/hunter_io

# Set API key (get from Hunter.io)
options set API_KEY YOUR_HUNTER_API_KEY
options set SOURCE example.com

# Run
run

# View results
show contacts

# Output:
# Email: admin@example.com
# Name: John Doe
# Position: Administrator
# Source: LinkedIn
```

### Recon-ng Workspaces

```bash
# Create a workspace
workspaces create acme_corp

# List workspaces
workspaces list

# Switch workspace
workspaces select acme_corp

# All data is stored in the workspace database
# Run multiple modules and all results are saved together
```

### Pre-Built Recon-ng Workflows

Instead of running modules one by one, create a workflow:

```yaml
# workflow.yml
- module: recon/domains-hosts/hackertarget
  options:
    SOURCE: example.com
- module: recon/domains-hosts/bing_domain_web
  options:
    SOURCE: example.com
- module: recon/domains-contacts/hunter_io
  options:
    SOURCE: example.com
    API_KEY: YOUR_KEY
```

**Run the workflow:**
```bash
recon-ng -w workflow.yml -r
```

---

## 6. TheHarvester — Email and Subdomain Harvesting

**TheHarvester** is a lightweight Python tool that gathers emails, subdomains, hosts, and employee names from public sources .

### What TheHarvester Finds

| Source | What It Finds |
|---|---|
| **Google** | Emails, subdomains |
| **Bing** | Emails, subdomains |
| **LinkedIn** | Employee names |
| **Shodan** | Hosts, IP addresses |
| **DNSDumpster** | Subdomains |
| **PGP Key Servers** | Email addresses |
| **Hunter.io** | Verified emails  |

### Installation

```bash
# Kali Linux (pre-installed)
theharvester -h

# Ubuntu/Debian
sudo apt install theharvester -y

# From GitHub
git clone https://github.com/laramies/theHarvester
cd theHarvester
pip3 install -r requirements.txt
```

### Basic TheHarvester Usage

```bash
# Basic search using Google
theharvester -d example.com -b google

# Search using multiple sources
theharvester -d example.com -b google,linkedin,duckduckgo

# Limit results
theharvester -d example.com -b google -l 500

# Save results to file
theharvester -d example.com -b google -f results.html

# Use virtual host discovery
theharvester -d example.com -b google -v
```

### Example Output

```bash
theharvester -d example.com -b google,linkedin
```

**Output:**
```
[*] Searching Google...
[*] Found emails:
- admin@example.com
- support@example.com
- john.doe@example.com
- jane.smith@example.com

[*] Searching LinkedIn...
[*] Found employee names:
- John Doe (Software Engineer)
- Jane Smith (Marketing Manager)
- Bob Wilson (CTO)

[*] Subdomains found:
- www.example.com
- mail.example.com
- dev.example.com
- api.example.com
- staging.example.com

[*] Total emails found: 12
[*] Total subdomains found: 8
```

### Advanced TheHarvester Techniques

**Using Shodan for host discovery:**
```bash
theharvester -d example.com -b shodan
```

**Using DNSDumpster for subdomains:**
```bash
theharvester -d example.com -b dnsdumpster
```

**Combined search with all sources:**
```bash
theharvester -d example.com -b all
```

---

## 7. Censys — Internet-Wide Asset Discovery

**Censys** is an internet-wide scanning platform similar to Shodan. It provides detailed information about hosts, certificates, and networks.

### Censys vs Shodan

| Feature | Censys | Shodan |
|---|---|---|
| **Device discovery** | ✅ Yes | ✅ Yes |
| **SSL certificates** | ✅ Very detailed | ✅ Yes |
| **Protocol analysis** | ✅ Deep | ✅ Yes |
| **Vulnerability data** | ✅ Yes | ✅ Yes |
| **API access** | ✅ Yes | ✅ Yes |
| **Free tier** | Limited | Limited |

### Installation

```bash
# Install Censys Python library
pip3 install censys

# Set API keys (get from https://censys.io)
censys config
```

### Basic Censys Usage

```bash
# Search for hosts
censys search "example.com"

# Get certificate details
censys certificates --q "example.com"

# Get host information
censys hosts --ip 8.8.8.8
```

### Example: Certificate Transparency Discovery

```bash
censys certificates --q "parsed.issuer.common_name: Example Inc"
```

**What this finds:** All SSL/TLS certificates issued to "Example Inc" — revealing all domains owned by the organization.

**Why this matters:** Many organizations have forgotten subdomains that still have valid SSL certificates. These are often vulnerable.

---

## 8. BuiltWith — Website Technology Profiling

**BuiltWith** is a web technology profiler that identifies what technology stack a website uses.

### What BuiltWith Reveals

| Category | Examples |
|---|---|
| **Content Management System** | WordPress, Joomla, Drupal |
| **Programming Language** | PHP, Python, Ruby, Node.js |
| **Web Server** | Apache, Nginx, IIS |
| **Frameworks** | React, Angular, Vue.js |
| **Hosting** | AWS, Azure, Google Cloud |
| **Analytics** | Google Analytics, Matomo |
| **CDN** | Cloudflare, Akamai |

### Using BuiltWith

**Web Interface:**
1. Go to https://builtwith.com
2. Enter a domain
3. Review the technology profile

**What you can learn:**
- A website using WordPress 5.7 is vulnerable to CVE-2021-29447
- A website using PHP 7.2 is no longer supported (security risks)
- A website using Apache 2.4.49 is vulnerable to path traversal
- The hosting provider helps identify other domains on the same server

**Example Profile:**
```
example.com
├── Web Server: Apache 2.4.49
├── CMS: WordPress 6.2
├── Framework: React 18
├── Hosting: AWS EC2 (US-East)
├── CDN: Cloudflare
├── Email: Google Workspace
├── DNS: AWS Route53
├── Analytics: Google Analytics
└── SSL: Let's Encrypt
```

### Using Wappalyzer (Browser Extension)

**Wappalyzer** is a browser extension that identifies technologies in real-time.

1. Install Wappalyzer extension (Chrome/Firefox)
2. Visit any website
3. Click the extension icon
4. View the technology stack

**Why this matters for security:** Older or outdated technologies often have known vulnerabilities. Knowing the tech stack helps prioritize which vulnerabilities to check.

---

## 9. FOCA — Metadata Extraction

**FOCA (Fingerprinting Organizations with Collected Archives)** extracts metadata from public documents .

### What FOCA Finds

| File Type | Metadata Extracted |
|---|---|
| **Office Documents** | Author, company, last saved by, edit time |
| **PDFs** | Author, software, creator, hidden text |
| **Images** | Camera model, GPS coordinates, date |
| **HTML/XML** | Author, generator, comments |

### How Attackers Use FOCA

1. Search for documents on the target domain
2. Extract metadata
3. Find internal information:
   - **Author name:** Employee identity
   - **Company:** Organization structure
   - **Software:** Internal tooling
   - **File path:** Internal network structure
   - **GPS coordinates:** Physical location

### Example: FOCA in Action

**Step 1:** Search for PDFs on `example.com`
**Step 2:** Extract metadata from `internal_report.pdf`

**Results:**
```
Author: John Doe
Last Modified By: Jane Smith
Company: ACME Corporation
Software: Microsoft Office 2016
File Path: \\server\shared\reports\internal_report.pdf
Hidden Comments: "The new server will be at 10.10.1.50"
```

**How this helps an attacker:**
- **Author name:** Potential target for phishing
- **File path:** Internal server names and structure
- **IP address:** Internal network range
- **Software:** Potential vulnerabilities in Office 2016

### Installing FOCA

**Windows:**
1. Download from https://www.elevenpaths.com/tecnologia/foca/
2. Run the installer

**Linux (alternative):**
```bash
# Use Metagoofil (Linux alternative)
sudo apt install metagoofil -y

# Example usage
metagoofil -d example.com -t pdf,doc,xls -l 20 -n 5 -o /tmp/
```

---

## 10. Hands-On Lab: Full OSINT Investigation with Advanced Tools

### Lab Overview

In this lab you will:
1. Use Shodan to discover an organization's internet-facing assets
2. Use Maltego to visualize relationships
3. Use TheHarvester to harvest emails and subdomains
4. Use Recon-ng to automate reconnaissance
5. Use SpiderFoot for automated scanning
6. Use BuiltWith for technology profiling
7. Produce a comprehensive OSINT report

### Target Selection

Use `example.com` (a fictional company) or a target you have permission to investigate.

### Part 1: Shodan Discovery

**Step 1: Search for the organization**

```bash
shodan search "org:ACME Corporation"
```

**Step 2: Find login pages**

```bash
shodan search "http.title:login org:ACME Corporation"
```

**Step 3: Find open ports**

```bash
shodan search "port:22,3389,445 org:ACME Corporation"
```

**Step 4: Document findings**

Create a table:
```
| IP Address | Port | Service | Version | Risk |
|---|---|---|---|---|
| 192.168.1.1 | 22 | SSH | OpenSSH 8.2p1 | Medium |
| 192.168.1.2 | 80 | HTTP | Apache 2.4.49 | High |
| 192.168.1.3 | 3389 | RDP | Windows 10 | High |
```

### Part 2: Maltego Link Analysis

**Step 1: Create a new graph**

1. Open Maltego
2. File → New → Blank Graph

**Step 2: Add the domain**

1. Drag "Domain" entity onto the canvas
2. Type: `example.com`

**Step 3: Run transforms**

Right-click the domain → Run Transform:
- "To IP Address"
- "To DNS Name"
- "To Email Address"
- "To WHOIS Information"

**Step 4: Expand to IPs**

1. Right-click the IP address
2. Run "To Geolocation"
3. Run "To Reverse DNS"

**Step 5: Document findings**

Screenshot the graph and list discovered:
- Subdomains
- IP addresses
- Email addresses
- Relationships

### Part 3: TheHarvester — Email Harvesting

```bash
# Run TheHarvester
theharvester -d example.com -b google,linkedin,duckduckgo -l 500 -f results.html
```

**Document findings:**
```
| Email | Source | Verified |
|---|---|---|
| admin@example.com | Google | Yes |
| support@example.com | Google | Yes |
| john.doe@example.com | LinkedIn | Yes |
```

### Part 4: Recon-ng — Automated Recon

```bash
# Step 1: Launch Recon-ng
recon-ng

# Step 2: Install required modules
marketplace install recon/domains-hosts/hackertarget
marketplace install recon/domains-hosts/bing_domain_web
marketplace install recon/domains-contacts/hunter_io

# Step 3: Create a workspace
workspaces create acme_corp

# Step 4: Run domain discovery
modules load recon/domains-hosts/hackertarget
options set SOURCE example.com
run
show hosts

# Step 5: Run email discovery
modules load recon/domains-contacts/hunter_io
options set API_KEY YOUR_KEY
options set SOURCE example.com
run
show contacts

# Step 6: Run subdomain discovery
modules load recon/domains-hosts/bing_domain_web
options set SOURCE example.com
run
show hosts
```

### Part 5: SpiderFoot — Automated Scanning

**Step 1: Start SpiderFoot**

```bash
cd spiderfoot
python3 sf.py
```

**Step 2: Access web interface**

1. Open browser → `http://127.0.0.1:5001`
2. Click "New Scan"
3. Enter target: `example.com`
4. Select "Footprint" scan type
5. Click "Run Scan"

**Step 3: Review results**

Wait for scan to complete, then review:
- Subdomains discovered
- IP addresses
- Email addresses
- Leaked credentials
- Social media profiles

**Step 4: Export report**

Click "Export" → Select format (HTML, CSV, JSON)

### Part 6: BuiltWith — Technology Profiling

**Step 1: Visit BuiltWith**

Go to https://builtwith.com

**Step 2: Enter the domain**

Enter `example.com`

**Step 3: Document findings**

Create a technology profile:
```
| Technology | Version | Risk |
|---|---|---|
| CMS | WordPress 6.2 | Check CVEs |
| PHP | 7.4 | Unsupported |
| Web Server | Apache 2.4.49 | High (CVE-2021-41773) |
| Hosting | AWS EC2 | — |
```

### Part 7: Compile OSINT Report

Create `lab_12_osint_report.md`:

```markdown
# OSINT Investigation Report — [Target Name]

## 1. Executive Summary
- Target: [domain/company]
- Tools Used: Shodan, Maltego, TheHarvester, Recon-ng, SpiderFoot, BuiltWith
- Investigation Date: [date]
- Key Findings: [summary]

## 2. Infrastructure Discovery
| IP | Port | Service | Version | Risk Level |
|----|------|---------|---------|------------|
| [IP] | [port] | [service] | [version] | [High/Med/Low] |

## 3. Email Harvesting
| Email | Source | Verified |
|-------|--------|----------|
| [email] | [source] | [Yes/No] |

## 4. Subdomain Discovery
| Subdomain | IP | Notes |
|-----------|----|-------|
| [subdomain] | [IP] | [notes] |

## 5. Technology Profile
| Technology | Version | Risk |
|------------|---------|------|
| [tech] | [version] | [risk] |

## 6. Relationships (Maltego)
[Describe relationships found]
- Domain → IP → Geolocation
- Domain → Subdomains
- IP → Hosting Provider
- Domain → Email Addresses → Social Media

## 7. Vulnerabilities Identified
| CVE | Description | Affected System | Severity |
|-----|-------------|-----------------|----------|
| CVE-2021-41773 | Path Traversal | Apache 2.4.49 | High |

## 8. Recommendations
1. Update Apache to 2.4.50+
2. Restrict SSH access to authorized IPs
3. Remove exposed admin panels

## 9. Screenshots
[Screenshots of Maltego graph, Shodan search results, etc.]
```

---

## 11. Summary & Key Takeaways

### Tools Covered

| Tool | Purpose | Command |
|---|---|---|
| **Shodan** | Internet-connected device search | `shodan search apache` |
| **Maltego** | Visual link analysis | `maltego` |
| **SpiderFoot** | Automated OSINT scanner | `python3 sf.py` |
| **Recon-ng** | Modular reconnaissance framework | `recon-ng` |
| **TheHarvester** | Email and subdomain harvesting | `theharvester -d example.com` |
| **Censys** | Internet-wide asset discovery | `censys search example.com` |
| **BuiltWith** | Website technology profiling | Web interface |
| **FOCA** | Metadata extraction | Windows app |

### When to Use Each Tool

| Tool | Best For |
|---|---|
| **Shodan** | Finding exposed infrastructure and devices |
| **Maltego** | Visualizing relationships between entities |
| **SpiderFoot** | Broad, automated reconnaissance |
| **Recon-ng** | Modular, database-driven investigations |
| **TheHarvester** | Quick email and subdomain discovery |
| **Censys** | SSL certificate and host analysis |
| **BuiltWith** | Technology stack identification |
| **FOCA** | Metadata extraction from documents |

### Key Takeaways

1. **Automation is essential** — Tools like SpiderFoot and Recon-ng save hours of manual work 

2. **Correlation is powerful** — Maltego connects dots that manual OSINT misses 

3. **Infrastructure is exposed** — Shodan reveals devices you didn't know existed 

4. **Metadata leaks information** — FOCA extracts employee names, internal paths, and more 

5. **Combine tools for complete picture** — No single tool does everything

---

## 12. Quiz

1. What is Shodan and what does it index?

2. What is Maltego used for?

3. What does theHarvester harvest and from what sources?

4. How does Recon-ng differ from theHarvester?

5. What does SpiderFoot do?

6. What is Censys and how is it different from Shodan?

7. What information can FOCA extract from documents?

8. What does BuiltWith reveal about a website?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Shodan** is a search engine that indexes internet-connected devices (servers, routers, webcams, IoT devices) and their exposed services .

2. **Maltego** is a tool for visual link analysis and data visualization. It shows relationships between entities like domains, IPs, emails, and social media profiles .

3. **TheHarvester** harvests emails, subdomains, hosts, and employee names from public sources like Google, LinkedIn, Bing, and PGP key servers .

4. **Recon-ng** is a modular reconnaissance framework with over 80 modules, a database, workspaces, and reporting capabilities. TheHarvester is a simpler, single-purpose tool .

5. **SpiderFoot** is an automated OSINT scanner that queries over 200 data sources to collect intelligence about a target .

6. **Censys** is similar to Shodan but provides deeper SSL certificate analysis and protocol detail. It's another internet-wide scanning platform .

7. **FOCA** extracts metadata from public documents (author names, company names, software used, file paths, GPS coordinates, hidden comments) .

8. **BuiltWith** reveals the technology stack of a website (CMS, web server, programming language, hosting provider, frameworks, analytics) .

</details>

---

## 13. Resources

### Official Websites
- [Shodan](https://shodan.io)
- [Maltego](https://www.maltego.com)
- [SpiderFoot](https://www.spiderfoot.net)
- [Recon-ng](https://github.com/lanmaster53/recon-ng)
- [TheHarvester](https://github.com/laramies/theHarvester)
- [Censys](https://censys.io)
- [BuiltWith](https://builtwith.com)

### GitHub Repositories
- [Recon-ng](https://github.com/lanmaster53/recon-ng)
- [TheHarvester](https://github.com/laramies/theHarvester)
- [SpiderFoot](https://github.com/smicallef/spiderfoot)
- [OSINT Projects for Beginners](https://github.com/0xrajneesh/OSINT-Projects-for-Beginners) 

### Practice
- [TryHackMe OSINT Room](https://tryhackme.com/room/osint)
- [Shodan Tutorial](https://www.shodan.io/tutorial)
- [Maltego Community Edition](https://www.maltego.com/downloads/)

---

**Previous Module:** [Module 11 — OSINT Part 2 →](Module_11_OSINT_Part_2.md)

**Next Module:** [Module 13 — Anonymity, Privacy & operational Security](Module_13_Anonymity_Privacy_&_Operational_Security.md)

---

*Module 12 | Phase 2: Reconnaissance & OSINT | cybersecurity_beginner_to_advance*
