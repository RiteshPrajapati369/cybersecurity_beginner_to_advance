# Module 18: Web Fundamentals & OWASP Top 10

> **Phase 4 — Web Application Security**
> 🟠 Difficulty: Intermediate | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `04_WEB_SECURITY`

---

## Table of Contents

1. [HTTP Protocol Deep Dive](#1-http-protocol-deep-dive)
2. [HTTP Methods](#2-http-methods)
3. [HTTP Headers](#3-http-headers)
4. [HTTP Status Codes](#4-http-status-codes)
5. [Cookies and Sessions](#5-cookies-and-sessions)
6. [Tokens and Authentication](#6-tokens-and-authentication)
7. [Same-Origin Policy and CORS](#7-same-origin-policy-and-cors)
8. [OWASP Top 10 2021](#8-owasp-top-10-2021)
9. [OWASP Top 10 2024 Preview](#9-owasp-top-10-2024-preview)
10. [Hands-On Lab: Analyze HTTP Traffic](#10-hands-on-lab-analyze-http-traffic)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz](#12-quiz)
13. [Resources](#13-resources)

---

## 1. HTTP Protocol Deep Dive

**HTTP (HyperText Transfer Protocol)** is the foundation of data communication on the World Wide Web. It is a simple, text-based request-response protocol that follows a client-server model .

### How HTTP Works

```
┌─────────────────────────────────────────────────────────────────────┐
│                    HTTP REQUEST-RESPONSE CYCLE                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Client (Browser)                     Server (Web Server)           │
│       │                                      │                      │
│       │────── 1. HTTP Request ───────────────→│                     │
│       │      (GET /index.html HTTP/1.1)      │                      │
│       │                                      │                      │
│       │←───── 2. HTTP Response ───────────────│                     │
│       │      (HTTP/1.1 200 OK + HTML)        │                      │
│       │                                      │                      │
└─────────────────────────────────────────────────────────────────────┘
```

### Key Characteristics of HTTP

| Characteristic | Description |
|---|---|
| **Stateless** | Each request is independent; the server doesn't remember previous requests  |
| **Text-Based** | Human-readable format (unlike binary protocols)  |
| **Client-Server** | Clients initiate requests; servers respond  |
| **Application Layer** | Operates at Layer 7 of the OSI model  |

### HTTP Versions

| Version | Year | Key Features |
|---|---|---|
| **HTTP/0.9** | 1991 | Only GET method, no headers, no status codes  |
| **HTTP/1.0** | 1996 | Added headers, status codes, and POST method  |
| **HTTP/1.1** | 1999 | Persistent connections, pipelining, chunked transfers, caching  |
| **HTTP/2** | 2015 | Binary format, multiplexing, header compression, server push  |
| **HTTP/3** | 2022 | Uses QUIC (UDP-based), lower latency, faster recovery  |

### HTTP Message Structure

**Request Structure:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                        HTTP REQUEST STRUCTURE                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  REQUEST LINE:     GET /index.html HTTP/1.1                       │
│                                                                     │
│  HEADERS:          Host: www.example.com                           │
│                   User-Agent: Mozilla/5.0                         │
│                   Accept: text/html                               │
│                   Cookie: session=123abc                          │
│                                                                     │
│  BLANK LINE:       (Carriage Return + Line Feed)                  │
│                                                                     │
│  BODY:             (Optional data for POST/PUT)                   │
│                   {"name": "John", "email": "john@example.com"}   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Response Structure:**

```
┌─────────────────────────────────────────────────────────────────────┐
│                        HTTP RESPONSE STRUCTURE                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  STATUS LINE:      HTTP/1.1 200 OK                                │
│                                                                     │
│  HEADERS:          Date: Thu, 24 Jul 2024 17:36:27 GMT            │
│                   Server: Apache-Coyote/1.1                       │
│                   Content-Type: text/html                         │
│                   Content-Length: 1846                            │
│                   Set-Cookie: session=abc123; HttpOnly            │
│                                                                     │
│  BLANK LINE:       (Carriage Return + Line Feed)                  │
│                                                                     │
│  BODY:             <html>                                         │
│                   <head><title>Example</title></head>             │
│                   <body><h1>Hello World!</h1></body>              │
│                   </html>                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. HTTP Methods

HTTP methods (or verbs) tell the server what action the client wants to perform .

### Core HTTP Methods

| Method | Purpose | Safe? | Idempotent? | Has Body? |
|---|---|---|---|---|
| **GET** | Retrieve a resource |  Yes |  Yes |  No |
| **HEAD** | Retrieve only headers |  Yes |  Yes |  No |
| **POST** | Submit data (create resource) |  No |  No |  Yes |
| **PUT** | Replace/update resource |  No |  Yes |  Yes |
| **PATCH** | Partial update |  No |  No |  Yes |
| **DELETE** | Remove a resource |  No |  Yes |  No |
| **OPTIONS** | Check supported methods |  Yes |  Yes |  No |
| **CONNECT** | Establish a tunnel |  No |  No |  Yes |
| **TRACE** | Debugging (echo request) |  Yes |  Yes |  No |

### Method Deep Dive

#### GET — Retrieve Data

**Purpose:** Fetch a resource from the server. Should have **no side effects** (doesn't change anything on the server) .

**Example:**
```http
GET /products/123 HTTP/1.1
Host: api.example.com
Accept: application/json
```

**Real-World Use:** Loading a webpage, fetching API data, searching.

#### POST — Submit Data

**Purpose:** Send data to the server to create a new resource .

**Example:**
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Content-Length: 48

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Real-World Use:** Form submissions, creating new accounts, posting comments.

#### PUT — Replace Resource

**Purpose:** Replace an entire resource at a specific URL .

**Example:**
```http
PUT /api/users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json
Content-Length: 62

{
  "id": 123,
  "name": "Jane Smith",
  "email": "jane@example.com"
}
```

**Real-World Use:** Updating user profiles, replacing entire records.

#### PATCH — Partial Update

**Purpose:** Apply partial modifications to a resource .

**Example:**
```http
PATCH /api/users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json
Content-Length: 28

{
  "email": "newemail@example.com"
}
```

**Difference from PUT:** PUT replaces the entire resource; PATCH only changes specified fields .

#### DELETE — Remove Resource

**Purpose:** Delete a specified resource.

**Example:**
```http
DELETE /api/users/123 HTTP/1.1
Host: api.example.com
```

---

## 3. HTTP Headers

Headers provide metadata about the request or response .

### Request Headers

| Header | Purpose | Example |
|---|---|---|
| **Host** | Target server hostname (required in HTTP/1.1) | `Host: www.example.com` |
| **User-Agent** | Client software identification | `User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)` |
| **Accept** | Content types the client can handle | `Accept: application/json, text/html` |
| **Cookie** | Session identifiers and stored data | `Cookie: session=abc123; user=john` |
| **Authorization** | Credentials for authentication | `Authorization: Bearer eyJhbGciOiJIUzI1NiIs...` |
| **Content-Type** | Format of request body | `Content-Type: application/json` |
| **Referer** | URL of the previous page | `Referer: https://www.google.com/` |

### Response Headers

| Header | Purpose | Example |
|---|---|---|
| **Server** | Server software identification | `Server: nginx/1.18.0` |
| **Content-Type** | Format of response body | `Content-Type: text/html; charset=utf-8` |
| **Content-Length** | Size of response body in bytes | `Content-Length: 1846` |
| **Set-Cookie** | Send cookie to client | `Set-Cookie: session=abc123; HttpOnly; Secure` |
| **Location** | Redirect URL | `Location: https://example.com/new-page` |
| **Cache-Control** | Caching instructions | `Cache-Control: max-age=3600, public` |

### Security Headers

| Header | Purpose |
|---|---|
| **Strict-Transport-Security (HSTS)** | Enforce HTTPS connections |
| **X-Frame-Options** | Prevent clickjacking (DENY, SAMEORIGIN) |
| **X-Content-Type-Options** | Prevent MIME type sniffing |
| **Content-Security-Policy (CSP)** | Control resources the browser can load |
| **X-XSS-Protection** | Enable browser's XSS filter |

### Custom Headers and Their Security Implications

Attackers often exploit custom headers in vulnerable applications. For example, a response containing a `Vary` header with `X-Forwarded-Host` can reveal that the application trusts user-controlled input, enabling further exploitation .

In the following example, the application trusts the `X-Forwarded-Host` header set by the attacker:

**Request:**
```http
GET / HTTP/1.1
Host: redacted.com
X-Forwarded-Host: attacker.com
```

**Response:**
```http
HTTP/1.1 200 OK
Vary: X-Forwarded-Host,Origin
...
<script src="https://attacker.com/assets/js/jquery.js"></script>
```

The attacker can then poison the cache by causing this malicious response to be cached for subsequent visitors .

### Cache-Poisoned Denial-of-Service (CPDoS)

Attackers can exploit caching behavior to deny service. For instance, with a Cloudflare CDN, an attacker can send a request with an invalid `X-Forwarded-Proto` header to trigger an infinite redirect loop :

**Request:**
```http
GET /js/index.js HTTP/2
Host: redacted.com
X-Forwarded-Proto: http
```

This misconfiguration forces a redirect loop that eventually causes a browser error for all users, effectively causing a denial of service .

---

## 4. HTTP Status Codes

Status codes are three-digit numbers that indicate the outcome of a request .

### Status Code Categories

| Range | Category | Description |
|---|---|---|
| **1xx** | Informational | Request received, continuing |
| **2xx** | Success | Request successfully processed |
| **3xx** | Redirection | Further action needed |
| **4xx** | Client Error | Request cannot be fulfilled |
| **5xx** | Server Error | Server failed to process valid request |

### Common Status Codes

| Code | Meaning | Example |
|---|---|---|
| **200** | OK | Request succeeded |
| **201** | Created | Resource successfully created |
| **204** | No Content | Success, but no response body |
| **301** | Moved Permanently | Resource has new permanent URL |
| **302** | Found | Temporary redirect |
| **304** | Not Modified | Use cached version |
| **400** | Bad Request | Malformed request |
| **401** | Unauthorized | Authentication required or failed |
| **403** | Forbidden | Server refuses to fulfill |
| **404** | Not Found | Requested resource doesn't exist |
| **405** | Method Not Allowed | HTTP method not supported |
| **429** | Too Many Requests | Rate limiting enforced |
| **500** | Internal Server Error | Generic server error |
| **502** | Bad Gateway | Upstream server error |
| **503** | Service Unavailable | Server overloaded or down |

### Real-World Example: 404 Not Found

```http
HTTP/1.1 404 Not Found
Server: nginx/1.18.0
Content-Type: text/html
Content-Length: 162

<html>
<head><title>404 Not Found</title></head>
<body>
<h1>404 Not Found</h1>
<p>The requested URL was not found on this server.</p>
</body>
</html>
```

---

## 5. Cookies and Sessions

HTTP is stateless — the server doesn't remember previous requests. **Cookies** and **sessions** add state to the protocol.

### What Are Cookies?

Cookies are small pieces of data stored by the browser and sent with each request to the same domain .

### Cookie Attributes

| Attribute | Purpose |
|---|---|
| **Name=Value** | Data stored (e.g., `session=abc123`) |
| **Domain** | Which domain the cookie belongs to |
| **Path** | URL path the cookie applies to |
| **Expires/Max-Age** | Cookie lifetime |
| **Secure** | Only send over HTTPS |
| **HttpOnly** | Not accessible via JavaScript (prevents XSS theft) |
| **SameSite** | Restrict cross-site requests (Lax, Strict, None) |

### Session Cookies Example

**Server sends Set-Cookie:**
```http
HTTP/1.1 200 OK
Set-Cookie: session=abc123xyz; HttpOnly; Secure; SameSite=Lax
```

**Client sends Cookie back:**
```http
GET /dashboard HTTP/1.1
Host: example.com
Cookie: session=abc123xyz
```

### Security Best Practices for Cookies

| Practice | Why |
|---|---|
| **Use Secure flag** | Cookie only sent over HTTPS |
| **Use HttpOnly flag** | Prevents JavaScript access (XSS protection) |
| **Use SameSite** | Prevents CSRF attacks |
| **Set expiration** | Limit cookie lifetime |
| **Use random session IDs** | Prevent session prediction |

---

## 6. Tokens and Authentication

### JWT (JSON Web Token)

JWT is a standard for securely transmitting information between parties as a JSON object.

**Structure:** `header.payload.signature`

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Real-World Example:** When you log in, the server issues a JWT. The client sends it with each request in the `Authorization` header.

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### OAuth

OAuth is an open standard for token-based authentication, often used for "Login with Google/Facebook." It enables third-party applications to access user data without sharing passwords.

### SAML (Security Assertion Markup Language)

SAML is an XML-based standard for exchanging authentication and authorization data between identity providers and service providers, commonly used in enterprise SSO systems.

---

## 7. Same-Origin Policy and CORS

### Same-Origin Policy (SOP)

The Same-Origin Policy is a browser security feature that restricts how documents/scripts from one origin can interact with resources from another origin .

**What is an Origin?**

An origin is defined by:
- Protocol (http vs https)
- Domain (example.com)
- Port (80 vs 443)

**Example:** _https://example.com:443_and _http://example.com:80_ are different origins.

### CORS (Cross-Origin Resource Sharing)

CORS is a mechanism that allows servers to specify which origins are permitted to access their resources .

**CORS Response Headers:**

| Header | Purpose |
|---|---|
| `Access-Control-Allow-Origin` | Allowed origins |
| `Access-Control-Allow-Credentials` | Whether credentials can be sent |
| `Access-Control-Allow-Methods` | Allowed HTTP methods |
| `Access-Control-Allow-Headers` | Allowed request headers |

### Real-World Vulnerability: CORS Misconfiguration

A common vulnerability occurs when a server uses `Access-Control-Allow-Origin: *` along with `Access-Control-Allow-Credentials: true` . This allows any website to make authenticated requests to your API and exfiltrate the response.

**Vulnerable Response:**
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

**Attack Exploit:**

An attacker's webpage sends a fetch request to the vulnerable API. The browser includes the victim's credentials (cookies), and the server accepts the cross-origin request. The attacker then extracts the sensitive response from the API .

**Fixing the Vulnerability:**

Only allow specific, trusted origins :

```http
Access-Control-Allow-Origin: https://trusted-frontend.com
```

### CORS Misconfiguration Example

In this real example from a Bugcrowd report, the server responded with _Access-Control-Allow-Origin: *_, allowing any domain to make cross-origin requests .

**Vulnerable Response:**
```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

**Impact:** An attacker-controlled domain can fetch sensitive content from the staging OAuth endpoint, bypassing the Same-Origin Policy and stealing data .

---

## 8. OWASP Top 10 2021

The **OWASP Top 10** is a standard awareness document for developers and web application security professionals .

### A01: Broken Access Control

**What it is:** Failures to enforce user permissions, allowing unauthorized access .

**Real-World Example:** In 2021, Microsoft Power Apps misconfiguration exposed 38 million records, including personal data, due to overly permissive API access .

**How to exploit:** Accessing administrative endpoints by changing a URL parameter (e.g., changing _/user/1_ to _/user/2_ in the browser) .

### A02: Cryptographic Failures

**What it is:** Exposing sensitive data due to weak or missing encryption .

**Real-World Example:** A website storing passwords with weak encryption (e.g., MD5) that can be cracked offline.

### A03: Injection (SQL, NoSQL, OS, LDAP)

**What it is:** Attacker-supplied data is executed as code .

**Real-World Example:** In 2020, SolarWinds Orion was compromised via an SQL injection vulnerability .

**How to exploit:** Entering SQL code into a login form to bypass authentication or dump the database .

### A04: Insecure Design

**What it is:** Security flaws at the architectural level .

**Common Issues:**
- **Client-side validation only:** The server trusts client-provided data without re-validation .
- **Path traversal:** Exposed file parameters allow reading arbitrary files (e.g., _GET /image?filename=../../../etc/passwd_) .
- **HTTP request smuggling:** Frontend/backend disagreement on request boundaries .

### A05: Security Misconfiguration

**What it is:** Default configurations, unnecessary features, and misconfigured headers.

**Examples:**
- Default credentials
- Directory listing enabled
- Unpatched systems
- Missing security headers (HSTS, CSP) 

### A06: Vulnerable and Outdated Components

**What it is:** Using libraries/frameworks with known vulnerabilities.

**Example:** Log4Shell (CVE-2021-44228) — a critical RCE in Apache Log4j that affected millions of applications.

### A07: Identification and Authentication Failures

**What it is:** Weak authentication mechanisms .

**Real-World Example:** The 2022 Uber breach occurred because an attacker obtained stolen credentials and bypassed MFA .

**How to exploit:** Sending a password reset link to an attacker-controlled email or intercepting authentication tokens.

### A08: Software and Data Integrity Failures

**What it is:** Not verifying the integrity of software updates or data.

**Examples:**
- Supply chain attacks
- CI/CD pipeline compromise
- Deserialization attacks

### A09: Security Logging and Monitoring Failures

**What it is:** Insufficient logging that prevents detection of breaches.

**How to exploit:** Attackers can inject fake log entries to disrupt investigations. In one example, URL-encoding was used to add fake log entries, making analysis difficult .

### A10: Server-Side Request Forgery (SSRF)

**What it is:** An attacker tricks the server into making requests to internal networks .

**How to exploit:** Modifying a URL parameter to point to internal services (e.g., _http://localhost/admin/secret_) and reading the response .

---

## 9. OWASP Top 10 2024 Preview

OWASP releases updated Top 10 lists periodically. Here are notable changes expected in the 2024 version:

| Category | Status |
|---|---|
| **Insecure Design** | Moved up to #4 (increased recognition) |
| **Security Misconfiguration** | Remains in Top 10 |
| **Vulnerable and Outdated Components** | Remains in Top 10 |
| **AI/ML Security** | Likely to be added as an honorable mention |

---

## 10. Hands-On Lab: Analyze HTTP Traffic

### Lab Overview

In this lab you will:
1. Use browser Developer Tools to inspect HTTP traffic
2. Analyze HTTP requests and responses
3. Identify security headers
4. Detect vulnerabilities

### Part 1: Open Developer Tools

**Chrome/Edge:**
1. Right-click → Inspect → Network tab
2. OR: F12 → Network tab
3. Check "Preserve log"

**Firefox:**
1. Right-click → Inspect Element → Network tab
2. OR: Ctrl+Shift+E (Windows/Linux), Cmd+Opt+E (macOS)

### Part 2: Analyze HTTP Traffic

**Step 1: Visit any website** (e.g., https://example.com)

**Step 2: View request details**

In the Network tab, click on the first request. You should see:

**Headers tab:**
- Request URL
- Request method (GET)
- Status code (200 OK)
- Request headers (Host, User-Agent, Accept)
- Response headers (Server, Content-Type, Content-Length)

**Response tab:**
- The HTML content returned by the server

### Part 3: Identify Security Headers

For the website you visited, check for these headers in the response:

| Header | Present? | Value |
|---|---|---|
| `Strict-Transport-Security` | | |
| `Content-Security-Policy` | | |
| `X-Frame-Options` | | |
| `X-Content-Type-Options` | | |
| `Referrer-Policy` | | |

### Part 4: Submit a Form and Analyze POST Request

1. Find a login form or search form
2. Fill in the form
3. Open Developer Tools → Network tab
4. Submit the form
5. Look for the POST request

**Document:**
- Request URL
- Request method
- Request body (form data)
- Response status
- Any cookies set

### Part 5: Analyze a Vulnerable Target (Optional)

If you have DVWA or Juice Shop installed:

1. Access the vulnerable application
2. Log in with default credentials
3. Analyze HTTP requests
4. Identify security misconfigurations

### Deliverable

Create `lab_18_http_analysis.md`:

```markdown
# Lab 18: HTTP Traffic Analysis

## Part 1: HTTP Request Analysis
- **URL:** [website]
- **Method:** [GET/POST]
- **Status Code:** [code]
- **Request Headers:** [list]
- **Response Headers:** [list]

## Part 2: Security Headers
| Header | Present | Value |
|---|---|---|
| HSTS | | |
| CSP | | |
| X-Frame-Options | | |

## Part 3: POST Request Analysis
- **URL:** [form URL]
- **Method:** POST
- **Body:** [form data]
- **Response:** [status and details]

## Part 4: Cookie Analysis
| Cookie | Secure | HttpOnly | SameSite |
|---|---|---|---|
| | | | |

## Part 5: Findings
- [List any security issues detected]
- [List any missing security headers]
- [Recommendations]
```

---

## 11. Summary & Key Takeaways

**HTTP Basics:**
- HTTP is stateless, text-based, and follows request-response model
- Major versions: HTTP/1.1 (persistent connections), HTTP/2 (multiplexing), HTTP/3 (QUIC)
- Request: Method + URL + Headers + Body
- Response: Status Code + Headers + Body

**HTTP Methods:**
- GET (read), POST (create), PUT (replace), PATCH (partial update), DELETE (remove)
- HEAD (headers only), OPTIONS (discover capabilities)

**HTTP Status Codes:**
- 2xx: Success (200 OK, 201 Created)
- 3xx: Redirection (301, 302, 304)
- 4xx: Client error (400, 401, 403, 404)
- 5xx: Server error (500, 502, 503)

**Security Headers:**
- HSTS: Enforce HTTPS
- CSP: Control resource loading
- X-Frame-Options: Prevent clickjacking
- X-Content-Type-Options: Prevent MIME sniffing

**CORS:**
- Bypasses SOP to allow cross-origin requests
- Misconfiguration (`Access-Control-Allow-Origin: *` + credentials) enables data theft 

**OWASP Top 10 2021:**
- A01: Broken Access Control (Microsoft Power Apps, 2021) 
- A03: Injection (SolarWinds SQLi, 2020) 
- A07: Authentication Failures (Uber 2022 breach) 
- A05: Security Misconfiguration
- A10: SSRF

---

## 12. Quiz

1. What is the key difference between HTTP and HTTPS?

2. Which HTTP method is used to retrieve a resource without retrieving the body?

3. What is the difference between PUT and PATCH?

4. What does the `HttpOnly` flag on a cookie do?

5. What is the Same-Origin Policy (SOP)?

6. What is the risk of a CORS configuration with `Access-Control-Allow-Origin: *`?

7. What is the OWASP Top 10?

8. What attack vector allows a server to be tricked into making requests to internal networks?

<details>
<summary>📋 Click to reveal answers</summary>

1. **HTTPS** adds SSL/TLS encryption; HTTP transmits data in plain text. HTTPS protects against eavesdropping .

2. **HEAD** — It fetches only the response headers, without the message body .

3. **PUT** replaces the entire resource, **PATCH** applies partial modifications to a resource .

4. The `HttpOnly` flag prevents JavaScript from accessing the cookie, protecting against XSS attacks.

5. **SOP** is a browser security policy that restricts scripts from accessing resources from a different origin .

6. It allows arbitrary origins to request the resource. If combined with `Access-Control-Allow-Credentials: true`, it enables data exfiltration .

7. The **OWASP Top 10** is a standard awareness document for developers and web application security professionals, listing the most critical security risks to web applications .

8. **Server-Side Request Forgery (SSRF)** — An attacker tricks the server into making requests to internal networks .

</details>

---

## 13. Resources

### Official Documentation
- [OWASP Top 10 2021](https://owasp.org/Top10/)
- [HTTP/1.1 RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616)
- [HTTP/2 RFC 7540](https://datatracker.ietf.org/doc/html/rfc7540)
- [CORS Specification](https://fetch.spec.whatwg.org/#http-cors-protocol)

### OWASP Examples
- [OWASP Vulnerabilities Examples (GitHub)](https://github.com/mamalonzo/owasp-vulnerabilities-examples) — Vulnerable apps demonstrating each OWASP Top 10 category 

### Practice Platforms
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [DVWA (Damn Vulnerable Web Application)](https://github.com/digininja/DVWA)

### Developer Tools
- [Chrome DevTools Guide](https://developer.chrome.com/docs/devtools/)
- [Firefox DevTools Guide](https://firefox-source-docs.mozilla.org/devtools-user/)

---

**Previous Module:** [Module 17 — Steganography](../03_SCANNING_&_ENUMERATION/Module_17_Steganography.md)

**Next Module:** [Module 19 — Web Proxy Tool (Burpsuite)](Module_19_Web_Proxy_Tool.md)

---

*Module 18 | Phase 4: Web Application Security | cybersecurity_beginner_to_advance*
