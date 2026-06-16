# Module 10: OSINT (Open Source Intelligence) - Part 2

> **Phase 2 — Reconnaissance & OSINT**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 5–6 hours | 📁 Folder: `02_OSINT`

---

## Table of Contents

1. [People Intelligence](#1-people-intelligence)
2. [Social Media OSINT](#2-social-media-osint)
3. [Email OSINT](#3-email-osint)
4. [Username OSINT](#4-username-osint)
5. [Phone OSINT](#5-phone-osint)
6. [Domain OSINT](#6-domain-osint)
7. [Code OSINT (GitHub)](#7-code-osint-github)
8. [Hands-On Lab: Full OSINT Investigation](#8-hands-on-lab-full-osint-investigation)
9. [Summary & Key Takeaways](#9-summary--key-takeaways)
10. [Quiz](#10-quiz)
11. [Resources](#11-resources)

---

## 1. People Intelligence

People intelligence is the practice of finding information about specific individuals using publicly available data.

### Why People Intelligence Matters

| Use Case | Application |
|---|---|
| **Social engineering** | Gathering details to craft convincing phishing emails |
| **Insider threat** | Identifying employees who might be at risk (financial stress, disgruntled) |
| **Due diligence** | Background checks on potential hires or business partners |
| **Account recovery** | Verifying identity when someone loses access |

### Core Tools for People Intelligence

#### Sherlock

**What it is:** A command-line tool that searches for usernames across 300+ social networks and websites.

**Installation:**
```bash
git clone https://github.com/sherlock-project/sherlock.git
cd sherlock
pip install -r requirements.txt
```

**Usage:**
```bash
python sherlock username
python sherlock john.doe jane.smith  # Multiple usernames
python sherlock --output result.txt username
```

**Example output:**
```
[*] Checking username johndoe on:
[+] GitHub: https://github.com/johndoe
[+] Twitter: https://twitter.com/johndoe
[-] Facebook: Not found
[+] Reddit: https://reddit.com/user/johndoe
[+] Instagram: https://instagram.com/johndoe_
```

---

#### Maigret

**What it is:** An advanced Sherlock alternative with better coverage and graph visualization. Searches over 2000 sites.

**Installation:**
```bash
pip install maigret
# Or:
docker run -it --rm soxoj/maigret username
```

**Usage:**
```bash
maigret username
maigret --site-count 500 username  # Limit to 500 sites
maigret --output report.html username  # Generate HTML report
```

**Why Maigret over Sherlock:** More sites, better success rate, visual reports.

---

#### WhatsMyName

**What it is:** A web-based username search tool with a massive database of sites.

**URL:** https://whatsmyname.app

**Usage:**
1. Enter a username
2. Click "Search"
3. Results show green (found) or red (not found)

**Also available as command-line:**
```bash
git clone https://github.com/WebBreacher/WhatsMyName
cd WhatsMyName
python web_accounts_list_checker.py -u username
```

---

#### Holehe

**What it is:** A tool that checks if an email address is registered on various websites WITHOUT sending any email.

**How it works:** Holehe uses website APIs and registration flows to check if an email exists — no email sent, no notification to the target.

**Installation:**
```bash
pip install holehe
```

**Usage:**
```bash
holehe email@example.com
```

**Example output:**
```
[+] email@example.com
[+] adobe.com : registered
[+] amazon.com : not registered
[+] facebook.com : registered
[+] instagram.com : not registered
[+] twitter.com : registered
[+] spotify.com : registered
```

**Command-line with JSON output:**
```bash
holehe email@example.com --json
```

---

## 2. Social Media OSINT

Social media platforms contain vast amounts of personal information. Each platform has different OSINT approaches.

### LinkedIn OSINT

**What you can find:**
- Employee names and job titles
- Company hierarchy and departments
- Employment history (reveals previous employers, locations)
- Skills and technologies used
- Connections (reveals other employees)
- Profile photos (for reverse image search)

**Manual techniques:**
```bash
# Google dorks for LinkedIn
site:linkedin.com/company/companyname
site:linkedin.com/in/ "Job Title" "Company Name"

# Find employees by role
site:linkedin.com/in "Security Engineer" "Company Name"

# Find email patterns
# Common patterns: first.last@company.com, flast@company.com
```

**Tools:**
- **LinkedIn Recruiter** (paid) — Advanced search
- **PhantomJS scraper** (complex, may violate ToS)

> ⚠️ **Warning:** Automated scraping of LinkedIn violates their Terms of Service. Stick to manual searches.

### Twitter OSINT

**What you can find:**
- User location and timezone
- Interests and opinions (tweets)
- Connections (followers, following)
- Check-ins and travel patterns
- Photos and metadata

**Twitter Advanced Search Operators:**

| Operator | Function | Example |
|---|---|---|
| `from:` | Tweets from specific user | `from:username` |
| `to:` | Tweets to specific user | `to:username` |
| `@` | Mentions | `@username` |
| `since:` | Tweets after date | `since:2024-01-01` |
| `until:` | Tweets before date | `until:2024-12-31` |
| `near:` | Tweets near location | `near:"New York"` |
| `within:` | Radius | `within:10mi` |
| `filter:images` | Tweets with images | `from:username filter:images` |
| `min_retweets:` | Minimum retweets | `min_retweets:100` |

**Example searches:**
```bash
# Tweets from a user with images
from:elonmusk filter:images

# Tweets about a company near a location
"Company Name" near:"Austin" within:25mi

# Tweets from employees mentioning internal tools
from:companydomain.com "Slack" or "Jira"
```

**Tools:**

| Tool | Type | Status | Notes |
|---|---|---|---|
| **snscrape** | CLI | Active | No API key needed |
| **Twint** | CLI | Discontinued | No longer works but need to gain knowledge |
| **Twitter API** | Official | Requires API key | Limited free tier |

**snscrape installation:**
```bash
pip install snscrape
```

**snscrape usage:**
```bash
# Scrape tweets from a user
snscrape twitter-user username

# Scrape tweets with keyword
snscrape twitter-hashtag cybersecurity

# Scrape tweets from date range
snscrape twitter-from:username since:2024-01-01 until:2024-06-01
```

### Facebook OSINT

**What you can find:**
- Personal information (birthday, hometown, education, work)
- Friends list (reveals connections)
- Photos (geotags, people tagging)
- Groups and interests
- Public posts

**Manual techniques:**
```bash
# Google dorks for Facebook
site:facebook.com "Jane Doe" "Company Name"
site:facebook.com "Current City" "Hometown"

# Graph Search (deprecated but some queries still work)
# facebook.com/search/people/?q=keyword
```

> ⚠️ **Warning:** Facebook has strong anti-scraping protections. Automated tools often fail or get IP-banned. Manual searching is the only reliable method.

### Instagram OSINT

**What you can find:**
- Photos (locations, tags, subjects)
- Bio information (links, descriptions)
- Followers/following (some public)
- Stories (temporary — act fast)

**Manual techniques:**
```bash
# Google dorks for Instagram
site:instagram.com "username"
# View profile without login: instagram.com/username

# Extract metadata from photos
exiftool instagram_photo.jpg  # Limited - Instagram strips most metadata
```

**Tools:**
- **Osintgram** — Python tool for Instagram OSINT
```bash
git clone https://github.com/Datalux/Osintgram
cd Osintgram
pip install -r requirements.txt
# Configure credentials in config.cfg
python main.py username
```

> ⚠️ **Warning:** Instagram aggressively blocks scrapers. Use with caution.

---

## 3. Email OSINT

Email addresses are often the key to linking multiple accounts and identities.

### Email Verification & Discovery

#### Hunter.io

**What it is:** Finds email addresses associated with a domain and verifies existing emails.

**URL:** https://hunter.io

**Free tier:** 25 searches/month

**Usage:**
1. Enter a domain (example.com)
2. Hunter finds common email patterns and discovered emails
3. Shows sources where emails were found (LinkedIn, GitHub, etc.)

**Example output for `example.com`:**
```
john.doe@example.com — source: LinkedIn
jane.smith@example.com — source: GitHub
admin@example.com — standard role
```

**Email pattern discovery:**
- `first@example.com`
- `first.last@example.com`
- `flast@example.com`
- `firstl@example.com`

---

#### EmailRep

**What it is:** API and web tool that rates email reputation based on data breaches, spam reports, and domain age.

**URL:** https://emailrep.io

**Usage:**
```bash
# Web interface
https://emailrep.io/email@example.com

# API (free tier)
curl https://emailrep.io/email@example.com
```

**Output includes:**
- Reputation score (0-100)
- Breach data (has this email appeared in breaches?)
- Domain reputation
- Suspicious indicators

---

#### HaveIBeenPwned (HIBP)

**What it is:** The largest database of breached credentials. Search by email to see if it appears in known data breaches.

**URL:** https://haveibeenpwned.com

##Using Comand Lines

**Usage:**
1. Enter an email address
2. HIBP shows which breaches contain that email
3. Shows what data was exposed (password hash, name, address, etc.)

**API (free, rate-limited):**
```bash
curl https://haveibeenpwned.com/api/v3/breachedaccount/email@example.com
# Add header: hibp-api-key: YOUR_KEY (Login in haveibeenpwned and Get your API)
```

**Domain search (paid):**
```bash
# Check all emails in a domain
curl https://haveibeenpwned.com/api/v3/breacheddomain/example.com
```

---

#### DeHashed

**What it is:** A paid breach data aggregator that searches across breaches, paste sites, and more.

**URL:** https://dehashed.com

**Paid tier:** Starts around $5-10/month

**Why pay for DeHashed:**
- Combines multiple breach sources
- Includes passwords (hashed or plaintext)
- Searches across email, username, IP, domain, phone
- Much more comprehensive than free options

**Usage (after subscription):**
```bash
# Search by email
https://dehashed.com/search?email=email@example.com

# Search by username
https://dehashed.com/search?username=targetuser
```

> ⚠️ **Ethical note:** Use DeHashed for your own security or authorized testing only. Do not use to access others' accounts.

---

### Email OSINT Workflow

```
1. Start with domain in Hunter.io → find email patterns
2. Try common patterns with emailrep.io → verify existence
3. Check HIBP → see if email appears in known breaches
4. If authorized/searching yourself, try DeHashed → find associated passwords
5. Use Holehe → see which sites the email is registered on
```

---

## 4. Username OSINT

A username across multiple platforms can reveal a complete online identity.

### Tools Comparison

| Tool | Type | Sites Covered | Best For |
|---|---|---|---|
| **Sherlock** | CLI | 300+ | Quick checks |
| **Maigret** | CLI | 2000+ | Deep searches |
| **WhatsMyName** | Web/CLI | 400+ | Simple interface |
| **Namechk** | Web | 50+ | Common platforms |
| **KnowEm** | Web | 500+ | Brand protection |

### Namechk

**URL:** https://namechk.com

**Usage:**
1. Enter a username
2. Shows availability across 50+ social platforms
3. Green = available, Red = taken

**Best for:** Checking common platforms quickly.

### KnowEm

**URL:** https://knowem.com

**Usage:**
1. Enter a username
2. Shows taken/available across 500+ sites
3. Premium tier shows more details

**Best for:** Comprehensive checks across many platforms.

### Username Correlation Example

```
If you find "johndoe" on:
- GitHub (shows code, email)
- Twitter (shows location, interests)
- LinkedIn (shows employer, real name)
- Reddit (shows posts, opinions)

You can build a complete profile:
- Real name from LinkedIn
- Skills from GitHub
- Location from Twitter
- Interests from Reddit
```

---

## 5. Phone OSINT

Phone numbers can reveal carrier, location (approximate), and associated accounts.

### Carrier Lookup

**Free methods:**
```bash
# Search carrier by prefix (first 6 digits)
# Example: 212-555-XXXX (212-555 prefix)
# Lookup tables available online

# LRN (Local Routing Number) lookup — limited free services
```

**Paid/API methods:**
- Twilio Lookup API
- Numverify API
- AbstractAPI Phone Validation

### Phone Number OSINT Tools

| Tool | Type | What It Finds |
|---|---|---|
| **SpyDialer** | Web | Voicemail, carrier, approximate location |
| **Zlookup** | Web | Name, carrier, location (US only) |
| **Numverify** | API | Carrier, location, line type |
| **Truecaller** | App | Name (if user has an account) |

**SpyDialer usage:**
1. Go to https://spydialer.com
2. Enter phone number
3. Shows: carrier, voicemail greeting (if available), location

**Truecaller OSINT:**
```bash
# Search via web (login required)
https://www.truecaller.com/search

# Returns name if user has registered
```

### What Phone Numbers Reveal

| Information | How to Get |
|---|---|
| **Carrier** | Prefix lookup, SpyDialer |
| **Approximate city/area code** | Area code lookup |
| **Registered name** | Truecaller, Zlookup (if user opted in) |
| **Voicemail greeting** | SpyDialer (may reveal name) |
| **Associated social accounts** | Search number on Facebook, Twitter, Instagram |

> ⚠️ **Privacy warning:** Phone OSINT is invasive. Only perform on numbers you own or have explicit authorization to investigate.

---

## 6. Domain OSINT

Domains reveal infrastructure, ownership, and technical details about organizations.

### Whois Lookup

**What it reveals:** Registrar, registration/expiration dates, name servers, registrant contact info (often redacted for privacy).

**Command line:**
```bash
whois example.com
```

**Online tools:**
- https://who.is
- https://lookup.icann.org
- https://securitytrails.com

**Example whois output:**
```
Domain Name: EXAMPLE.COM
Registry Domain ID: 123456
Registrar: GoDaddy
Creation Date: 2000-01-01
Expiration Date: 2025-01-01
Name Server: NS1.EXAMPLE.COM
Name Server: NS2.EXAMPLE.COM
```

### DNS Lookup

**nslookup (basic):**
```bash
nslookup example.com
nslookup example.com 8.8.8.8  # Use specific DNS server
```

**dig (advanced):**
```bash
# A record (IPv4)
dig example.com A

# AAAA record (IPv6)
dig example.com AAAA

# MX record (mail servers)
dig example.com MX

# TXT records (SPF, DKIM, etc.)
dig example.com TXT

# NS records (name servers)
dig example.com NS

# ANY (all records)
dig example.com ANY
```

### SecurityTrails

**What it is:** Domain intelligence platform with historical DNS data.

**URL:** https://securitytrails.com

**Free tier:**
- DNS lookups (A, MX, NS, TXT)
- Whois lookup
- Domain search

**Paid features:**
- Historical DNS data (what IP was hosted before?)
- Reverse DNS (what domains share an IP?)
- Subdomain enumeration

**Example usage (free):**
```bash
# Search domain
https://securitytrails.com/domain/example.com

# See current and historical DNS records
```

### Censys

**What it is:** Search engine for internet devices and their configurations. Finds servers, open ports, SSL certificates.

**URL:** https://censys.io

**Free tier:** Limited queries

**What you can find:**
```bash
# Search for certificates issued to example.com
https://search.censys.io/certificates?q=example.com

# Search for devices hosting example.com
https://search.censys.io/hosts?q=example.com
```

**Use cases:**
- Find subdomains via certificate transparency logs
- Identify cloud providers hosting the target
- Discover exposed services

### Shodan

**What it is:** Search engine for internet-connected devices (IoT, servers, cameras, industrial controls).

**URL:** https://shodan.io

**Free tier:** Limited (requires login)

**What you can find:**
```bash
# Search by domain
https://www.shodan.io/host/1.1.1.1

# Search by SSL certificate
ssl:example.com

# Search by organization
org:"Company Name"
```

**Use cases:**
- Find exposed devices belonging to an organization
- Discover forgotten infrastructure
- Identify open ports and vulnerable services

### Criminal IP

**What it is:** Alternative to Shodan with different data sources and UI.

**URL:** https://www.criminalip.io

**Free tier:** Available

**Usage:**
```bash
# Search IP
https://www.criminalip.io/ip/8.8.8.8

# Search domain
https://www.criminalip.io/domain/example.com
```

### Domain OSINT Workflow

```
1. whois → Find registrar, dates, name servers
2. dig → Get current DNS records
3. SecurityTrails → Find historical DNS, subdomains
4. Censys → Find SSL certificates (reveals subdomains)
5. Shodan → Find exposed devices, open ports
6. Criminal IP → Alternative view of same infrastructure
```

---

## 7. Code OSINT (GitHub)

GitHub contains millions of public repositories. Developers often accidentally expose secrets in code.

### What to Search For

| Type | Search Pattern |
|---|---|
| **API Keys** | `api_key`, `apikey`, `secret_key` |
| **Passwords** | `password =`, `passwd =` |
| **Tokens** | `token =`, `access_token` |
| **Private Keys** | `BEGIN RSA PRIVATE KEY` |
| **Credentials in config** | `.env`, `config.php`, `settings.py` |
| **Internal URLs** | `internal.company.com`, `admin.company.com` |
| **Employee emails** | `@company.com` in commits |
| **Database connections** | `mysql://`, `postgres://` |

### GitHub Search Operators

| Operator | Function | Example |
|---|---|---|
| `repo:` | Specific repository | `repo:username/repo` |
| `user:` | Specific user | `user:username` |
| `org:` | Specific organization | `org:organization` |
| `language:` | Programming language | `language:python` |
| `path:` | File path | `path:config` |
| `extension:` | File extension | `extension:env` |
| `size:` | File size | `size:>1000` |
| `filename:` | Exact filename | `filename:docker-compose.yml` |

### GitHub Dorks Examples

```bash
# Find API keys in code
"api_key" language:python

# Find AWS keys
"AKIA" "secret_access_key"

# Find .env files (contains environment variables)
filename:.env

# Find passwords in config files
"password" extension:json

# Find private keys
"BEGIN RSA PRIVATE KEY" language:python

# Find internal company references in code
"internal.company.com" extension:js

# Find employee emails in commits
"@company.com" extension:py

# Find exposed database connection strings
"mongodb://" "password"
```

### GitHub Advanced Search

**URL:** https://github.com/search/advanced

**Web interface allows:**
- Multiple qualifiers combined
- Date ranges
- Exact phrase matching

**Example advanced search:**
```
org:google extension:py "password" NOT "test"
```

### Tools for GitHub OSINT

**GitLeaks:** CLI tool to scan Git repositories for secrets.

```bash
# Install
brew install gitleaks
# or
docker pull zricethezav/gitleaks

# Scan a repository
gitleaks detect --source https://github.com/example/repo

# Scan with custom rules
gitleaks detect --source . --config gitleaks.toml
```

**TruffleHog:** Another secret scanner with regex and entropy analysis.

```bash
# Install
pip install truffleHog

# Scan repository
trufflehog github --repo=https://github.com/example/repo
```

**GitHub Archive:** Historical data of GitHub events (commits, issues, stars).

```bash
# Use BigQuery (Google Cloud) to query GitHub Archive
# Example: Find secrets committed and later deleted
```

### Code OSINT Workflow

```
1. Start with org:COMPANY on GitHub
2. Search for common secret patterns (api_key, password)
3. Look for config files (.env, config.yml)
4. Check commit history for deleted secrets
5. Use GitLeaks/TruffleHog for automated scanning
6. Download suspicious repos for offline analysis
```

> ⚠️ **Ethical note:** Finding exposed credentials does not give permission to USE them. Report findings responsibly through vulnerability disclosure programs.

---

## 8. Hands-On Lab: Full OSINT Investigation

### Objective

Conduct a complete OSINT investigation on a target. **You will investigate yourself or a target you have explicit permission to investigate.**

### Target Selection

Choose ONE:
- **Yourself** (most ethical — investigate your own digital footprint)
- **A friend** (with written permission)
- **A company you work for** (with authorization)

### Lab Instructions

**Phase 1: Reconnaissance Plan**

Create a target profile document:

```markdown
## Target Profile
- Name: [your name / target name]
- Email: [email to investigate]
- Username: [common username]
- Domain: [company domain if applicable]
```

**Phase 2: Email OSINT**

```bash
# 1. Check HaveIBeenPwned
https://haveibeenpwned.com (enter your email)

# 2. Check Holehe (command line)
holehe your_email@example.com

# 3. If applicable: Check Hunter.io for your company domain
https://hunter.io (search your company domain)
```

Document: Which sites is your email registered on? What breaches contain your email?

**Phase 3: Username OSINT**

```bash
# 1. Sherlock (command line)
python sherlock your_username

# 2. Maigret (command line)
maigret your_username

# 3. WhatsMyName (web)
https://whatsmyname.app (enter your username)
```

Document: On which platforms do you have accounts?

**Phase 4: Social Media OSINT**

```bash
# 1. Google dorks for your name
"Your Full Name" 
"Your Full Name" site:linkedin.com
"Your Full Name" site:twitter.com

# 2. Reverse image search your profile photos
https://images.google.com

# 3. If using Twitter: Try snscrape
snscrape twitter-user your_username
```

Document: What information is publicly visible on your social media?

**Phase 5: Domain OSINT (if investigating a company domain)**

```bash
# 1. Whois lookup
whois company.com

# 2. DNS records
dig company.com A
dig company.com MX
dig company.com TXT

# 3. SecurityTrails (free tier)
https://securitytrails.com/domain/company.com
```

Document: What infrastructure does the domain use? What email providers?

**Phase 6: Code OSINT**

```bash
# 1. GitHub search for your username
https://github.com/search?q=your_username

# 2. Search for accidentally exposed info
# In GitHub search:
username:your_username "password"
username:your_username "api_key"

# 3. If you have repos: Run GitLeaks
cd your_repo
gitleaks detect --source .
```

Document: Did you find any exposed secrets in your own code?

**Phase 7: Phone OSINT (optional — skip if uncomfortable)**

```bash
# 1. Check Truecaller (web or app)
# 2. Search phone number on Google
"123-456-7890"
```

Document: What information is associated with your phone number?

**Phase 8: Compile Report**

Create `lab_10_full_osint_report.md`:

```markdown
# Full OSINT Investigation Report

## Target
- Name: [target]
- Email: [email]
- Username: [username]
- Domain: [domain]

## Email OSINT Findings
| Tool | Finding |
|------|---------|
| HIBP | [breaches found?] |
| Holehe | [sites registered on] |
| Hunter | [email pattern] |

## Username OSINT Findings
| Platform | Username Found? | URL |
|----------|----------------|-----|
| GitHub | Yes/No | [link] |
| Twitter | Yes/No | [link] |
| [others] | Yes/No | [link] |

## Social Media OSINT Findings
- Public information exposed: [list]
- Photos revealing location: [yes/no]
- Job history visible: [yes/no]

## Domain OSINT Findings (if applicable)
- Registrar: [name]
- Name servers: [list]
- Mail servers: [list]
- Subdomains found: [list]

## Code OSINT Findings
- Exposed secrets found: [yes/no]
- Sensitive files found: [yes/no]
- Action needed: [list]

## Phone OSINT Findings (optional)
- Name associated: [yes/no]
- Carrier: [name]
- Location: [city]

## Remediation Actions
Based on your findings, list steps to reduce your digital footprint:
1. [action]
2. [action]
3. [action]

## Reflection
- What surprised you most about your own digital footprint?
- What will you change based on this investigation?
```

### Deliverable

Save `lab_10_full_osint_report.md` with your findings.

---

## 9. Summary & Key Takeaways

**People Intelligence:**
- Sherlock, Maigret, WhatsMyName find usernames across platforms
- Holehe checks email registration without sending notifications

**Social Media OSINT:**
- LinkedIn reveals employment and connections
- Twitter requires search operators and snscrape
- Facebook/Instagram are mostly manual due to anti-scraping

**Email OSINT:**
- Hunter.io finds email patterns and verified emails
- HaveIBeenPwned checks breach exposure
- DeHashed (paid) is the most comprehensive

**Username OSINT:**
- Same username across platforms = linked identity
- Namechk and KnowEm check common platforms

**Phone OSINT:**
- Carrier, approximate location, sometimes name
- Use only with authorization (invasive)

**Domain OSINT:**
- whois, dig for basic DNS
- SecurityTrails for historical data
- Censys/Shodan for infrastructure discovery

**Code OSINT:**
- GitHub dorks find exposed secrets
- GitLeaks/TruffleHog automate secret scanning
- Many developers accidentally commit passwords

---

## 10. Quiz

1. What is the main difference between Sherlock and Maigret?

2. What does Holehe check WITHOUT sending an email?

3. How can you find all PDFs on a specific company website using Google?

4. What information can you get from a whois lookup?

5. Name three GitHub search operators.

6. What is the difference between Censys and Shodan?

7. What tool would you use to check if your email appears in known data breaches?

8. Why should you NEVER search for someone else's phone number without authorization?

<details>
<summary>📋 Click to reveal answers</summary>

1. Maigret covers more sites (2000+ vs 300+) and generates visual HTML reports. Sherlock is simpler and faster for basic checks.

2. Holehe checks if an email is registered on various websites using public APIs — it does NOT send any email to the target.

3. `site:company.com filetype:pdf`

4. Registrar, registration/expiration dates, name servers, and (sometimes) registrant contact information.

5. Any three: `repo:`, `user:`, `org:`, `language:`, `path:`, `extension:`, `size:`, `filename:`

6. Censys focuses on SSL certificates and host configurations. Shodan focuses on IoT devices, industrial controls, and open ports. They have overlapping but different data sets.

7. HaveIBeenPwned (HIBP)

8. Phone OSINT is invasive and can reveal personal information like name, location, and voicemail. Searching without authorization violates privacy norms and may be illegal depending on jurisdiction.

</details>

---

## 11. Resources

### People Intelligence
- [Sherlock GitHub](https://github.com/sherlock-project/sherlock)
- [Maigret GitHub](https://github.com/soxoj/maigret)
- [WhatsMyName](https://whatsmyname.app)
- [Holehe GitHub](https://github.com/megadose/holehe)

### Social Media OSINT
- [snscrape GitHub](https://github.com/JustAnotherArchivist/snscrape)
- [Osintgram GitHub](https://github.com/Datalux/Osintgram)
- [Twitter Advanced Search](https://twitter.com/search-advanced)

### Email OSINT
- [Hunter.io](https://hunter.io)
- [EmailRep](https://emailrep.io)
- [HaveIBeenPwned](https://haveibeenpwned.com)
- [DeHashed](https://dehashed.com)

### Domain OSINT
- [SecurityTrails](https://securitytrails.com)
- [Censys](https://censys.io)
- [Shodan](https://shodan.io)
- [Criminal IP](https://www.criminalip.io)

### Code OSINT
- [GitHub Advanced Search](https://github.com/search/advanced)
- [GitLeaks](https://github.com/gitleaks/gitleaks)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)

### Practice Platforms
- [TryHackMe OSINT Room](https://tryhackme.com/room/osint)
- [HackTheBox OSINT Challenges](https://www.hackthebox.com)

---

**Previous Module:** [Module 9 — OSINT Part 1 →](Module_09_OSINT_Part_1.md
)

**Next Module:** [Module 11 — Anonymity, Privacy & OpSec →](Module_11_Anonymity_Privacy_OpSec.md)

---

*Module 10 | Phase 2: Reconnaissance & OSINT | cybersecurity_beginner_to_advance*
