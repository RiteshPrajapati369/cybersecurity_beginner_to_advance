# Module 11: Anonymity, Privacy & Operational Security

> **Phase 2 — Reconnaissance & OSINT**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 4–5 hours | 📁 Folder: `02_OSINT`

---

## Table of Contents

1. [Surface Web vs Deep Web vs Dark Web](#1-surface-web-vs-deep-web-vs-dark-web)
2. [TOR Browser](#2-tor-browser)
3. [Dark Web Search Engines](#3-dark-web-search-engines)
4. [I2P (Invisible Internet Project)](#4-i2p-invisible-internet-project)
5. [Freenet](#5-freenet)
6. [OnionShare](#6-onionshare)
7. [Tails OS](#7-tails-os)
8. [VPNs vs Proxies vs TOR vs I2P](#8-vpns-vs-proxies-vs-tor-vs-i2p)
9. [Operational Security (OpSec)](#9-operational-security-opsec)
10. [Hands-On Lab: Browse Dark Web Safely & Share Files via OnionShare](#10-hands-on-lab-browse-dark-web-safely--share-files-via-onionshare)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz](#12-quiz)
13. [Resources](#13-resources)

---

## 1. Surface Web vs Deep Web vs Dark Web

The internet is often described as an iceberg. What you see daily is only the tip.

### The Three Layers

```
                    ┌─────────────────────┐
                    │                     │
                    │    Surface Web      │  ← 4% of the internet
                    │   (4% of internet)  │     Google-indexed
                    │                     │     Public websites
                    └─────────────────────┘
                    ┌─────────────────────┐
                    │                     │
                    │     Deep Web        │  ← 90% of the internet
                    │   (90% of internet) │     Not Google-indexed
                    │                     │     Requires login/auth
                    └─────────────────────┘
                    ┌─────────────────────┐
                    │                     │
                    │     Dark Web        │  ← 6% of the internet
                    │   (6% of internet)  │     Requires TOR/I2P
                    │                     │     .onion sites
                    └─────────────────────┘
```

### Surface Web (Clear Web)

**What it is:** Websites indexed by search engines like Google, Bing, and DuckDuckGo.

**Examples:**
- Google.com, Wikipedia.org, YouTube.com
- News websites, corporate websites, blogs
- Social media profiles (public)

**Access:** No special software needed. Standard browser.

**Size:** ~4% of the total internet.

### Deep Web

**What it is:** Content not indexed by standard search engines. Requires authentication, payment, or specific access.

**Examples:**
- Your email inbox (Gmail, Outlook)
- Online banking portals
- Company intranets and internal databases
- Medical records, legal documents
- Subscription-only content (paywalled articles)
- Academic databases (JSTOR, IEEE)
- Private cloud storage (Google Drive, Dropbox private files)

**Access:** Standard browser + login credentials.

**Size:** ~90% of the internet. It is the largest part.

**Is it illegal?** No. The Deep Web is mostly legitimate, private content.

### Dark Web

**What it is:** Small, encrypted portion of the internet that requires special software (TOR, I2P) to access. Designed for anonymity.

**Examples:**
- .onion websites (TOR hidden services)
- Marketplaces (Silk Road, AlphaBay — most are shut down)
- Whistleblowing platforms (SecureDrop)
- Forums and communities
- News sites (ProPublica .onion)

**Access:** TOR Browser, I2P, or Freenet.

**Size:** ~6% of the internet. Small but high-profile.

**Is it illegal?** Accessing the Dark Web is legal. Engaging in illegal activities make it illegal.

---

### Key Differences

| | Surface Web | Deep Web | Dark Web |
|---|---|---|---|
| **Indexed by Google** |  Yes |  No |  No |
| **Requires login** |  No |  Yes |  Yes (TOR needed) |
| **Special software** |  No |  No |  Yes (TOR/I2P) |
| **Anonymity** |  No |  No |  Yes |
| **Size** | ~4% | ~90% | ~6% |
| **Legality** | Legal | Legal | Mostly legal (some illegal content) |

>  **Note:** The Dark Web is not inherently illegal. Many journalists, activists, and whistleblowers use it to communicate safely.

---

## 2. TOR Browser

**TOR (The Onion Router)** is free software that enables anonymous communication by routing traffic through a worldwide network of volunteer-operated relays.

### How TOR Works (The Onion Model)

```
Your Computer → Relay 1 → Relay 2 → Relay 3 → Destination
                  ↑         ↑         ↑
            Encrypted    Encrypted  Encrypted
            (Layer 3)    (Layer 2)  (Layer 1)
```

Each relay only knows the previous and next hop. No single relay knows both the source and destination.

**Three layers of encryption (hence "onion"):**
1. **Guard relay ** — It was also known as Relay 1 and this relay knows your IP, but not your destination.
2. **Middle relay** — It was also known as Relay 2 and this relay knows the previous and next relay, but now you IP or your destination.
3. **Exit relay** — It was also known as Relay 3 and this relay knows the destination, but not your IP.

### Installing TOR Browser
```bash
Suggestion: Use TOR Browser in Virtual Machine for you and your all data safty and Not to enter without
knowing if the link was legal to use or not because they can be harmful like stealing data got virus
in your device. And if any case such problem appear you data can be safe and can uninstall which make
your main machine/devic safe.
```

**Official website:** https://www.torproject.org

**Windows/Linux/macOS:**
1. Download from torproject.org
2. Extract the archive
3. Run the Tor Browser executable
4. Connect to the TOR network

**Linux (command line):**
```bash
sudo apt install torbrowser-launcher
torbrowser-launcher
```

**Android:**
- Download "Tor Browser" from Google Play Store or F-Droid
- **iOS:** Onion Browser (not official, but recommended by TOR Project)

### TOR Browser Configuration

**Security Settings:**

In TOR Browser, click the shield icon to adjust security:

| Level | What It Blocks | Best For |
|---|---|---|
| **Standard** | None | General browsing |
| **Safer** | JavaScript disabled on HTTP sites, some fonts | Most users |
| **Safest** | JavaScript disabled everywhere, some HTML/audio/video disabled | Maximum security |

**Recommended for beginners:** Safer level.

**Bridge Configuration:**

If TOR is blocked in your country or network, use bridges.

```
Settings → Connection → Bridges → Use Bridges
Options:
├── Built-in bridges (obfs4, meek-azure)
└── Provide a bridge address manually
```

**Bridge addresses (new ones at torproject.org):**
```
obfs4 1.2.3.4:443 1234567890
```

### .onion Sites (Hidden Services)

**What are .onion sites?** Websites hosted within the TOR network. They have `.onion` domains and cannot be accessed without TOR.

**Examples of legitimate .onion sites:**

| Site | .onion Address | Purpose |
|---|---|---|
| ProPublica | propub3r6espa33w.onion | Investigative journalism |
| DuckDuckGo | 3g2upl4pq6kufc4m.onion | Private search engine |
| BBC News | bbcnewsd73hkzno2ini43t4gblxvycyac5aw4gnv7t2rccijh7745uqd.onion | News (UK) |
| Facebook | facebookwkhpilnemxj7asaniu7vnjjbiltxjqhye3mhbshg7kx5tfyd.onion | Facebook via TOR |
| SecureDrop | Various | Whistleblowing platform |

### TOR Browser Limitations

| Limitation | Why |
|---|---|
| **Slow speed** | Traffic routed through 3 relays worldwide |
| **No streaming** | Video streaming is impractical |
| **Some sites block TOR** | Captchas, access denial |
| **Not 100% anonymous** | Bad opsec can still deanonymize |
| **Exit node risks** | Traffic can be monitored at exit node (use HTTPS) |

> ⚠️ **Critical Rule:** Always use HTTPS with TOR. The exit relay can see unencrypted traffic.

---

## 3. Dark Web Search Engines

Dark Web search engines index .onion sites. None are as comprehensive as Google.

### Ahmia

**URL (Clear Web):** https://ahmia.fi

**What it is:** A search engine for .onion sites. Also provides an onion service at `msydqstlz2kzerdg.onion`.

**Features:**
- Indexes .onion sites
- Filters CSAM content
- Shows search results with descriptions
- Allows submission of new .onion sites

**How to use:**
1. Visit ahmia.fi in TOR Browser
2. Enter search terms
3. Browse results

---

### Torch

**URL:** `torchdeedp3i2jigzjdmfpn5ttjhthh5wbmda2rr3jvqjg5p77c54dqd.onion`

**What it is:** One of the oldest Dark Web search engines.

**Features:**
- Large index (hundreds of thousands of .onion sites)
- Basic search interface
- Banner ads (typical for Dark Web sites)

---

### Haystak

**URL:** `haystak5njsmn2hqkewecpaxetahtwhsbsa64jom2k22z5afxhnpxfid.onion`

**What it is:** Dark Web search engine with advanced features.

**Features:**
- Indexes 1.5+ million .onion sites
- Advanced search operators
- Paid tier (better results)

---

### Dark.fail

**URL:** https://dark.fail (also .onion)

**What it is:** A status checker for Dark Web sites.

**Features:**
- Shows which .onion sites are online
- Provides verified URLs (reduces phishing risk)
- Monitors site status

**Example listing:**
```
Site Name: Dream Market
URL: dream7f5vcb.onion
Status: ONLINE (last checked: 2 min ago)
```

---

### Not Evil

**URL:** `hss3uro2hsxfogfq.onion`

**What it is:** A Dark Web search engine based on Google's search algorithm.

**Features:**
- Simple interface
- Searches .onion sites
- No JavaScript required

---

### Safety Warning for Dark Web Searches

| Risk | Mitigation |
|---|---|
| **Phishing sites** | Use verified URLs from dark.fail |
| **Malware** | Disable JavaScript, don't download files |
| **Scams** | If it sounds too good, it is |
| **Illegal content** | Don't click on suspicious links |
| **Law enforcement** | Assume activity is monitored |

> ⚠️ **NEVER:** Download files, enter personal information, or click suspicious links on the Dark Web.

---

## 4. I2P (Invisible Internet Project)

**I2P (Invisible Internet Project)** is an anonymous network layer that provides peer-to-peer (P2P) communication.

### How I2P Differs from TOR

| Feature | TOR | I2P |
|---|---|---|
| **Traffic type** | Client-server | Peer-to-peer |
| **Speed** | Slow | Faster |
| **Focus** | Accessing public internet anonymously | Internal hidden services |
| **Routing** | Onion routing (3 relays) | Garlic routing (multiple tunnels) |
| **Best for** | Browsing .onion sites | Hidden services, P2P, email |

### Installing I2P

**Official website:** https://geti2p.net

**Linux (Ubuntu/Debian):**
```bash
sudo apt install i2p
sudo systemctl enable i2p
sudo systemctl start i2p
```

**Windows/macOS:**
1. Download installer from geti2p.net
2. Run installer
3. Launch I2P router
4. Access console at http://127.0.0.1:7657

### I2P Use Cases

| Use Case | Description |
|---|---|
| **Hidden services** | Host websites inside I2P |
| **Email** | I2P-Bote (anonymous email) |
| **File sharing** | P2P sharing with anonymity |
| **Chat** | IRC over I2P |

> **I2P is better for hosting services; TOR is better for browsing.**

---

## 5. Freenet

**Freenet** is a decentralized, peer-to-peer network designed for censorship-resistant communication.

### Key Features

- **Decentralized:** No central servers
- **Distributed storage:** Files stored across multiple nodes
- **Censorship-resistant:** Content cannot be removed by any single party
- **Anonymity:** Built-in anonymization

### Freenet vs TOR vs I2P

| | TOR | I2P | Freenet |
|---|---|---|---|
| **Focus** | Browsing | Hosting services | File storage |
| **Decentralized** |  (central directory) |  (DHT) |  (DHT) |
| **Speed** | Slow | Faster | Very slow |
| **Installing** | Browser | Router software | Standalone app |
| **Best for** | Anonymous browsing | Hidden services | P2P file sharing |

### Installing Freenet

**Download:** https://freenetproject.org

**Usage:**
1. Install Freenet
2. Start the node
3. Access web interface at http://127.0.0.1:8888
4. Browse freesites (`.freenet` URLs)

---

## 6. OnionShare

**OnionShare** is a tool that lets you share files securely and anonymously over the TOR network.

### How It Works

1. You choose a file
2. OnionShare creates a temporary .onion site
3. You share the .onion URL with the recipient
4. Recipient downloads the file via TOR
5. The temporary site closes after download

### Key Features

- **Files never leave your computer** until downloaded
- **Encrypted** by default
- **No intermediaries** — direct P2P transfer over TOR
- **Temporary** — site closes automatically
- **Anonymous** — neither party knows each other's IP

### Installing OnionShare

**Official website:** https://onionshare.org

**Linux:**
```bash
sudo apt install onionshare
```

**Windows/macOS:**
1. Download installer from onionshare.org
2. Install and run

**Command-line version:**
```bash
pip install onionshare-cli
onionshare-cli /path/to/file
```

### Using OnionShare

**GUI Mode:**
1. Open OnionShare
2. Click "Add Files"
3. Select file(s)
4. Click "Start Sharing"
5. Copy the .onion URL
6. Share URL with recipient via secure channel
7. Recipient downloads (via TOR)
8. Files stop sharing after download

**Command-line:**
```bash
onionshare-cli document.pdf
# Creates .onion URL: http://xxxxxxxxxxxx.onion/secret
```

---

## 7. Tails OS

**Tails (The Amnesic Incognito Live System)** is a Linux distribution designed for privacy and anonymity.

### Key Features

| Feature | Description |
|---|---|
| **Live OS** | Boots from USB/DVD; no installation |
| **Amnesic** | Leaves no trace on the computer |
| **TOR by default** | All traffic routed through TOR |
| **Persistent storage** | Optional encrypted storage |
| **Built-in tools** | Encrypted email, Office suite, etc. |

### Use Cases

| Use Case | Why Tails |
|---|---|
| **Whistleblowing** | No trace, no logs |
| **Journalism** | Confidential sources |
| **Sensitive research** | Complete privacy |
| **Circumventing censorship** | TOR baked in |

### Installing Tails

**Official website:** https://tails.net

**Installation steps:**
1. Download Tails ISO
2. Flash to a USB (balenaEtcher, Rufus)
3. Boot from USB
4. Select "Tails" from boot menu
5. Connect to TOR
6. Start working

**Persistent storage setup:**
- During boot, select "Configure persistent storage"
- Set a passphrase
- Store your files and settings securely

> ⚠️ **Tails does not solve all problems.** It does not protect against malware, physical keyloggers, or poor OPSEC.

---

## 8. VPNs vs Proxies vs TOR vs I2P

### Quick Comparison

| | VPN | Proxy | TOR | I2P |
|---|---|---|---|---|
| **Speed** | High | Medium | Slow | Fast |
| **Anonymity** | Medium | Low | High | High |
| **Encryption** | Yes | Sometimes | Yes | Yes |
| **Logging** | Depends on VPN | Often logs | No | Minimal |
| **Best for** | Everyday privacy | Simple geo-blocking | Maximum anonymity | Hosting/hidden services |
| **Cost** | Paid (often) | Free/Paid | Free | Free |

### VPN (Virtual Private Network)

**What it does:** Encrypts all traffic from your device and routes it through a server.

**Pros:**
- Fast speeds
- All traffic encrypted
- Hides IP from websites
- Simple setup

**Cons:**
- VPN provider can see your traffic
- Logs may be kept
- Not fully anonymous

**When to use:** Everyday privacy, public Wi-Fi security, bypassing geo-restrictions.

### Proxy

**What it does:** Routes traffic through an intermediate server (one hop).

**Pros:**
- Fast
- Free options available

**Cons:**
- No encryption
- Proxy sees all traffic
- Easy to identify

**When to use:** Quick geo-block bypass, low-security needs.

### TOR

**What it does:** Routes traffic through 3 relays with multi-layer encryption.

**Pros:**
- High anonymity
- No single point of failure
- Free

**Cons:**
- Slow
- Exit node can see traffic
- Some sites block TOR

**When to use:** Maximum anonymity, accessing Dark Web.

### I2P

**What it does:** Peer-to-peer network for internal services.

**Pros:**
- Faster than TOR
- Better for hosting
- Decentralized

**Cons:**
- Not for clear web
- Smaller community

**When to use:** Hosting hidden services, P2P sharing.

### Use Case Recommendations

| Goal | Recommended Tool |
|---|---|
| Secure browsing on public Wi-Fi | VPN |
| Bypass geo-blocking | VPN or Proxy |
| Access Dark Web | TOR |
| Maximum anonymity (web) | TOR + VPN (advanced) |
| Host anonymous services | I2P or TOR hidden service |
| Share sensitive files | OnionShare |
| Complete privacy (OS) | Tails OS |

---

## 9. Operational Security (OpSec)

**OpSec (Operational Security)** is the practice of protecting sensitive information from adversaries during operations.

### Core OpSec Principles

| Principle | Meaning |
|---|---|
| **Separation** | Keep different activities separate |
| **Minimal disclosure** | Don't share more than needed |
| **Assume risk** | Assume everything can be monitored |
| **Plan ahead** | Think about consequences before acting |
| **Red team yourself** | Think like an attacker |

### Compartmentalization

**Compartmentalization** means keeping different identities and activities separate.

**Example:**

| Compartment | Tools | Purpose |
|---|---|---|
| **Personal** | Real name, home IP, personal email | Everyday life |
| **Professional** | Work email, LinkedIn, corporate VPN | Day job |
| **Security Research** | Pseudonym, TOR, ProtonMail | Research/OSINT |
| **Testing** | Kali VM, isolated network | Lab work |

**Never mix compartments.** If you use the same username across personal and research accounts, you've compromised both.

---

### Minimizing Your Digital Footprint

| Action | Impact |
|---|---|
| **Use unique usernames** | Prevents cross-platform correlation |
| **Don't overshare** | Limit personal info on social media |
| **Use burner emails** | Separate emails for different purposes |
| **Disable location tracking** | Stop apps from tracking you |
| **Check app permissions** | Limit access to camera, contacts, etc. |
| **Avoid metadata** | Remove metadata from files before sharing |
| **Use encrypted chat** | Signal, Wire, or Matrix |
| **Be careful with photos** | Avoid photos with EXIF data |

### Common OpSec Mistakes

| Mistake | How to Avoid |
|---|---|
| **Using same username everywhere** | Use different usernames |
| **Posting photos with metadata** | Strip metadata before sharing |
| **Using real email for OSINT** | Use burner email |
| **Working on personal computer** | Use isolated VM or Tails |
| **Telling others about activities** | Need-to-know basis only |
| **Not using 2FA** | Enable 2FA everywhere |

### OpSec Checklist

Before any sensitive activity, review:

- [ ] Is my device clean (no malware)?
- [ ] Am I using a VPN/TOR/appropriate tool?
- [ ] Have I checked metadata before sharing files?
- [ ] Am I using a unique username/email?
- [ ] Is my activity legal and ethical?
- [ ] Have I documented what I plan to do?
- [ ] What could go wrong? What's my backup plan?

---

## 10. Hands-On Lab: Browse Dark Web Safely & Share Files via OnionShare

### Lab Overview

In this lab you will:

1. Install and configure TOR Browser
2. Access legitimate .onion sites
3. Install and use OnionShare to share a file

### Prerequisites

- TOR Browser installed
- OnionShare installed (if using)
- Stable internet connection

---

### Part 1: Install TOR Browser

**Step 1: Download**

Go to https://www.torproject.org
Download the version for your operating system.

**Step 2: Install**

**Windows:**
- Extract the downloaded file
- Run `Start Tor Browser.exe`

**Linux:**
```bash
sudo apt install torbrowser-launcher -y
torbrowser-launcher
```

**macOS:**
- Open .dmg file
- Drag Tor Browser to Applications
- Open Tor Browser

**Step 3: Connect**

1. Click "Connect"
2. Wait for connection (10-30 seconds)
3. TOR Browser opens with DuckDuckGo as default

**Step 4: Verify**

Visit https://check.torproject.org
You should see: "Congratulations. This browser is configured to use TOR."

---

### Part 2: Browse Legitimate .onion Sites

**Step 1: Visit DuckDuckGo .onion**

In TOR Browser, go to:
```
3g2upl4pq6kufc4m.onion
```

This is the DuckDuckGo search engine over TOR.

**Step 2: Visit ProPublica .onion**

```
propub3r6espa33w.onion
```

ProPublica is a legitimate investigative journalism site.

**Step 3: Visit BBC News .onion**

```
bbcnewsd73hkzno2ini43t4gblxvycyac5aw4gnv7t2rccijh7745uqd.onion
```

**Step 4: Visit Dark.fail (status checker)**

```
darkfailenbsdla5mal2mxn2uz66od5vtzd5qozsatrf5dfe3bbk3fqud.onion
```

This verifies which .onion sites are currently online.

**Step 5: Document your visit**

Record the sites you visited and what you observed.

---

### Part 3: OnionShare File Sharing

**Step 1: Install OnionShare**

**Linux (Ubuntu/Debian):**
```bash
sudo apt install onionshare -y
```

**Windows/macOS:**
Download from https://onionshare.org
Run installer

**Step 2: Prepare a file**

Create a text file named `hello_onionshare.txt` with this content:
```
Hello from OnionShare!
This file was shared securely over the TOR network.
```

**Step 3: Share via OnionShare (GUI)**

1. Open OnionShare
2. Click "Add Files"
3. Select `hello_onionshare.txt`
4. Click "Start Sharing"
5. Wait for the .onion URL to generate
6. Copy the URL (looks like: `http://xxxxxxxxxxxx.onion/secret`)
7. Click "Stop Sharing" (or let it close after download)

**Step 4: Share via OnionShare (Command Line)**

```bash
onionshare-cli hello_onionshare.txt
# Copy the generated .onion URL
```

**Step 5: Download from OnionShare**

1. Open TOR Browser
2. Go to the .onion URL you generated
3. Click the file to download it
4. Verify the content matches

**Step 6: Document your experience**

Record:
- The OnionShare URL (generated)
- How long the site was available
- Any issues encountered

---

### Lab Deliverable

Create `lab_11_anonymity_ops.md`:

```markdown
# Lab 11: Anonymity, Privacy & OpSec

## TOR Browser
- Version installed: [version]
- Connection status: [success/failed]
- Check.torproject.org result: [screenshot]

## .onion Sites Visited
| Site | URL | Status |
|------|-----|--------|
| DuckDuckGo | 3g2upl4pq6kufc4m.onion | [online/offline] |
| ProPublica | propub3r6espa33w.onion | [online/offline] |
| BBC News | [url] | [online/offline] |
| Dark.fail | [url] | [online/offline] |

## OnionShare
- File shared: hello_onionshare.txt
- URL generated: [url]
- Successful download: [yes/no]

## Reflection
- What was your experience using TOR?
- Was the speed different from normal browsing?
- What would you use OnionShare for in real life?

## OpSec Checklist
- [ ] TOR Browser installed and configured
- [ ] .onion sites accessed safely
- [ ] OnionShare used to share a file
- [ ] No personal information entered
- [ ] No files downloaded from suspicious sources
```

---

## 11. Summary & Key Takeaways

**The Three Webs:**
- **Surface Web:** Public, indexed (4%)
- **Deep Web:** Private, behind logins (90%)
- **Dark Web:** Anonymous, requires TOR (6%)

**TOR:**
- Anonymizes traffic via 3 relay hops
- Use for maximum anonymity
- .onion sites are TOR-hosted websites
- Always use HTTPS

**Dark Web Search:**
- Ahmia (clear web), Torch, Haystak
- Dark.fail for site status verification
- Be cautious — phishing and scams are common

**Other Anonymity Tools:**
- **I2P:** Peer-to-peer, good for hosting
- **Freenet:** Decentralized storage
- **OnionShare:** Anonymous file sharing
- **Tails:** Amnesic live OS, leaves no trace

**VPNs vs TOR:**
- VPN = fast, moderate anonymity
- TOR = slow, high anonymity
- Use both only when necessary

**OpSec:**
- Compartmentalize identities
- Minimize digital footprint
- Assume compromise
- Think before you act

---

## 12. Quiz

1. What percentage of the internet is the Surface Web?

2. What is the main difference between the Deep Web and the Dark Web?

3. How many relays does TOR traffic pass through?

4. Name two legitimate .onion sites.

5. What is OnionShare used for?

6. What does Tails OS do that makes it unique?

7. What is the main advantage of I2P over TOR?

8. What is compartmentalization in OpSec?

<details>
<summary>📋 Click to reveal answers</summary>

1. ~4% of the internet.

2. The Deep Web is private, indexed content (behind logins, paywalls). The Dark Web is encrypted, anonymous content that requires TOR/I2P to access.

3. Three relays: Guard relay, Middle relay, Exit relay.

4. Any two: DuckDuckGo (3g2upl4pq6kufc4m.onion), ProPublica (propub3r6espa33w.onion), BBC News, Facebook .onion, SecureDrop.

5. OnionShare is a secure, anonymous file-sharing tool that creates temporary .onion sites for file transfer.

6. Tails OS is amnesic — it leaves no trace on the computer after shutdown. It also routes all traffic through TOR by default.

7. I2P is faster than TOR and better for hosting hidden services and P2P applications.

8. Compartmentalization is keeping different identities and activities separate. For example, having different usernames, emails, and browsers for personal, professional, and research activities.

</details>

---

## 13. Resources

### Official Sites
- [TOR Project](https://www.torproject.org)
- [OnionShare](https://onionshare.org)
- [Tails OS](https://tails.net)
- [I2P](https://geti2p.net)
- [Freenet](https://freenetproject.org)

### Dark Web Directories
- [Ahmia](https://ahmia.fi)
- [Dark.fail](https://dark.fail)
- [Dark Web Links](https://darkweblinks.com) (use caution)

### Privacy & OpSec Guides
- [Electronic Frontier Foundation (EFF) Surveillance Self-Defense](https://ssd.eff.org)
- [Privacy Guides](https://privacyguides.org)
- [Surveillance Self-Defense - TOR](https://ssd.eff.org/en/module/how-use-tor)

### Practice
- [TryHackMe TOR Room](https://tryhackme.com/room/tor)
- [OnionShare Tutorial](https://onionshare.org/tutorial.html)

---

**Previous Module:** [Module 10 — OSINT Part 2 →](Module_10_OSINT_Part_2.md)

**Next Module:** [Module 12 — Network Scanning with Nmap →](../Module_12_Nmap.md)

---

*Module 11 | Phase 2: Reconnaissance & OSINT | cybersecurity_beginner_to_advance*
