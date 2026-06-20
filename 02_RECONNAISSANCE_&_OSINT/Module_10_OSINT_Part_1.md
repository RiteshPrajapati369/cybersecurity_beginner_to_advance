# Module 10: OSINT (Open Source Intelligence) - Part 1

> **Phase 2 — Reconnaissance & OSINT**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 4–5 hours | 📁 Folder: `02_OSINT`

---

## Table of Contents

1. [What is OSINT?](#1-what-is-osint)
2. [OSINT Methodology](#2-osint-methodology)
3. [Passive vs Active OSINT](#3-passive-vs-active-osint)
4. [Legal Boundaries & Ethics](#4-legal-boundaries--ethics)
5. [OSINT Framework](#5-osint-framework)
6. [Google Dorking Basics](#6-google-dorking-basics)
7. [Google Hacking Database (GHDB)](#7-google-hacking-database-ghdb)
8. [Reverse Image Search](#8-reverse-image-search)
9. [Metadata Analysis](#9-metadata-analysis)
10. [Hands-On Lab: Your First OSINT Investigation](#10-hands-on-lab-your-first-osint-investigation)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz](#12-quiz)
13. [Resources](#13-resources)

---

## 1. What is OSINT?

**OSINT (Open Source Intelligence)** is the practice of collecting and analyzing information from **publicly available sources**.

Key phrase: **publicly available**. OSINT does not involve hacking, breaking into systems, or accessing private data. It uses information anyone can access — if they know where to look.

**Examples of OSINT sources:**
- Google search results
- Social media profiles (LinkedIn, Twitter, Facebook, Instagram)
- Company websites and job postings
- Government records (property, business registrations)
- GitHub repositories
- Public documents (PDFs, Word files, spreadsheets)
- Archived website versions (Wayback Machine)
- DNS records (whois, dig)

**Why OSINT matters in cybersecurity:**

| Use Case | What OSINT Reveals |
|---|---|
| **Red Team / Pentesting** | Employee emails, technology stack, exposed documents |
| **Blue Team / Defense** | What attackers can learn about your organization |
| **Threat Intelligence** | Attacker infrastructure, leaked credentials |
| **Social Engineering** | Personal details about targets |
| **Physical Security** | Building layouts, employee badges in photos |

>  **OSINT is often the first step in any penetration test.** Before running a single tool, an attacker researches the target for hours or days using only Google and social media.

---

## 2. OSINT Methodology

Professional OSINT investigations follow a structured methodology. Random searching is inefficient.

### The 5-Step OSINT Process

```
Step 1: Define Objectives
        ↓
Step 2: Collect Sources
        ↓
Step 3: Gather Data
        ↓
Step 4: Analyze & Correlate
        ↓
Step 5: Document Findings
```

**Step 1: Define Objectives**

Before searching, ask: What am I looking for?

| Objective | Example |
|---|---|
| Email addresses | Find all @company.com emails |
| Technology stack | What web server, framework, cloud provider? |
| Employee information | Names, roles, locations |
| Exposed documents | PDFs, spreadsheets on public servers |
| Credentials | Leaked passwords in breach databases |

**Step 2: Collect Sources**

Identify which sources might contain the information. Different objectives require different sources.

**Step 3: Gather Data**

Systematically search each source. Document what you find.

**Step 4: Analyze & Correlate**

Connect pieces of information. Example: A LinkedIn profile shows "Works at Company X" + a GitHub commit shows "user@companyx.com" + a whois record shows the same email = confirmed employee email.

**Step 5: Document Findings**

Record everything you found, where you found it, and when. This creates an audit trail for legal and reporting purposes.

---

## 3. Passive vs Active OSINT

This is a critical distinction for legal and ethical reasons.

| | **Passive OSINT** | **Active OSINT** |
|---|---|---|
| **Definition** | Collecting data without interacting with the target | Interacting with the target's systems |
| **Examples** | Google search, LinkedIn, whois, archive.org | Port scanning, DNS zone transfer, website crawling |
| **Leaves traces?** | No (target cannot know you searched) | Yes (logs show your IP address) |
| **Legal risk** | Very low (public information) | Higher (may violate terms of service) |
| **When to use** | Initial reconnaissance | After passive OSINT is exhausted |

**Passive OSINT (always do first):**

```bash
# Examples of passive OSINT:
- Google search: site:company.com filetype:pdf
- LinkedIn: "Job Title" at "Company Name"
- whois: whois example.com
- DNS: dig example.com
- Wayback Machine: archive.org/web/
- GitHub: search for company name in code
```

**Active OSINT (proceed with caution):**

```bash
# Examples of active OSINT:
- Nmap scan against target IPs
- DNS zone transfer (axfr)
- Web crawling with custom user-agent
- Subdomain enumeration with tools like gobuster
```

> ⚠️ **Rule of thumb:** Start with passive OSINT only. If you move to active techniques, ensure you have proper authorization.

---

## 4. Legal Boundaries & Ethics

### What is Legal vs Illegal?

| Activity | Legal Status | Notes |
|---|---|---|
| Searching Google for company information |  Legal | Public information |
| Viewing public social media profiles |  Legal | Must be public, not "friends only" |
| Checking whois records |  Legal | Public database |
| Accessing archived websites |  Legal | Public archive |
| Downloading public PDFs from company website |  Legal | Publicly accessible |
| Scanning a company's IP addresses without permission |  Gray area | May violate CFAA or terms of service |
| Attempting to access password-protected areas |  Illegal | Even if you guess correctly |
| Social engineering employees for information |  Illegal | Fraud, pretexting |

### The Ethical OSINT Checklist

Before any OSINT activity, confirm:

- [ ] The information is **publicly available** (no passwords, no bypassing access controls)
- [ ] You are not **violating terms of service** (some websites prohibit automated scraping)
- [ ] You have **authorization** if working for a client (scope of work)
- [ ] You will **not use the information for harm** (doxxing, harassment, stalking)
- [ ] You will **respect privacy** (stop if you encounter private information)

### The "OSINT for Good" Principle

Just because information is public doesn't mean you should weaponize it.

**Ethical use:** Reporting exposed documents to a company so they can fix it.  
**Unethical use:** Publishing employee personal information online (doxxing).

**Ethical use:** Finding your own leaked credentials to change passwords.  
**Unethical use:** Using found credentials to access someone's account.

---

## 5. OSINT Framework

**OSINT Framework** (osintframework.com) is a web-based directory of OSINT tools organized by information type.

**How to use it:**

1. Go to https://osintframework.com
2. Browse by category (Email, Username, Domain, Social Media, etc.)
3. Each category expands to show tools
4. Click any tool to go directly to it

**Example categories:**

| Category | What You Can Find |
|---|---|
| Email Address | Verify email, find associated accounts |
| Username | Find accounts with same username across platforms |
| Phone Number | Carrier lookup, location (basic) |
| Domain Name | Whois, DNS history, subdomains |
| IP Address | Geolocation, reputation, hosting provider |
| Social Media | Profile information, posts, connections |
| Documents | Metadata, hidden data in PDFs/Office files |

> **Bookmark OSINT Framework.** It is the single most useful OSINT resource for beginners.

---

## 6. Google Dorking Basics

**Google Dorking (Google Hacking)** is using advanced Google search operators to find specific information that isn't easily found with normal searches.

### Essential Google Dork Operators

| Operator | Function | Example |
|---|---|---|
| `site:` | Limit to specific website | `site:company.com` |
| `filetype:` | Specific file type | `filetype:pdf` |
| `intitle:` | Word in page title | `intitle:"confidential"` |
| `inurl:` | Word in URL | `inurl:admin` |
| `ext:` | File extension (same as filetype) | `ext:xls` |
| `"exact phrase"` | Exact match | `"Social Security Number"` |
| `-` | Exclude term | `site:company.com -marketing` |
| `|` | OR operator | `"password" | "passphrase"` |
| `*` | Wildcard | `"SSN: * *-*` |

### Basic Dork Examples

```bash
# Find login pages on a specific site
site:company.com intitle:login

# Find Excel files containing passwords
filetype:xls password

# Find open directories (often contain sensitive files)
intitle:index.of

# Find exposed Git repositories
intitle:index.of .git

# Find PHP info pages (reveal server configuration)
intitle:"phpinfo()" "PHP Version"

# Find WordPress admin panels
inurl:wp-admin site:company.com

# Find SSH private keys exposed
filetype:pem OR filetype:key "BEGIN RSA PRIVATE KEY"
```

### Combining Operators

```bash
# Find PDFs on company.com that contain "confidential"
site:company.com filetype:pdf "confidential"

# Find login pages but exclude marketing subdomain
site:company.com intitle:login -inurl:marketing

# Find spreadsheets with passwords (either xls or xlsx)
site:company.com (filetype:xls | filetype:xlsx) password
```

---

## 7. Google Hacking Database (GHDB)

The **Google Hacking Database (GHDB)** is a collection of pre-written Google dorks maintained by Offensive Security and Exploit-DB.

**URL:** https://www.exploit-db.com/google-hacking-database

### GHDB Categories

| Category | Example Dork |
|---|---|
| Footholds | `filetype:sql "insert into" password` |
| Files containing passwords | `ext:pwd "User ID"` |
| Sensitive directories | `intitle:index.of "backup"` |
| Vulnerable files | `"phpinfo()" "PHP Version"` |
| Error messages | `"Warning: mysql_connect()"` |
| Network information | `intitle:"Network Camera"` |

### Using GHDB

1. Go to https://www.exploit-db.com/google-hacking-database
2. Search by category or keyword
3. Copy a dork
4. Paste into Google
5. Review results (ethically — don't access systems you don't own)

> ⚠️ **Warning:** Some GHDB entries find genuinely sensitive information (open cameras, exposed databases). Use this knowledge to help organizations fix exposures, not to exploit them.

---

## 8. Reverse Image Search

**Reverse image search** takes an image as input and finds where else that image appears online.

### Use Cases

| Use Case | How It Helps |
|---|---|
| **Catfishing detection** | Find if a profile picture appears under different names |
| **Source verification** | Find original source of an image |
| **Location tracking** | Find where a photo was taken (landmarks, signs) |
| **Person identification** | Find other photos of the same person |

### Tools

| Tool | URL | Best For |
|---|---|---|
| Google Images | images.google.com | General search |
| TinEye | tineye.com | Older images, first appearance |
| Yandex | yandex.com/images | European/Asian sources, faces |
| Bing Visual Search | bing.com/visualsearch | Similar images |

### How to Use

**Method 1: Upload an image**
1. Go to images.google.com
2. Click the camera icon
3. Upload image or paste image URL
4. Review results

**Method 2: Search by URL**
1. Right-click any image on a website
2. Copy image address
3. Paste into reverse image search tool

### Example Workflow

1. Find a LinkedIn profile with a profile photo
2. Download the photo
3. Run through Google Reverse Image Search
4. If the same photo appears on Twitter, Facebook, or a dating site — you've connected multiple accounts to the same person

---

## 9. Metadata Analysis

**Metadata** is "data about data." Every file contains hidden information beyond what you see.

### Types of Metadata

| File Type | Metadata Examples |
|---|---|
| **Images (JPEG, PNG)** | Camera model, GPS coordinates, date taken, software used |
| **PDFs** | Author, company, creation software, hidden text |
| **Office docs (Word, Excel)** | Author name, last saved by, edit time, hidden comments |
| **Audio/Video** | Recording device, location, creation date |

### Why Metadata Matters

A company publishes a PDF on their website. The metadata reveals:
- The author's full name (Jane Doe)
- The company's internal file path (`C:\Users\jdoe\SecretProject\`)
- The software used (unpatched version with known vulnerabilities)
- Hidden tracked changes showing internal review comments

### Metadata Extraction Tools

**Command line (Linux/macOS):**

```bash
# ExifTool (most comprehensive)
exiftool image.jpg

# Pdfinfo (PDF metadata)
pdfinfo document.pdf

# Strings (extract readable text from any file)
strings suspicious.exe | head -50
```

**GUI Tools:**

| Tool | Platform | Use |
|---|---|---|
| ExifTool GUI | Windows | Photo metadata |
| Metadata++ | Windows | Multiple file types |
| FOCA | Windows | Advanced document analysis |
| MAT2 | Linux | Metadata anonymization |

**Online Tools (use with caution — don't upload sensitive files):**

- metapicz.com (images)
- pdf-metadata.com (PDFs)
- aperisolve.com (steganography + metadata)

### Removing Metadata

Before sharing files publicly, remove metadata.

```bash
# ExifTool: Remove all metadata
exiftool -all= image.jpg

# Linux: mat2 (Metadata Anonymisation Toolkit)
mat2 document.pdf

# Windows: Right-click file → Properties → Details → Remove Properties
```

---

## 10. Hands-On Lab: Your First OSINT Investigation

### Objective

Conduct a passive OSINT investigation on a fictional target. You will not contact the target or access any non-public information.

### Target Information (Fictional)

**Company:** `TechVault Inc.`
**Domain:** `techvault.example` (use this domain for searching — it's a test domain)

### Instructions

**Part 1: Define Objectives**

Write down 3 things you want to find:
- Example: Email addresses of employees
- Example: Technology stack used by TechVault
- Example: Any public documents containing sensitive information

**Part 2: Google Dorking**

Perform these searches and record what you find:

```bash
# 1. Find all pages on techvault.example
site:techvault.example

# 2. Find PDFs on techvault.example
site:techvault.example filetype:pdf

# 3. Find login pages
site:techvault.example intitle:login

# 4. Find possible admin panels
site:techvault.example inurl:admin

# 5. Find exposed documents with "confidential"
site:techvault.example "confidential"
```

**Part 3: Reverse Image Search**

1. Go to Google Images
2. Upload any image (a photo you took, or download a stock photo)
3. See what results appear
4. Try searching by image URL from a public website

**Part 4: Metadata Analysis**

1. Download a PDF from any public website (your university, local government, etc.)
2. Run metadata extraction:
   ```bash
   exiftool downloaded.pdf
   ```
   Or use an online tool (don't upload sensitive documents)

3. Record what metadata you found:
   - Author name?
   - Software used?
   - Creation date?
   - Any hidden information?

**Part 5: OSINT Framework Exploration**

1. Go to https://osintframework.com
2. Click on "Email Address" → "Email Verification"
3. Try a tool like Hunter.io (free tier) to see email formats
4. Click on "Username" → check if a username exists across platforms

**Part 6: Documentation**

Create `lab_09_osint_basics.md` with:

# OSINT Lab 1: Basic Investigation

## Target
- Company: TechVault Inc.
- Domain: techvault.example

## Google Dorking Results
| Dork | Results Found | Notable Findings |
|------|---------------|------------------|
| site:techvault.example | [number] | [describe] |
| filetype:pdf | [number] | [describe] |

## Reverse Image Search
- Image used: [describe]
- Results: [what did you find?]

## Metadata Analysis
- File analyzed: [filename]
- Metadata found:
  - Author: 
  - Software: 
  - Date: 

## OSINT Framework
- Tools tested: [list]
- What worked: [describe]

## Reflection
- What was the most surprising thing you found?
- What would you look for next time?

### Deliverable

Save `lab_09_osint_basics.md` in your course notes folder.

---

## 11. Summary & Key Takeaways

**What OSINT Is:**
- Collecting and analyzing publicly available information
- No hacking, no passwords, no private data

**Passive vs Active:**
- Passive: No interaction with target (Google, LinkedIn)
- Active: Direct interaction (port scanning, crawling)
- Always start with passive OSINT

**Legal Boundaries:**
- Public information = generally legal
- Accessing private areas = illegal
- Using information to harm = unethical

**Core Tools:**
- OSINT Framework — Directory of tools
- Google Dorking — Advanced search operators
- GHDB — Pre-written dorks
- Reverse Image Search — Find image sources
- ExifTool — Extract metadata

**The OSINT Mindset:**
- Be systematic (follow the methodology)
- Document everything
- Stay within legal and ethical boundaries

---

## 12. Quiz

1. What does OSINT stand for?

2. What is the key difference between passive and active OSINT?

3. Give an example of a Google dork that finds PDFs on a specific website.

4. What is the Google Hacking Database (GHDB)?

5. Name two use cases for reverse image search.

6. What metadata might be hidden in a photo you upload online?

7. Is scanning a company's IP addresses without permission considered passive or active OSINT?

8. What is the first step in the OSINT methodology?

<details>
<summary> Click to reveal answers</summary>

1. **Open Source Intelligence**

2. **Passive** does not interact with the target (no traces). **Active** directly interacts with target systems (leaves logs).

3. `site:example.com filetype:pdf`

4. A database of pre-written Google dorks maintained by Offensive Security and Exploit-DB.

5. Any two: Catfishing detection, source verification, location tracking, person identification.

6. Camera model, GPS coordinates, date taken, software used to edit.

7. **Active OSINT** — you are directly interacting with the target's systems.

8. **Define Objectives** — know what you are looking for before you start searching.

</details>

---

## 13. Resources

### Essential Websites
- [OSINT Framework](https://osintframework.com) — Tool directory
- [Google Hacking Database](https://www.exploit-db.com/google-hacking-database) — Dorks
- [Google Images](https://images.google.com) — Reverse image search
- [TinEye](https://tineye.com) — Reverse image search
- [Wayback Machine](https://archive.org/web/) — Archived websites

### Metadata Tools
- [ExifTool](https://exiftool.org) — Command-line metadata (all platforms)
- [FOCA](https://github.com/ElevenPaths/FOCA) — Windows metadata extraction
- [MAT2](https://0xacab.org/jvoisin/mat2) — Metadata anonymization (Linux)

### Learning More
- [Bellingcat OSINT Guide](https://www.bellingcat.com/category/resources/how-tos/) — Investigative OSINT
- [IntelTechniques](https://inteltechniques.com) — OSINT tools and books
- [The OSINT Curious Project](https://osintcurio.us) — Community and tutorials

### Practice Targets (Ethical)
- Your own online presence
- Your company's public information (with permission)
- Capture The Flag (CTF) OSINT challenges

---

**Previous Module:** [Module 8 — Virtualization & Home Lab Setup ](../01_FOUNDATIONS/Module_08_Virtualization_and_Home_Lab.md)

**Next Module:** [Module 10 — OSINT Part 2 (People & Social Media) ](Module_11_OSINT_Part_2.md)

---

*Module 9 | Phase 2: Reconnaissance & OSINT | cybersecurity_beginner_to_advance*
