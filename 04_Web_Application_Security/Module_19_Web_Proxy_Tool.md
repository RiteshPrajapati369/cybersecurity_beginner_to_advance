# Module 19: Web Proxy Tools (Burp Suite & OWASP ZAP)

> **Phase 4 — Web Application Security**
> 🟠 Difficulty: Intermediate | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `04_WEB_SECURITY`

---

## Table of Contents

1. [What Are Web Proxies?](#1-what-are-web-proxies)
2. [Burp Suite Community Edition](#2-burp-suite-community-edition)
3. [Burp Suite Features (Core Tools)](#3-burp-suite-features-core-tools)
4. [Burp Extensions (BApp Store)](#4-burp-extensions-bapp-store)
5. [OWASP ZAP](#5-owasp-zap)
6. [ZAP Features (Core Tools)](#6-zap-features-core-tools)
7. [Burp Suite vs OWASP ZAP (Comparison)](#7-burp-suite-vs-owasp-zap-comparison)
8. [Hands-On Lab: Intercept and Modify Requests](#8-hands-on-lab-intercept-and-modify-requests)
9. [Hands-On Lab: Brute-Force Login with Intruder](#9-hands-on-lab-brute-force-login-with-intruder)
10. [Summary & Key Takeaways](#10-summary--key-takeaways)
11. [Quiz](#11-quiz)
12. [Resources](#12-resources)

---

## 1. What Are Web Proxies?

**Web proxies** are specialized tools that sit between your browser and the target web server, acting as a **man-in-the-middle (MITM)** to capture, view, and manipulate all HTTP/HTTPS traffic .

```
┌─────────────────────────────────────────────────────────────────────┐
│                    WEB PROXY WORKFLOW                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Browser (User)  ←→  Web Proxy (Burp/ZAP)  ←→  Target Server      │
│                                                                     │
│  [Request] ───────────────────────────────────────────────────────→ │
│  [Response] ←─────────────────────────────────────────────────────  │
│                                                                     │
│  ALL traffic is intercepted, visible, and modifiable!              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Why Use a Web Proxy?

| Feature | What It Does |
|---|---|
| **Intercept** | Pause and modify requests before they reach the server  |
| **Analyze** | View every request/response in detail |
| **Replay** | Resend requests with modifications  |
| **Fuzz** | Automate payload injection (brute force, injection testing)  |
| **Scan** | Automatically detect vulnerabilities  |

---

## 2. Burp Suite Community Edition

**Burp Suite (Burp)** is the most common web proxy tool for web penetration testing .

### Version Comparison

| Feature | Community (Free) | Professional ($399/year) | Enterprise |
|---|---|---|---|
| Proxy/Intercept |  Yes |  Yes |  Yes |
| Repeater |  Yes |  Yes |  Yes |
| Intruder |  Yes (throttled) |  Yes (fast) |  Yes |
| Scanner |  No |  Yes |  Yes |
| Extensions |  Limited |  All |  All |



>  **Note:** _The Community version is powerful enough for most penetration testers. The Intruder is throttled in the free version, but still functional._

### Installing Burp Suite

**Step 1: Prerequisites**

- Java Runtime Environment (JRE) 8 or later
- Download from: https://portswigger.net/burp/communitydownload

**Step 2: Installation**

```bash
# Linux (Debian/Ubuntu/Kali)
sudo apt install burpsuite -y

# Or download JAR file and run:
java -jar burpsuite_community_v*.jar
```

**Windows/macOS:**
- Download the installer
- Run the .exe or .dmg
- Follow installation wizard

**Step 3: First Launch**

1. Click **"Next"** through the setup
2. Accept the license agreement
3. Choose **"Burp Suite"** as the installation type

### Configuring Proxy Settings

#### Option A: Burp's Built-in Browser (Recommended)

Burp Suite provides a built-in Chromium browser that is **pre-configured** to use the proxy .

1. Go to **Proxy** tab
2. Click **"Open Browser"**
3. A Chromium browser opens with proxy already configured

#### Option B: Manual Browser Configuration

**Firefox:**

1. Settings → General → Network Settings
2. Select **"Manual proxy configuration"**
3. HTTP Proxy: `127.0.0.1`, Port: `8080`
4. Check **"Also use this proxy for HTTPS"**
5. Click OK 

**Chrome/Edge:**

1. Use extension: **Proxy SwitchyOmega**
2. Configure: `127.0.0.1:8080`
3. Switch to the proxy profile when testing

### Installing Burp's CA Certificate (For HTTPS)

Burp needs its own certificate to intercept HTTPS traffic .

**Step 1: Download Certificate**

1. Configure browser to use Burp proxy
2. Visit: `http://burpsuite`
3. Click **"CA Certificate"** → Download

**Step 2: Import Certificate**

**Firefox:**
1. Settings → Privacy & Security → Certificates → View Certificates
2. Click **"Import…"**
3. Select the downloaded certificate
4. Check **"Trust this CA to identify websites"**
5. Click OK 

**Chrome/Edge:**
1. Open **"Manage Certificates"** (Windows) or **Keychain Access** (macOS)
2. Import certificate to **"Trusted Root Certification Authorities"**
3. Trust the certificate 

**Step 3: Verify**

1. Open any HTTPS website
2. Check that no SSL warnings appear
3. View the request in Burp Proxy → HTTP History

---

## 3. Burp Suite Features (Core Tools)

Burp Suite is modular, with each tab serving a specific purpose .

### 3.1 Proxy (The Core Tool)

**Proxy** is the heart of Burp. It intercepts all HTTP/HTTPS traffic .

**Key Sub-tabs:**

| Tab | Purpose |
|---|---|
| **Intercept** | Pause requests/responses, modify them, then forward |
| **HTTP History** | View all requests/responses that passed through |
| **WebSocket History** | Capture WebSocket communication |

**Intercept Workflow:**

```
1. Browser sends request → Burp intercepts
2. Click "Intercept is on" to pause traffic
3. Modify the request (headers, parameters, body)
4. Click "Forward" to send to server
5. Click "Drop" to discard the request
```

**Pro Tip:** Instead of leaving intercept on all the time (which gets annoying), rely on **HTTP History** to inspect past requests, and only turn on intercept when you need to modify a specific request .

### 3.2 Intruder (Automated Attacks)

**Intruder** is Burp's brute-force and fuzzing powerhouse .

**Use Cases:**
- Brute-force login credentials
- Directory enumeration
- Parameter fuzzing
- SQL injection testing
- Rate limiting testing

#### Attack Types

| Attack Type | Description | Best Use Case |
|---|---|---|
| **Sniper** | One payload set, one position at a time | Testing a single parameter  |
| **Battering Ram** | Same payload to multiple positions | When multiple fields need same input |
| **Pitchfork** | Multiple payload sets, parallel processing | Testing interdependent parameters  |
| **Cluster Bomb** | All combinations of multiple payload sets | Exhaustive permutation testing  |

#### Step-by-Step: Brute-Force Login

**Step 1: Capture Login Request**

1. Open browser → Go to login page
2. Enter username: `admin`, password: `password`
3. Submit → Burp intercepts the request

**Step 2: Send to Intruder**

1. Right-click the intercepted request
2. Select **"Send to Intruder"** (or press `Ctrl + I`) 

**Step 3: Configure Payload Positions**

1. Go to **Intruder** tab → **Positions**
2. Clear all positions: Click **"Clear §"**
3. Select the password value in the request
4. Click **"Add §"** to mark it as a payload position

**Step 4: Load Payloads**

1. Go to **Intruder** tab → **Payloads**
2. Payload type: **Simple list**
3. Click **"Load…"** to load a wordlist (e.g., `rockyou.txt`)
4. Or use built-in list: **Add from list → Passwords**

**Step 5: Attack!**

1. Click **"Start Attack"**
2. Burp sends requests with each password
3. Sort results by **Response Length** to find valid password 

### 3.3 Repeater (Manual Testing)

**Repeater** allows you to manually modify and resend requests .

**Workflow:**

1. Right-click any request in Proxy → HTTP History
2. Select **"Send to Repeater"** (or press `Ctrl + R`) 
3. Modify the request (headers, parameters, body)
4. Click **"Send"** to resend
5. View the response

**Use Cases:**
- Test edge cases (long strings, special characters)
- Experiment with parameter manipulation
- Explore hidden functionality

### 3.4 Additional Tabs

| Tab | Purpose |
|---|---|
| **Decoder** | Encode/decode data (Base64, URL, Hex, etc.) |
| **Comparer** | Compare two requests/responses visually |
| **Sequencer** | Test session token randomness |
| **Scanner** | Active vulnerability scanner (Pro only) |

---

## 4. Burp Extensions (BApp Store)

**BApp Store** provides extensions to enhance Burp's functionality .

### How to Install Extensions

1. Open **Extender** tab
2. Click **BApp Store**
3. Browse available extensions
4. Click **"Install"** on any extension

### Recommended Extensions

| Extension | Purpose |
|---|---|
| **Retire.js** | Detect vulnerable JavaScript libraries |
| **Active Scan++** | Extend active scanning capabilities |
| **Autorize** | Test authorization bypasses |
| **Hackbar** | Easier manual testing |
| **Turbo Intruder** | Faster/flexible intruder (requires Python) |

---

## 5. OWASP ZAP

**OWASP ZAP (Zed Attack Proxy)** is a free and open-source web proxy maintained by the OWASP community .

### Why ZAP?

| Pro | Con |
|---|---|
|  Completely free (no paid features) |  UI less intuitive than Burp |
|  Active scanning included |  Some advanced features less polished |
|  Growing community support |  Fewer extensions than Burp |
|  Open-source |  Can be slower on large scans |



### Installing ZAP

**Step 1: Prerequisites**

- Java Runtime Environment (JRE) 8 or later 

**Step 2: Installation**

**Linux (Ubuntu/Debian/Kali):**
```bash
sudo apt install zaproxy -y
```

**Windows/macOS:**
1. Download from: https://www.zaproxy.org/download/
2. Run installer (.exe for Windows, .dmg for macOS)
3. Follow installation wizard

**ZAP via Docker:**
```bash
docker pull ghcr.io/zaproxy/zaproxy:stable
docker run -u zap -p 8080:8080 -p 8090:8090 -i ghcr.io/zaproxy/zaproxy:stable zap-webswing.sh
```
Access at: `http://localhost:8080/zap` 

**Step 3: First Launch**

1. On first run, choose session persistence option 
2. ZAP prompts to generate/import CA certificate
3. Follow similar steps as Burp to install certificate in browser

---

## 6. ZAP Features (Core Tools)

### 6.1 Proxy Setup

**Default ZAP Proxy Settings:**
- Address: `localhost`
- Port: `8080` 

**Configure browser proxy:** Same as Burp (`127.0.0.1:8080`).

**CA Certificate:** Tools → Options → Dynamic SSL Certificates → Generate/Save 

### 6.2 Quick Start Scan

ZAP's **Quick Start** tab provides an easy automated scan :

1. Go to **Quick Start** tab
2. Enter target URL
3. Click **"Attack"** 

This runs:
- **Passive Scan:** Analyzes traffic without sending requests (safe) 
- **Active Scan:** Sends test payloads to detect vulnerabilities 

### 6.3 Spider (Application Crawling)

**Spider** automatically explores the application to find all pages and forms .

**How to use:**
1. Navigate to **Sites** tab
2. Right-click on the target site
3. Select **Attack → Spider**
4. Configure options
5. Click **Start Scan** 

**AJAX Spider:** Extended crawler for single-page applications (SPAs) that load dynamic content via JavaScript .

### 6.4 Manual Test Features

| Feature | Purpose |
|---|---|
| **Breakpoints** | Intercept and modify requests (like Burp Intercept) |
| **Fuzzer** | Automated payload injection |
| **Manual Explore** | Browse while ZAP captures traffic for analysis |



### 6.5 Active Scanner

**Active Scan** sends attack payloads to detect vulnerabilities :

1. Right-click target in **Sites** tab
2. Select **Attack → Active Scan**
3. Configure scan policy
4. Click **Start Scan** 

**What it finds:** SQL injection, XSS, insecure direct object references, and more .

### 6.6 Alerts (Results)

**Alerts** tab displays all findings organized by severity:

| Severity | Color | Action Required |
|---|---|---|
| **High** | 🔴 | Fix immediately |
| **Medium** | 🟠 | Fix as soon as possible |
| **Low** | 🟡 | Fix when possible |
| **Informational** | 🔵 | Review, not urgent |

---

## 7. Burp Suite vs OWASP ZAP (Comparison)

| Feature | Burp Suite | OWASP ZAP |
|---|---|---|
| **Cost** | Free (Community) / Paid (Pro) |  **Free** (always)  |
| **Active Scanner** |  Pro only |  **Included**  |
| **Intruder Speed** | Throttled in Community |  **Full speed**  |
| **UI/UX** |  Very polished |  Less intuitive  |
| **Extensions** |  Extensive |  Limited |
| **Built-in Browser** |  Included |  Not included |
| **Community** |  Very large |  Growing  |
| **Best For** | Advanced pentests, corporate use | Budget-conscious, open-source teams |



### When to Use Which?

| Scenario | Recommended Tool |
|---|---|
| **Learning web pentesting** | Burp Suite (Community) — better UI, more resources  |
| **Advanced corporate pentest** | Burp Suite Pro (paid) — scanner, no throttling |
| **Budget-conscious testing** | OWASP ZAP — completely free  |
| **Automated scanning** | ZAP — scanner included in free version |
| **Mobile/API testing** | Both work well |
| **Quick vulnerability check** | ZAP Quick Start  |

**Pro Tip:** You can use both! Chain them together: Use Burp Community for manual testing, and ZAP for automated scanning .

---

## 8. Hands-On Lab: Intercept and Modify Requests

### Lab Overview

In this lab you will:
1. Set up Burp Suite with proxy
2. Intercept a web request
3. Modify the request to change the server's response
4. Bypass client-side validation

### Target

**PortSwigger Web Security Academy Lab:** "Excessive Trust in Client-Side Controls" 

1. Go to https://portswigger.net
2. Create free account
3. Access: Web Security Academy → Logic Flaws → Excessive Trust in Client-Side Controls
4. Click **"Access Lab"**

### Step 1: Configure Burp

1. Launch Burp Suite Community
2. Go to **Proxy** → **Intercept**
3. Ensure intercept is **ON**
4. Set browser proxy to `127.0.0.1:8080`

### Step 2: Login to Target

1. In browser, go to lab URL
2. Click **"My Account"**
3. Login with credentials: `wiener` / `peter`
4. Click **"Home"**

### Step 3: Add Item to Cart

1. View an expensive item (e.g., "Leather Jacket" - $1,337.00)
2. Click **"Add to cart"**
3. Intercept the request in Burp Proxy 

**Intercepted Request:**
```
POST /cart HTTP/1.1
Host: [lab-id].web-security-academy.net
Cookie: session=xyz123

productId=1&quantity=1&price=1337.00
```

### Step 4: Modify the Request

1. In the intercepted request, find the `price` parameter
2. Change the value to a tiny amount (e.g., `0.01` or `0.13`) 

**Modified Request:**
```
POST /cart HTTP/1.1
Host: [lab-id].web-security-academy.net
Cookie: session=xyz123

productId=1&quantity=1&price=0.01
```

### Step 5: Forward and Verify

1. Click **"Forward"** in Burp 
2. Check your shopping cart in the browser
3. The item should now show the modified price!
4. Place the order to complete the lab

### What You Learned

- **Client-side validation** can be bypassed via proxy
- The server trusts the `price` parameter sent by the browser
- Always validate on the **server-side** as well

---

## 9. Hands-On Lab: Brute-Force Login with Intruder

### Lab Overview

In this lab you will:
1. Intercept a login request
2. Use Burp Intruder to brute-force a password
3. Analyze responses to find the correct password

### Target

**DVWA (Damn Vulnerable Web Application)** or **PortSwigger Lab: Brute Force**

### Step 1: Setup

1. Access DVWA login page (or PortSwigger brute force lab)
2. Open Burp Suite
3. Ensure proxy intercept is **ON**
4. Configure browser proxy

### Step 2: Intercept Login Request

1. Enter username: `admin`
2. Enter password: (any value)
3. Submit login form
4. Intercept the request in Burp

**Intercepted Request:**
```
GET /login.php?username=admin&password=test HTTP/1.1
Host: dvwa.local
Cookie: PHPSESSID=xyz123
```

### Step 3: Send to Intruder

1. Right-click the intercepted request
2. Select **"Send to Intruder"** (`Ctrl + I`) 

### Step 4: Configure Payload Position

1. Go to **Intruder** → **Positions**
2. Click **"Clear §"** (remove all default positions)
3. Highlight the `password` value (`test`)
4. Click **"Add §"** to mark as payload position 

### Step 5: Set Attack Type

- **Attack Type:** `Sniper` (one payload set) 

### Step 6: Load Payloads

1. Go to **Intruder** → **Payloads**
2. **Payload Type:** `Simple list`
3. **Add from list** → `Passwords` (built-in list) 
4. Or load your own: Click **"Load…"** → Select wordlist (e.g., `rockyou.txt`)

### Step 7: Configure Grep Match (Optional)

1. Go to **Options** → **Grep - Match**
2. Click **"Add"**
3. Enter success indicators:
   - `Login successful`
   - `Welcome`
   - `Logout` 

This highlights responses that contain these phrases.

### Step 8: Start Attack

1. Click **"Start Attack"** 
2. Burp sends requests with each password
3. Monitor the **Response Length** column
4. A different length indicates a successful login

### Step 9: Analyze Results

| Request # | Payload | Length | Status |
|---|---|---|---|
| 1 | admin | 2500 | ❌ |
| 2 | password | 2500 | ❌ |
| 3 | letmein | 2500 | ❌ |
| 42 | Secret123 | 1200 | ✅ Success! |

### What You Learned

- Intruder automates brute-force attacks 
- `Sniper` attack type tests one parameter at a time
- Response length differences indicate success
- Rate limiting can be bypassed with IP rotation 

### Practical Example: Brute-Force Scenario

**Target:** `https://target.com/login.php`

**Wordlist (passwords.txt):**
```
password
123456
letmein
admin123
Secret123
```

**Intruder Configuration:**
- Payload Position: `password` parameter
- Attack Type: Sniper
- Payloads: passwords.txt
- Grep Match: `"Welcome"`

**Result:** Request with `Secret123` returned a different response length → successful login.

---

## 10. Summary & Key Takeaways

### Burp Suite

| Feature | Purpose |
|---|---|
| **Proxy** | Intercept and modify HTTP/HTTPS traffic  |
| **Intruder** | Automated attacks (brute force, fuzzing)  |
| **Repeater** | Manual request replay  |
| **Extensions** | Enhance functionality via BApp Store |

### OWASP ZAP

| Feature | Purpose |
|---|---|
| **Proxy** | Intercept and modify traffic  |
| **Spider** | Automatic application crawling  |
| **Active Scan** | Automated vulnerability detection  |
| **Quick Start** | One-click automated scan  |

### Comparison Summary

| Burp Suite | OWASP ZAP |
|---|---|
|  Better UI/UX |  Fully free |
|  More extensions |  Active Scanner included |
|  Intruder throttled (Community) |  Less intuitive UI |



---

## 11. Quiz

1. What port do Burp and ZAP use by default?

2. What is the purpose of a web proxy in security testing?

3. What is the difference between Sniper and Cluster Bomb attack types in Intruder?

4. How do you install the CA certificate for Burp or ZAP?

5. What does the Spider tool do in ZAP?

6. What is the difference between Passive and Active scanning?

7. Why should you use Grep Match in Intruder?

8. How can you bypass rate limiting during a brute-force attack?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Port 8080** — Both Burp and ZAP default to `localhost:8080` .

2. **Intercept and manipulate HTTP/HTTPS traffic** — Web proxies act as MITM tools to analyze and modify requests/responses .

3. **Sniper** tests one payload set at one position. **Cluster Bomb** tests all combinations of multiple payload sets .

4. Visit `http://burpsuite` (Burp) or use ZAP's Dynamic SSL Certs → Generate/Save certificate → Import into browser's trusted certificates .

5. **Crawls the application** — Follows links to map all pages and forms for testing .

6. **Passive** analyzes traffic without sending requests (safe). **Active** sends attack payloads (potentially disruptive) .

7. **Identify successful responses** — Grep Match highlights responses containing success indicators like "Welcome" or "Logout" .

8. **IP rotation** — Use `X-Forwarded-For` header with rotating IPs, or alternate valid logins to reset the counter .

</details>

---

## 12. Resources

### Official Documentation
- [Burp Suite Download](https://portswigger.net/burp/communitydownload) 
- [OWASP ZAP Download](https://www.zaproxy.org/download/) 
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) 

### Extensions
- [Retire.js](https://retirejs.github.io/)
- [Autorize](https://github.com/PortSwigger/autorize)
- [Turbo Intruder](https://github.com/PortSwigger/turbo-intruder)

### Practice Platforms
- [TryHackMe Burp Suite Room](https://tryhackme.com/room/burpsuite)
- [PicoCTF - Intro to Burp](https://play.picoctf.org/practice/challenge/419) 
- [PicoCTF - Cookies (Intruder)](https://play.picoctf.org/practice/challenge/173) 

---

**Previous Module:** [Module 18 — Web Fundamentals & OWASP Top 10 ](Module_18_Web_Fundamentals_&_OWASP_Top_10.md)

**Next Module:** [Module 20 — Web Vulnerabilities Part 1 (Injection & XSS)](Module_20_Web_Vulnerabilities_Part1.md)

---

*Module 19 | Phase 4: Web Application Security | cybersecurity_beginner_to_advance*
