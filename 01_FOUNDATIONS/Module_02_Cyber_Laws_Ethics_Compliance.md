# Module 2: Cyber Laws, Ethics & Compliance

> **Phase 1 — Absolute Foundations**
> 🟢 Difficulty: Beginner | ⏱ Estimated Time: 5–7 hours | 📁 Folder: `01_FOUNDATIONS`

---

## Table of Contents

1. [Why Laws and Ethics Matter in Cybersecurity](#1-why-laws-and-ethics-matter-in-cybersecurity)
2. [Global Cyber Laws](#2-global-cyber-laws)
3. [Regional Cyber Laws](#3-regional-cyber-laws)
4. [Industry Compliance Frameworks](#4-industry-compliance-frameworks)
5. [Common Cyber Crimes and Punishments](#5-common-cyber-crimes-and-punishments)
6. [Bug Bounty Programs](#6-bug-bounty-programs)
7. [NIST Cybersecurity Framework](#7-nist-cybersecurity-framework)
8. [ISO/IEC 27001](#8-isoiec-27001)
9. [Ethics in Hacking](#9-ethics-in-hacking)
10. [Hands-On Lab: Write a Responsible Disclosure Policy](#10-hands-on-lab-write-a-responsible-disclosure-policy)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz: Test Your Knowledge](#12-quiz-test-your-knowledge)
13. [Resources](#13-resources)

---

## 1. Why Laws and Ethics Matter in Cybersecurity

Before you learn a single offensive technique, you must understand **the legal and ethical boundaries** around cybersecurity work.

This isn't just about staying out of prison — though that matters too. It's about being a professional who can be trusted with access to sensitive systems.

### The Core Problem

The same knowledge and tools used by ethical hackers are used by criminals. The **only difference is authorization and intent**.

```
Same tool: Nmap (port scanner)
─────────────────────────────────────────────
White Hat: Scans their client's network with written permission → Legal
Black Hat: Scans a company's network without permission → Illegal
```

### What Can Go Wrong Without This Knowledge?

- You scan a network without permission → **Federal crime in most countries**
- You find a vulnerability and exploit it "just to prove it" → **Criminal charges**
- You access a system using default credentials "because the door was open" → **Still illegal**
- You share your findings publicly without notifying the company first → **Legal liability**
- Your client didn't give you proper written authorization → **You have no defense**

> ⚠️ **Real case:** In 2021, a security researcher found a vulnerability in a Florida water treatment plant's system and reported it. Had he exploited it rather than reported it, he could have faced charges under the CFAA regardless of intent.

### The Ethical Hacker's Motto

> **"With great power comes great responsibility."**

As a cybersecurity professional, you will have skills that can cause serious harm. Professionalism, ethics, and legal awareness are not optional extras — they are the **foundation of the entire career**.

---

## 2. Global Cyber Laws

### 2.1 Budapest Convention on Cybercrime (2001)

**Full name:** Convention on Cybercrime (ETS No. 185)
**Organization:** Council of Europe
**Signed:** November 23, 2001 (Budapest, Hungary)
**Status:** The first and most widely ratified international treaty on cybercrime

#### What It Is

The Budapest Convention is an **international treaty** that aims to harmonize cybercrime laws across countries, improve investigation techniques, and increase cooperation between nations in fighting cybercrime.

Before this treaty, a hacker in Country A could attack a victim in Country B with no legal consequence because the two countries had incompatible or non-existent cybercrime laws.

#### What It Covers

The convention addresses four categories of cybercrime:

| Category | What It Covers | Examples |
|---|---|---|
| Offences against confidentiality, integrity & availability | Unauthorized access, interception, data/system interference | Hacking, DDoS, malware deployment |
| Computer-related offences | Fraud and forgery using computers | Online fraud, identity theft |
| Content-related offences | Child sexual abuse material online | CSAM distribution |
| Copyright offences | Piracy and intellectual property theft | Software piracy, film piracy |

#### Key Provisions

- Countries must **criminalize unauthorized access** to computer systems
- Countries must **criminalize interception** of private communications
- Law enforcement can **collect digital evidence** in a standardized way
- Countries must **cooperate across borders** in cybercrime investigations
- Establishes a **24/7 network** for emergency assistance between countries

#### Signatories

- 67 countries have ratified (including USA, UK, Australia, Japan, Canada, most of EU)
- Notable non-signatories: China, Russia, India (though India is in observer status discussions)

#### Why It Matters for Security Professionals

- If you work internationally, your actions may be governed by the Budapest Convention
- Employers operating globally follow this as a baseline legal framework
- Understanding it helps you understand why cross-border cooperation in cybercrime investigations works the way it does

---

### 2.2 United Nations Resolutions on Cybersecurity

The UN has passed multiple resolutions related to cybersecurity, though international consensus remains difficult:

- **UN GGE (Group of Governmental Experts):** Established norms of responsible state behavior in cyberspace
- **UN Open-Ended Working Group (OEWG):** Broader participation, ongoing discussions on international cybersecurity norms
- Key norm established: **States should not conduct or knowingly support cyberattacks on critical infrastructure of other states during peacetime**

#### Challenges

Russia and China have pushed for a new UN cybercrime treaty that would give governments more control over the internet. Western nations have largely opposed this as a tool for censorship and surveillance.

---

## 3. Regional Cyber Laws

Different countries and regions have their own cybercrime laws. As a security professional you must know the laws of:
1. The country where **you** operate
2. The country where **your client** operates
3. The country where **their servers** are located

---

### 3.1 GDPR — General Data Protection Regulation (Europe, 2018)

**Full name:** Regulation (EU) 2016/679
**Jurisdiction:** European Union + EEA (European Economic Area)
**Effective:** May 25, 2018
**Enforced by:** Data Protection Authorities (DPA) in each EU member state

#### What GDPR Is

GDPR is the world's **strongest data privacy law**. It governs how organizations collect, store, process, and protect the personal data of EU residents — regardless of where the organization is located.

> ⚠️ **Critical point:** GDPR applies to **any organization in the world** that processes data of EU residents. A company in Nepal handling data from a German customer must comply with GDPR.

#### Core Principles of GDPR

| Principle | Meaning |
|---|---|
| Lawfulness, Fairness, Transparency | Must have a legal basis to collect data; must be transparent |
| Purpose Limitation | Collect data only for specified, explicit purposes |
| Data Minimisation | Collect only what's necessary |
| Accuracy | Keep data accurate and up to date |
| Storage Limitation | Don't keep data longer than necessary |
| Integrity & Confidentiality | Protect data with appropriate security |
| Accountability | Demonstrate compliance |

#### Individual Rights Under GDPR

- **Right to access** — Request a copy of your personal data
- **Right to erasure** ("right to be forgotten") — Request deletion of your data
- **Right to portability** — Get your data in a machine-readable format
- **Right to object** — Opt out of certain data processing
- **Right to rectification** — Correct inaccurate data
- **Right to restrict processing** — Limit how your data is used

#### GDPR and Cybersecurity

- Organizations must implement "appropriate technical and organizational measures" to protect data
- **Data breach notification:** Must notify the DPA within **72 hours** of discovering a breach
- Must notify affected individuals if the breach is "likely to result in a high risk"
- Must appoint a **Data Protection Officer (DPO)** if processing large amounts of sensitive data
- Must conduct **Data Protection Impact Assessments (DPIA)** for high-risk processing

#### Penalties

- **Tier 1 (less severe violations):** Up to €10 million or **2% of global annual turnover**, whichever is higher
- **Tier 2 (more severe violations):** Up to €20 million or **4% of global annual turnover**, whichever is higher

**Real fines:**
- Meta (Facebook): €1.2 billion (2023) — for transferring EU user data to the US
- Amazon: €746 million (2021) — for GDPR violations in advertising
- WhatsApp: €225 million (2021) — for lack of transparency

---

### 3.2 CFAA — Computer Fraud and Abuse Act (USA, 1986)

**Full name:** Computer Fraud and Abuse Act, 18 U.S.C. § 1030
**Jurisdiction:** United States Federal Law
**Original:** 1986 (amended multiple times: 1994, 1996, 2001, 2002, 2008)

#### What the CFAA Is

The CFAA is the **primary US federal law** against computer crimes. It prohibits unauthorized access to computers and computer networks.

#### Key Provisions

The CFAA makes it a crime to:

| Act | Example |
|---|---|
| Access a computer without authorization | Hacking into someone's account |
| Exceed authorized access | Employee accessing files outside their job scope |
| Cause damage to a protected computer | Deploying malware |
| Traffic in passwords/access credentials | Selling stolen login credentials |
| Commit fraud involving computers | Online fraud, wire fraud |
| Threaten to damage computers (extortion) | Ransomware, threatening DDoS |
| Access government computers | Any unauthorized access to federal systems |

#### "Without Authorization" — The Controversial Part

The phrase "without authorization" is the most legally contested aspect of the CFAA.

- Courts have historically interpreted it **broadly** — meaning even violating a website's Terms of Service could technically be a CFAA violation
- This has been used against security researchers who "exceeded" their access during legitimate testing
- The **Van Buren v. United States (2021)** Supreme Court ruling narrowed this — "exceeds authorized access" means accessing files/areas the user isn't permitted to access, not just misusing legitimately accessible information

#### Penalties

- **Basic violations:** Up to 1–5 years imprisonment
- **Aggravated violations** (critical infrastructure, government, organized crime): Up to 10–20 years
- **Repeat offenders:** Doubled sentences
- **Civil liability:** Victims can sue for damages

#### Famous CFAA Cases

- **Aaron Swartz (2013):** MIT grad student/activist who downloaded academic articles from JSTOR using an MIT network connection. Faced up to 35 years in prison and $1M in fines. Committed suicide before trial at age 26. Sparked major debate about CFAA overreach.
- **Weev / Andrew Auernheimer (2012):** Accessed AT&T's publicly exposed API to collect 114,000 iPad user email addresses. Convicted, later overturned on jurisdictional grounds.

#### Implication for Security Professionals

- Always get **written authorization** before any security testing
- Even scanning a network without permission can be prosecuted under CFAA
- "I was just testing security" is not a legal defense without prior authorization

---

### 3.3 IT Act — Information Technology Act (India, 2000)

**Full name:** Information Technology Act, 2000 (amended 2008)
**Jurisdiction:** India
**Administered by:** Ministry of Electronics and Information Technology (MeitY)

#### Key Sections for Cybersecurity

| Section | Offence | Punishment |
|---|---|---|
| Section 43 | Unauthorized access, damage to computer systems | Civil liability — compensation up to ₹1 crore |
| Section 66 | Computer-related offences (hacking, data theft) | Up to 3 years imprisonment + fine up to ₹5 lakh |
| Section 66B | Receiving stolen computer resource | Up to 3 years + ₹1 lakh fine |
| Section 66C | Identity theft (using someone's password/digital signature fraudulently) | Up to 3 years + ₹1 lakh fine |
| Section 66D | Cheating by personation using computer | Up to 3 years + ₹1 lakh fine |
| Section 66E | Violation of privacy (capturing/publishing private images) | Up to 3 years + ₹2 lakh fine |
| Section 66F | Cyber terrorism | Up to life imprisonment |
| Section 67 | Publishing obscene material | Up to 5 years + ₹10 lakh fine |
| Section 69 | Government interception/monitoring of information | — |
| Section 70 | Unauthorized access to protected systems (government) | Up to 10 years + fine |
| Section 72 | Breach of confidentiality and privacy by authorized persons | Up to 2 years + ₹1 lakh fine |

#### CERT-In (Indian Computer Emergency Response Team)

- Government body under MeitY that responds to cybersecurity incidents
- In 2022, CERT-In issued a directive requiring organizations to report cybersecurity incidents within **6 hours** (one of the fastest mandatory timelines in the world)
- Organizations must maintain logs for 180 days
- VPN providers and cloud companies must maintain customer data for 5 years

---

### 3.4 Cybersecurity Law (China, 2017)

**Full name:** 网络安全法 (Cybersecurity Law of the People's Republic of China)
**Effective:** June 1, 2017
**Enforced by:** Cyberspace Administration of China (CAC)

#### Key Requirements

- **Network operators** must implement security protection measures
- **Critical information infrastructure** operators face stricter obligations
- **Data localization:** Personal and important data collected in China must be stored within China
- **Security reviews:** Products and services used in critical infrastructure must pass security review
- **Real-name registration:** Users must register with real identity on internet platforms
- **Assistance to government:** Network operators must assist government with investigations

#### Implications for International Companies

- Foreign companies operating in China must store Chinese user data in China
- Government can access data stored in China
- Security products must be approved by Chinese authorities

#### Additional Laws (China)

- **Data Security Law (DSL, 2021):** Classifies data by importance to national security; regulates data transfers
- **Personal Information Protection Law (PIPL, 2021):** China's equivalent of GDPR

---

### 3.5 Other Notable Regional Laws

| Country/Region | Law | Key Focus |
|---|---|---|
| UK | Computer Misuse Act (CMA) 1990 | Unauthorized computer access; 3 offence tiers |
| UK | UK GDPR | Post-Brexit version of EU GDPR |
| Australia | Privacy Act 1988 + Australian Privacy Principles | Data protection; Notifiable Data Breaches scheme |
| Canada | PIPEDA (Personal Information Protection and Electronic Documents Act) | Data privacy in commercial activities |
| Singapore | Computer Misuse Act + Personal Data Protection Act (PDPA) | Cybercrime + data privacy |
| UAE | Federal Law No. 5 of 2012 on Combating Cybercrimes | Broad cybercrime provisions |
| Brazil | LGPD (Lei Geral de Proteção de Dados) | Data protection, modeled on GDPR |
| South Korea | Personal Information Protection Act (PIPA) | One of strictest data laws in Asia |

---

## 4. Industry Compliance Frameworks

Compliance frameworks are **sets of rules and standards** that organizations in specific industries must follow to protect data and systems. Violating them results in fines, loss of operating licenses, or lawsuits.

---

### 4.1 HIPAA — Health Insurance Portability and Accountability Act (Healthcare)

**Jurisdiction:** USA
**Year:** 1996 (Security Rule 2003)
**Enforced by:** Department of Health and Human Services (HHS) Office for Civil Rights (OCR)
**Applies to:** Healthcare providers, health plans, healthcare clearinghouses ("covered entities") and their business associates

#### What It Protects

**PHI (Protected Health Information):** Any information related to a person's health, treatment, or payment that could identify them.

Examples: Medical records, diagnoses, prescriptions, test results, health insurance information, billing records

#### Key Rules

**Privacy Rule:**
- Governs how PHI can be used and disclosed
- Patients have the right to access their medical records
- PHI can only be shared with authorized parties (treating physicians, insurers, etc.)

**Security Rule:**
- Applies specifically to **ePHI (electronic PHI)**
- Three safeguards required:

| Safeguard Type | Examples |
|---|---|
| Administrative | Security policies, workforce training, access management |
| Physical | Facility access controls, workstation security, device controls |
| Technical | Access controls, audit logs, encryption, automatic logoff |

**Breach Notification Rule:**
- Covered entities must notify affected individuals within **60 days** of discovering a breach
- If breach affects 500+ individuals in a state, must notify the media
- Must notify HHS immediately for breaches affecting 500+ individuals

#### Penalties

| Tier | Situation | Fine Range |
|---|---|---|
| Tier 1 | Did not know (and would not have known) | $100–$50,000 per violation |
| Tier 2 | Reasonable cause, not willful neglect | $1,000–$50,000 per violation |
| Tier 3 | Willful neglect, corrected | $10,000–$50,000 per violation |
| Tier 4 | Willful neglect, not corrected | $50,000 per violation, up to $1.9M/year |

**Real fines:**
- Anthem Inc.: $16 million (2018) — breach of 79 million records
- Premera Blue Cross: $6.85 million (2020)

#### For Security Professionals

HIPAA penetration testers must:
- Have a signed Business Associate Agreement (BAA)
- Understand what constitutes ePHI
- Ensure testing doesn't expose PHI
- Document all findings with care (test reports may themselves contain PHI)

---

### 4.2 PCI-DSS — Payment Card Industry Data Security Standard (Payments)

**Full name:** Payment Card Industry Data Security Standard
**Governed by:** PCI Security Standards Council (founded by Visa, Mastercard, Amex, Discover, JCB)
**Current version:** PCI-DSS v4.0 (released March 2022)
**Applies to:** Any organization that stores, processes, or transmits **cardholder data**

#### The 12 Requirements of PCI-DSS

Organized into 6 control objectives:

**Build and Maintain a Secure Network:**
1. Install and maintain network security controls (firewalls)
2. Apply secure configurations to all system components

**Protect Account Data:**
3. Protect stored account data (encrypt, mask, don't store unnecessary data)
4. Protect cardholder data with strong cryptography during transmission

**Maintain a Vulnerability Management Program:**
5. Protect all systems and networks from malicious software
6. Develop and maintain secure systems and software

**Implement Strong Access Control Measures:**
7. Restrict access to system components by business need to know
8. Identify users and authenticate access
9. Restrict physical access to cardholder data

**Regularly Monitor and Test Networks:**
10. Log and monitor all access to system components and cardholder data
11. Test security of systems and networks regularly

**Maintain an Information Security Policy:**
12. Support information security with organizational policies and programs

#### Compliance Levels

| Level | Transaction Volume | Requirements |
|---|---|---|
| Level 1 | >6 million transactions/year | Annual on-site audit by Qualified Security Assessor (QSA) |
| Level 2 | 1–6 million/year | Annual Self-Assessment Questionnaire (SAQ) + quarterly scans |
| Level 3 | 20,000–1 million e-commerce/year | Annual SAQ + quarterly scans |
| Level 4 | <20,000 e-commerce/year | Annual SAQ + quarterly scans recommended |

#### Penalties for Non-Compliance

- Fines from payment brands: $5,000–$100,000 per month
- Cost of forensic investigation after a breach
- Increased transaction fees
- Termination of ability to process card payments

#### For Security Professionals

- Penetration testing is a **PCI-DSS requirement** (Requirement 11.4)
- Must test at least annually or after significant changes
- Specific scope: cardholder data environment (CDE) and connected systems

---

### 4.3 SOX — Sarbanes-Oxley Act (Finance)

**Jurisdiction:** USA
**Year:** 2002 (passed in response to Enron and WorldCom accounting scandals)
**Enforced by:** Securities and Exchange Commission (SEC)
**Applies to:** Publicly traded companies in the USA + their subsidiaries + foreign companies listed on US exchanges

#### IT Security Relevance

SOX is primarily a financial governance law, but it has major IT security implications:

**Section 302:** CEO and CFO must personally certify the accuracy of financial reports. If IT systems are compromised and reports are inaccurate as a result, they are personally liable.

**Section 404:** Companies must assess and report on the effectiveness of internal controls over financial reporting. IT general controls (access controls, change management, data integrity) are a core part of this.

**Section 802:** Penalties for destroying or altering documents that are subject to federal investigation.

#### IT Controls Required by SOX

- **Access controls:** Only authorized users can access financial systems
- **Audit trails:** All changes to financial data must be logged
- **Change management:** Changes to financial systems must be documented and approved
- **Data integrity:** Controls to prevent unauthorized modification of financial data
- **Incident response:** Procedures for responding to security incidents that affect financial data

#### Penalties

- **Knowingly certifying false financial statements:** Up to $1 million fine and 10 years imprisonment
- **Willfully certifying false statements:** Up to $5 million and 20 years imprisonment

---

### 4.4 FISMA — Federal Information Security Management Act (US Government)

**Jurisdiction:** USA
**Year:** 2002 (updated by FISMA 2014)
**Applies to:** All US federal agencies and contractors who work with federal information systems

#### Key Requirements

- All federal agencies must develop, document, and implement an information security program
- Must categorize information systems by impact level (Low, Moderate, High) using FIPS 199
- Must implement security controls from NIST SP 800-53
- Must conduct annual security reviews
- Must report cybersecurity incidents to US-CERT within 1 hour for major incidents
- Must develop and maintain Continuity of Operations Plans (COOP)

#### Compliance Levels (FIPS 199)

| Level | Definition | Example |
|---|---|---|
| Low | Limited adverse effect | Loss of public-facing non-sensitive data |
| Moderate | Serious adverse effect | Loss of financial records |
| High | Severe or catastrophic effect | Loss of classified data; national security impact |

---

### Compliance Framework Summary

| Framework | Industry | Key Focus | Jurisdiction |
|---|---|---|---|
| GDPR | Any (EU data) | Data privacy and protection | EU |
| HIPAA | Healthcare | Patient health data (PHI) | USA |
| PCI-DSS | Payments | Cardholder data | Global |
| SOX | Public companies | Financial data integrity | USA |
| FISMA | Government | Federal information systems | USA |
| ISO 27001 | Any | Information security management | Global |
| NIST CSF | Any | Cybersecurity risk management | USA (global adoption) |

---

## 5. Common Cyber Crimes and Punishments

Understanding what constitutes a cybercrime — and the real consequences — is essential for staying on the right side of the law.

### 5.1 Major Cybercrime Categories

#### Unauthorized Access (Hacking)

- **What it is:** Accessing a computer, network, or system without permission
- **USA (CFAA):** Up to 5 years (10–20 for aggravated)
- **UK (CMA):** Up to 2 years for basic; up to 10 years if intending to commit further offences
- **India (IT Act §66):** Up to 3 years + fine
- **Famous case:** Kevin Mitnick — served 5 years in federal prison for hacking into corporate networks

---

#### Identity Theft

- **What it is:** Using another person's personal information without consent
- **USA:** Up to 15 years under Identity Theft Enforcement and Restitution Act
- **India (IT Act §66C):** Up to 3 years + ₹1 lakh fine
- **Famous case:** Albert Gonzalez — stole 170 million credit card numbers; sentenced to 20 years

---

#### Ransomware / Extortion

- **What it is:** Encrypting a victim's data and demanding payment for decryption
- **USA:** Up to 20 years + asset forfeiture under multiple statutes
- **Russia has extradited few ransomware criminals; US has charged many in absentia**
- **Famous case:** Colonial Pipeline attackers (DarkSide) — US recovered ~$2.3M, Russia ultimately pressured DarkSide to shut down

---

#### Phishing / Online Fraud

- **What it is:** Deceptive messages to steal credentials or money
- **USA:** Up to 20 years for wire fraud
- **Famous case:** Evaldas Rimasauskas — Phished Facebook and Google out of $123 million; sentenced to 5 years

---

#### Cyberstalking / Harassment

- **What it is:** Using electronic communications to stalk, harass, or threaten
- **USA:** Up to 5 years under federal cyberstalking statute
- **India (IT Act §66E, §67):** Up to 3–5 years

---

#### DDoS Attacks

- **What it is:** Flooding a server to make it unavailable
- **USA:** Up to 10 years under CFAA
- **Famous case:** Mirai botnet operators pleaded guilty; received probation in exchange for cooperation with FBI

---

#### Child Sexual Abuse Material (CSAM)

- **Most severe category** across all jurisdictions
- **USA:** Mandatory minimum 5 years; up to 20–30 years per image/video
- Internationally, virtually every country has severe penalties

---

### 5.2 Punishment Comparison by Country

| Crime | USA | UK | India | EU (avg.) |
|---|---|---|---|---|
| Unauthorized access | 5–20 years | 2–10 years | 3 years | 1–5 years |
| Data theft | 5–15 years | 2–10 years | 3 years | 2–5 years |
| Ransomware | Up to 20 years | Up to 10 years | 3–7 years | 5–10 years |
| Identity theft | Up to 15 years | Up to 10 years | Up to 3 years | 2–5 years |
| CSAM | 5–30 years | 5–10 years | 3–7 years | 5–15 years |
| Cyber terrorism | Life imprisonment | Life imprisonment | Life imprisonment | 15+ years |

> ⚠️ **Note:** Penalties listed are maximums and vary greatly by circumstance, jurisdiction, and prior offenses. Always consult a legal professional.

---

### 5.3 The "I Was Just Testing" Defense

This is one of the most common mistakes made by aspiring security professionals:

> "I found a vulnerability in a company's website and accessed their database to prove it was real. I wasn't going to do anything bad with it."

**This is still a crime.** Authorization, not intent, is what determines legality.

The courts have generally held:
- "Ethical" intent does not override the lack of authorization
- Accessing data to "prove" a vulnerability is as illegal as accessing it maliciously
- The damage caused (even just reading data) is still a criminal act

**The right way:**
1. **Do not access** beyond confirming the vulnerability exists (e.g., don't extract the actual data — just confirm the injection works)
2. **Document** the vulnerability with minimal evidence
3. **Report** through proper channels (responsible disclosure)
4. **Wait** for acknowledgment before any public disclosure

---

## 6. Bug Bounty Programs

Bug bounty programs are **formal programs run by organizations** that invite security researchers to find and report vulnerabilities in exchange for **monetary rewards, recognition, or both**.

They are the **legal framework for authorized vulnerability research** outside of direct employment or contracted penetration testing.

---

### 6.1 How Bug Bounties Work

```
1. Company publishes a bug bounty program (scope, rules, rewards)
        ↓
2. Researcher reads scope carefully — what is in-scope vs out-of-scope
        ↓
3. Researcher tests ONLY in-scope systems, within the defined rules
        ↓
4. Researcher finds a vulnerability
        ↓
5. Researcher writes a clear, detailed report with reproduction steps
        ↓
6. Researcher submits report through the platform (HackerOne, Bugcrowd, etc.)
        ↓
7. Company triages and validates the report
        ↓
8. Company fixes the vulnerability
        ↓
9. Researcher receives reward (bounty payment)
        ↓
10. After a defined period, researcher may publish a write-up (coordinated disclosure)
```

---

### 6.2 Major Bug Bounty Platforms

| Platform | Notable Programs | Notes |
|---|---|---|
| **HackerOne** | US DoD, Twitter/X, Uber, Spotify, GitHub | Largest platform; has government programs |
| **Bugcrowd** | Tesla, Mastercard, Atlassian, Netgear | Strong enterprise focus |
| **Intigriti** | European companies | European focus |
| **Synack** | Government, enterprise (invite-only) | Vetted researchers only |
| **Open Bug Bounty** | Various | Focuses on XSS and CSRF |
| **Immunefi** | Blockchain / DeFi protocols | Crypto bounties — some pay $1M+ |

---

### 6.3 Reward Ranges (Industry Averages)

| Severity | CVSS Score | Typical Reward |
|---|---|---|
| Informational | N/A | $0–$50 or "hall of fame" |
| Low | 0.1–3.9 | $50–$500 |
| Medium | 4.0–6.9 | $500–$2,000 |
| High | 7.0–8.9 | $2,000–$10,000 |
| Critical | 9.0–10.0 | $10,000–$100,000+ |

**Record Bounties:**
- **$2 million** — Immunefi (Wormhole blockchain bridge exploit, 2022)
- **$1 million** — Apple Security Bounty (zero-click iMessage exploit)
- **$500,000** — Google (Android full chain exploit)
- **$450,000** — Apple (various critical vulnerabilities)

---

### 6.4 Responsible Disclosure (Coordinated Vulnerability Disclosure)

**Responsible disclosure** is the process of privately notifying a vendor about a vulnerability and giving them time to fix it before publicly disclosing.

#### The Standard Process

1. **Discovery** — Researcher finds a vulnerability
2. **Private notification** — Report sent directly to the affected organization
3. **Acknowledgment** — Organization acknowledges receipt (should be within 7 days)
4. **Remediation period** — Typically **90 days** (Google Project Zero standard)
5. **Verification** — Researcher verifies the fix
6. **Public disclosure** — After fix is deployed, researcher may publish details

#### Why 90 Days?

Google Project Zero established the 90-day standard. The reasoning:
- Long enough for vendors to properly fix the issue
- Short enough that vendors can't indefinitely delay fixing known vulnerabilities
- Creates accountability pressure

**What happens if the vendor doesn't fix it in 90 days?** The researcher discloses anyway (with notification). This is called a "deadline disclosure" and is accepted practice in the security community.

#### Full Disclosure vs Responsible Disclosure vs No Disclosure

| Approach | What It Means | Pros | Cons |
|---|---|---|---|
| **No disclosure** | Never tell anyone | Avoids confrontation | Leaves users vulnerable |
| **Full disclosure** | Immediate public release | Maximum pressure on vendor | Can enable attacks before patch |
| **Responsible/Coordinated** | Private first, then public after fix | Protects users; gives vendor time | Vendor might not act |

---

### 6.5 Scope and Rules in Bug Bounty Programs

**Always read the scope before testing.** The scope defines what is legally covered.

**Typical in-scope items:**
- Main web application (e.g., `*.company.com`)
- Mobile applications
- Specific API endpoints

**Typical out-of-scope items:**
- Physical attacks
- Social engineering of employees
- Third-party services the company uses
- Denial of Service testing
- Other users' accounts (no testing on real user data)
- Infrastructure not owned by the company

**Common rules:**
- Do not destroy or modify data
- Do not access data belonging to other users
- Do not cause service disruption
- Report vulnerabilities promptly
- Do not publicly disclose before coordinated disclosure period

> ⚠️ **Testing out-of-scope assets can void your legal protection, even in a bug bounty program. Read the scope carefully.**

---

## 7. NIST Cybersecurity Framework

**Full name:** NIST Cybersecurity Framework (CSF)
**Organization:** National Institute of Standards and Technology (USA)
**Current version:** CSF 2.0 (released February 2024)
**Status:** Voluntary framework, but widely adopted globally and required for US federal contractors

---

### 7.1 What the NIST CSF Is

The NIST CSF is a **risk-based framework** that gives organizations a common language and systematic approach to managing cybersecurity risk.

It doesn't tell you exactly what to do — it provides a structure for making cybersecurity decisions that fit your organization's risk tolerance and needs.

**Who uses it:**
- Small businesses to Fortune 500 companies
- US government agencies
- Healthcare, finance, energy, transportation
- Organizations in 100+ countries

---

### 7.2 CSF 2.0 Core Functions

CSF 2.0 has **6 core functions** (CSF 1.1 had 5 — "Govern" was added in 2.0):

```
GOVERN → IDENTIFY → PROTECT → DETECT → RESPOND → RECOVER
```

#### GOVERN (New in CSF 2.0)

**Purpose:** Establish and monitor the organization's cybersecurity risk management strategy, expectations, and policy.

**Key activities:**
- Define cybersecurity roles and responsibilities
- Establish cybersecurity policies and risk tolerance
- Integrate cybersecurity into enterprise risk management
- Supply chain risk management

**Why it was added:** Organizations need top-level leadership commitment for cybersecurity to be effective. Governance ensures it's treated as a business issue, not just a technical one.

---

#### IDENTIFY

**Purpose:** Understand your organization's systems, assets, data, and risks.

**Key activities:**
- **Asset Management** — Inventory all hardware, software, data, and services
- **Risk Assessment** — Identify threats, vulnerabilities, and their potential impact
- **Business Environment** — Understand your organization's role and critical functions
- **Governance** — Policies, procedures, and legal requirements
- **Supply Chain Risk** — Understand dependencies on external parties

**Simple principle:** You cannot protect what you don't know you have.

---

#### PROTECT

**Purpose:** Implement safeguards to limit the impact of potential cybersecurity events.

**Key activities:**
- **Identity Management and Access Control** — MFA, least privilege, account management
- **Awareness and Training** — Employee security education
- **Data Security** — Encryption, data classification, backup
- **Information Protection Processes** — Change management, vulnerability management
- **Protective Technology** — Firewalls, IDS/IPS, endpoint protection

---

#### DETECT

**Purpose:** Identify when a cybersecurity event is occurring.

**Key activities:**
- **Anomalies and Events** — Monitor for unusual activity
- **Security Continuous Monitoring** — SIEM, network monitoring, endpoint monitoring
- **Detection Processes** — Define what "normal" looks like so you can spot "abnormal"

---

#### RESPOND

**Purpose:** Take action when a cybersecurity incident is detected.

**Key activities:**
- **Response Planning** — Have an Incident Response plan ready before an incident happens
- **Communications** — Internal (legal, management) and external (regulators, public, customers)
- **Analysis** — Understand what happened and its scope
- **Mitigation** — Contain the incident and prevent spread
- **Improvements** — Learn from the incident

---

#### RECOVER

**Purpose:** Restore capabilities and services that were impaired by an incident.

**Key activities:**
- **Recovery Planning** — Restoration procedures (backups, DR plans)
- **Improvements** — Incorporate lessons learned
- **Communications** — Update stakeholders on recovery progress

---

### 7.3 NIST CSF Tiers

The framework defines 4 implementation tiers that describe the maturity of an organization's cybersecurity program:

| Tier | Name | Description |
|---|---|---|
| Tier 1 | Partial | Ad hoc, reactive; cybersecurity isn't a priority |
| Tier 2 | Risk Informed | Awareness exists but not organization-wide; informal policies |
| Tier 3 | Repeatable | Formal policies, regularly updated, organization-wide awareness |
| Tier 4 | Adaptive | Adaptive, continuously improving; threat intelligence integrated |

Most organizations target **Tier 3** as a baseline. Tier 4 is typically only seen in mature large enterprises.

---

### 7.4 NIST CSF vs NIST SP 800-53

| | NIST CSF | NIST SP 800-53 |
|---|---|---|
| Type | High-level framework | Detailed control catalog |
| Audience | Any organization | US federal agencies (and contractors) |
| Flexibility | High — adapt to your context | Prescriptive — specific controls required |
| Depth | Strategic | Tactical (hundreds of specific controls) |
| Use case | "What should our program look like?" | "What specific controls do we need?" |

---

## 8. ISO/IEC 27001

**Full name:** ISO/IEC 27001 — Information Security Management Systems (ISMS)
**Organization:** International Organization for Standardization (ISO) + International Electrotechnical Commission (IEC)
**Current version:** ISO/IEC 27001:2022
**Status:** International standard — organizations can be **certified** against it by accredited auditors

---

### 8.1 What ISO 27001 Is

ISO 27001 is an **internationally recognized standard** for establishing, implementing, maintaining, and continually improving an **Information Security Management System (ISMS)**.

An ISMS is the collection of policies, procedures, and controls that an organization uses to manage information security risks.

**The key difference from NIST CSF:**
- NIST CSF is a **framework** (voluntary guidance)
- ISO 27001 is a **standard** — organizations can be **certified** against it by an external auditor
- ISO 27001 certification tells customers: "We have been independently verified to manage information security systematically"

---

### 8.2 ISO 27001 Structure

The standard has two main parts:

**Part 1: Clauses (4–10) — The ISMS Requirements**

| Clause | Topic |
|---|---|
| 4 | Context of the organization |
| 5 | Leadership and commitment |
| 6 | Planning (risk assessment, risk treatment) |
| 7 | Support (resources, competence, communication) |
| 8 | Operation (implement risk treatment plan) |
| 9 | Performance evaluation (monitoring, audits, management review) |
| 10 | Improvement (nonconformities, corrective actions, continual improvement) |

**Part 2: Annex A — Controls**

ISO 27001:2022 Annex A contains **93 controls** organized into 4 themes:

| Theme | Controls | Examples |
|---|---|---|
| Organizational (A.5) | 37 controls | Information security policies, asset management, supplier relationships |
| People (A.6) | 8 controls | Screening, terms of employment, security awareness |
| Physical (A.7) | 14 controls | Physical entry controls, clear desk policy, equipment security |
| Technological (A.8) | 34 controls | Access control, encryption, vulnerability management, backup |

---

### 8.3 Risk-Based Approach

ISO 27001 is fundamentally **risk-based**. The core process is:

1. **Identify information assets** (what needs protecting)
2. **Identify threats and vulnerabilities** (what could go wrong)
3. **Assess risk** (likelihood × impact)
4. **Select controls** from Annex A (or elsewhere) to mitigate risks
5. **Implement controls**
6. **Monitor and review** — are controls working?
7. **Continually improve**

---

### 8.4 ISO 27001 Certification Process

1. **Gap analysis** — Compare current state to ISO 27001 requirements
2. **Implement ISMS** — Fill gaps; implement required policies, procedures, controls
3. **Internal audit** — Self-assessment of ISMS
4. **Management review** — Leadership reviews ISMS performance
5. **Stage 1 audit (Documentation review)** — External auditor reviews documentation
6. **Stage 2 audit (Certification audit)** — Auditor verifies implementation
7. **Certification issued** — Valid for 3 years
8. **Surveillance audits** — Annual check-ins (Years 1 and 2)
9. **Recertification audit** — Full audit at Year 3

---

### 8.5 Why ISO 27001 Matters

- Customers (especially enterprises and governments) increasingly require vendors to be ISO 27001 certified
- Demonstrates that security is managed systematically, not ad hoc
- Reduces cyber insurance premiums
- Required for some government contracts (UK G-Cloud, etc.)
- Aligns with GDPR requirements ("appropriate technical and organizational measures")

---

### NIST CSF vs ISO 27001 Comparison

| | NIST CSF 2.0 | ISO 27001:2022 |
|---|---|---|
| Origin | USA | International |
| Type | Framework (guidance) | Standard (certifiable) |
| Certification | No | Yes |
| Cost | Free | Paid (audit + certification fees) |
| Prescriptiveness | Flexible | Structured requirements |
| Best for | Risk management strategy | Demonstrating compliance to customers |
| Geographic popularity | USA, global | Global (especially Europe, Asia) |

---

## 9. Ethics in Hacking

Technical skill without ethics is dangerous. This section covers the **ethical principles** that separate professionals from criminals.

---

### 9.1 Core Ethical Principles

#### 1. Authorization is Everything

**Never access any system without explicit, written permission from the owner.**

This is non-negotiable. No exceptions.

- "It was publicly accessible" → Not an excuse
- "I was just testing" → Not an excuse
- "I found the credentials online" → Not an excuse
- "The login page was default" → Not an excuse

**The written authorization (scope of work / rules of engagement) is your legal protection.** Without it, you have none.

---

#### 2. Do No Harm

Your testing should not cause disruption, data loss, or damage to the target's systems.

**In practice:**
- Don't delete or modify data — only read what's necessary to prove the vulnerability
- Avoid DoS techniques that could bring systems down unless explicitly authorized
- Test in a staging environment whenever possible
- Communicate immediately if testing causes unintended disruption
- Be careful with production systems — mistakes can have real-world consequences

---

#### 3. Respect Privacy

When you gain access to systems, you will often see data that doesn't belong to you.

**Ethical handling:**
- Do not read personal or sensitive data beyond what's necessary
- Do not copy or exfiltrate real user data (use synthetic test data)
- Do not share or discuss client data with unauthorized parties
- Treat what you see with discretion — you are a guest, not an owner

---

#### 4. Honesty and Integrity

**With clients:**
- Report findings accurately — don't exaggerate to seem impressive, don't minimize to avoid awkward conversations
- Report all findings, including ones that might reflect poorly on your work
- Be honest about your skill level and the limitations of your testing

**With yourself:**
- Don't claim certifications or experience you don't have
- Don't take on engagements beyond your competence
- Keep learning — the field changes constantly

---

#### 5. Confidentiality

As a security tester, you will be trusted with:
- Details of the client's network architecture
- Vulnerabilities in their systems
- Business-sensitive information
- Employee names, credentials, and behavior

**This information must be protected with the same care as the client's own security team.**
- Use encrypted storage for client data
- Communicate test results only through secure channels
- Destroy data after the engagement (as agreed in contract)
- Don't discuss client engagements publicly without permission

---

#### 6. Responsible Disclosure

If you find vulnerabilities — whether on a formal engagement, a bug bounty, or during personal research:
- Report them through the appropriate channel
- Give the vendor time to fix before public disclosure
- Don't use vulnerabilities for personal gain
- Don't share vulnerability details with unauthorized parties

---

### 9.2 The Professional Code of Conduct

Most cybersecurity professional organizations have a code of ethics:

**EC-Council (CEH):** Members must not use skills for unauthorized purposes; protect client confidentiality; disclose conflicts of interest; continually improve skills

**ISC² (CISSP):** Four canons:
1. Protect society, the common good, necessary public trust and confidence, and the infrastructure
2. Act honorably, honestly, justly, responsibly, and legally
3. Provide diligent and competent service to principals
4. Advance and protect the profession

**ISACA (CISM, CISA):** Support the implementation and encouragement of appropriate standards, procedures, and controls; perform their duties with objectivity and due diligence

---

### 9.3 Permission Boundaries

Understanding exactly what you're authorized to do — and where that authorization ends — is critical.

**Scope creep during testing:**

Imagine you're testing `app.company.com` and you discover a link to `legacy.company.com`. That second domain is **not in scope** even though the client owns it. You must:
1. Stop immediately and do not test the out-of-scope system
2. Notify the client that you discovered this system
3. Ask if they want to add it to scope

**Third-party systems:**

Many applications integrate with third-party services (payment processors, CDNs, analytics). These are never in scope unless explicitly stated — they are owned by a different company.

**Social engineering:**

If your scope says "web application testing," you do not have authorization to call employees, send phishing emails, or attempt to tailgate into the building — unless social engineering is explicitly listed.

---

### 9.4 The Dual-Use Dilemma

Virtually every cybersecurity tool has both offensive and defensive uses:

| Tool | Offensive Use | Defensive Use |
|---|---|---|
| Nmap | Map a network you're not authorized to access | Audit your own network's exposure |
| Metasploit | Exploit unpatched systems for financial gain | Test your own systems for exploitable vulnerabilities |
| Mimikatz | Dump credentials from a breached system | Test if your credential protections are effective |
| Wireshark | Capture credentials on a network you don't own | Monitor your own network for suspicious traffic |
| SQLmap | Dump a production database | Test your own application for SQL injection |

The tool is neutral. **Authorization and intent determine legality and ethics.**

---

### 9.5 Staying Out of Legal Trouble — Practical Checklist

Before any security engagement:

- [ ] Get signed written authorization (scope of work / rules of engagement)
- [ ] Ensure the authorizing person actually has authority to grant permission
- [ ] Confirm the scope in writing — what systems are in scope?
- [ ] Confirm the timeline — when can you test?
- [ ] Confirm emergency contacts — who do you call if something breaks?
- [ ] Confirm out-of-scope items explicitly
- [ ] Confirm data handling requirements
- [ ] Keep a copy of the authorization document accessible during the engagement

During the engagement:

- [ ] Stay within the authorized scope
- [ ] Document everything — timestamps, commands run, results
- [ ] Communicate immediately if you cause unintended disruption
- [ ] Do not access or copy data beyond what's needed to prove the vulnerability
- [ ] Do not escalate an attack beyond what's necessary to prove the point

---

## 10. Hands-On Lab: Write a Responsible Disclosure Policy

### Objective

Write a **Responsible Disclosure Policy (RDP)** as if you are the security team of a fictional company called **"TechVault Inc."** — a cloud storage provider.

A responsible disclosure policy tells security researchers:
- How to report vulnerabilities to your company
- What researchers are allowed to do during research
- What rewards or recognition they can expect
- What you commit to in response

---

### Background: Why Companies Need This

Without a disclosure policy:
- Researchers don't know if they'll be prosecuted for reporting findings
- Researchers may post findings publicly immediately
- Companies lose control of the disclosure narrative

A clear, researcher-friendly policy:
- Encourages researchers to report to you instead of selling to criminals
- Gives you time to fix issues before public disclosure
- Builds goodwill with the security community

---

### Instructions

Write a responsible disclosure policy for TechVault Inc. covering all sections below. Use formal, clear language.

---

### Template Structure

```
TECHVAULT INC. — RESPONSIBLE DISCLOSURE POLICY
Version 1.0 | Last Updated: [Date]

1. INTRODUCTION
2. SCOPE
3. OUT-OF-SCOPE
4. AUTHORIZED TESTING GUIDELINES
5. REPORTING A VULNERABILITY
6. WHAT TO INCLUDE IN YOUR REPORT
7. OUR COMMITMENTS TO YOU
8. SAFE HARBOR
9. REWARDS / RECOGNITION
10. LEGAL NOTICE
```

---

### Section-by-Section Guidance

**Section 1: Introduction**
Write 2–3 sentences explaining that TechVault Inc. takes security seriously, welcomes reports from security researchers, and has created this policy to provide a clear, safe process.

---

**Section 2: Scope**
List what researchers ARE allowed to test. For TechVault Inc., include:
- `*.techvault.io` (web applications)
- TechVault iOS and Android mobile applications
- TechVault public API (`api.techvault.io`)

---

**Section 3: Out-of-Scope**
List what researchers are NOT allowed to test. Include at minimum:
- Physical attacks on TechVault offices or data centers
- Social engineering of TechVault employees or contractors
- Denial of Service (DoS/DDoS) attacks
- Attacks on TechVault's customers or user accounts
- Third-party services integrated into TechVault
- `beta.techvault.io` and `staging.techvault.io`

---

**Section 4: Authorized Testing Guidelines**
Describe the rules of conduct. Include:
- Do not access, modify, or delete data belonging to other users
- Do not perform testing that would disrupt service availability
- Use only test accounts you have created yourself
- Do not exploit vulnerabilities beyond confirming they exist
- Do not publicly disclose the vulnerability before TechVault has issued a fix

---

**Section 5: Reporting a Vulnerability**
Provide a process. Include:
- Email address for reports: `security@techvault.io`
- Request for encrypted communication: provide a PGP public key (or indicate one is available)
- Timeframe: TechVault will acknowledge receipt within 3 business days

---

**Section 6: What to Include in Your Report**
List the information required:
- Description of the vulnerability
- Potential impact
- Steps to reproduce (numbered, clear)
- Proof of concept (screenshots, video, code)
- Any systems, URLs, or parameters affected
- Your suggested severity (Critical/High/Medium/Low)

---

**Section 7: TechVault's Commitments**
What you promise the researcher:
- Acknowledge their report within 3 business days
- Provide a timeline for investigation within 10 business days
- Notify them when the vulnerability has been fixed
- Credit them publicly (if they wish) after the fix is deployed
- Not pursue legal action against researchers who follow this policy

---

**Section 8: Safe Harbor**
This is the most important section for researchers. Write language indicating that:
- TechVault will not pursue legal action against researchers who act in good faith within this policy
- TechVault considers this policy an authorized testing permission under the CFAA and similar laws
- Safe harbor only applies to testing done under this policy within the defined scope

---

**Section 9: Rewards / Recognition**
For this lab, TechVault does not have a formal bug bounty (no cash rewards), but offers:
- Public acknowledgment in TechVault's Security Hall of Fame
- A personalized letter of thanks for significant findings
- (Bonus: Add a future bounty program placeholder for Critical findings)

---

**Section 10: Legal Notice**
Standard disclaimer:
- This policy does not grant permission to test out-of-scope systems
- Researchers must comply with all applicable laws
- TechVault reserves the right to update this policy at any time

---

### Deliverable

Save your completed policy as:
`lab_02_techvault_responsible_disclosure_policy.md`

### Bonus Challenge

Compare your policy against two real disclosure policies from major companies:
- [Google's Vulnerability Reward Program Policy](https://bughunters.google.com/about/rules/6625378258649088)
- [HackerOne's Policy Template](https://www.hackerone.com/vulnerability-and-security-testing-policy-templates)

Note: What did they include that you didn't? What would you add to your policy?

---

## 11. Summary & Key Takeaways

### What You Learned in Module 2

**Global Legal Landscape:**
- The Budapest Convention provides the international baseline for cybercrime law
- Cybercrime is prosecuted globally — where servers are located matters
- Authorized access is the line between ethical hacking and criminal activity

**Regional Laws:**
- GDPR (EU) — strongest data privacy law globally; applies to any organization handling EU data; up to 4% of global turnover in fines
- CFAA (USA) — primary US federal cybercrime law; "without authorization" is the key phrase
- IT Act (India) — Section 66 governs computer-related offences; CERT-In handles incident response
- China's Cybersecurity Law — data localization, government access requirements

**Compliance Frameworks:**
- HIPAA — protects patient health data; 60-day breach notification; requires technical, physical, and administrative safeguards
- PCI-DSS — 12 requirements for protecting cardholder data; penetration testing is required annually
- SOX — financial data integrity; IT controls are central to compliance
- FISMA — US federal agencies and contractors; uses NIST SP 800-53 controls

**Bug Bounty and Responsible Disclosure:**
- Bug bounties are the formal mechanism for legal authorized research outside employment
- Responsible disclosure = private report → 90 days → public disclosure
- Always read scope carefully; out-of-scope testing voids legal protection

**Frameworks:**
- NIST CSF 2.0 — 6 functions (Govern, Identify, Protect, Detect, Respond, Recover); risk-based; flexible
- ISO 27001 — certifiable ISMS standard; 93 controls; demonstrates commitment to customers

**Ethics:**
- Authorization is everything — written, explicit, from the right person
- Do no harm — avoid disruption, don't copy data beyond necessity
- Confidentiality — treat client information with utmost care
- Responsible disclosure — give vendors time to fix before going public

---

## 12. Quiz: Test Your Knowledge

1. What treaty is considered the first international treaty on cybercrime?

2. Under GDPR, how many hours does an organization have to report a data breach to its Data Protection Authority?

3. What does CFAA stand for, and what country does it apply to?

4. Which compliance framework specifically protects **patient health information** in the USA?

5. How many requirements does PCI-DSS have?

6. What are the 6 functions of NIST CSF 2.0? (New function added in v2.0?)

7. What is the difference between ISO 27001 and NIST CSF?

8. What is "responsible disclosure" and what is the standard disclosure timeline?

9. Name three things that are typically **out-of-scope** in a bug bounty program.

10. A researcher finds a vulnerability in a banking app and accesses real customer accounts to "prove" the issue. Is this ethical? Why or why not?

11. Under HIPAA, what is the fine range for Tier 4 violations (willful neglect, not corrected)?

12. What does "safe harbor" mean in a responsible disclosure policy?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Budapest Convention on Cybercrime (2001)**
2. **72 hours**
3. **Computer Fraud and Abuse Act** — United States federal law
4. **HIPAA** (Health Insurance Portability and Accountability Act)
5. **12 requirements**, organized into 6 control objectives
6. **Govern, Identify, Protect, Detect, Respond, Recover** — "Govern" was added in CSF 2.0
7. NIST CSF is a **flexible voluntary framework** (no certification); ISO 27001 is a **certifiable standard** that organizations can be independently audited against
8. Responsible disclosure = privately notifying the vendor, giving them time to fix (typically **90 days**), then publishing publicly after the fix is deployed
9. Any three of: Physical attacks, social engineering, DoS/DDoS, third-party services, other users' data, out-of-scope domains/IPs
10. **No** — even with good intent, accessing real customer accounts without authorization is illegal (CFAA, GDPR, etc.) and unethical. The researcher should have used a test account or confirmed the vulnerability without accessing real data.
11. **$50,000 per violation**, up to **$1.9 million per year**
12. Safe harbor means the company **will not pursue legal action** against security researchers who act in good faith within the defined policy scope

</details>

---

## 13. Resources

### Official Legal Texts
- [Budapest Convention Full Text](https://www.coe.int/en/web/conventions/full-list/conventions/treaty/185)
- [GDPR Full Text](https://gdpr-info.eu/)
- [CFAA — 18 U.S.C. § 1030](https://www.law.cornell.edu/uscode/text/18/1030)
- [India IT Act 2000](https://www.meity.gov.in/content/information-technology-act-2000)

### Compliance Resources
- [HIPAA Journal](https://www.hipaajournal.com/)
- [PCI Security Standards Council](https://www.pcisecuritystandards.org/)
- [NIST CSF 2.0](https://www.nist.gov/cyberframework)
- [ISO 27001 Overview](https://www.iso.org/standard/27001)

### Bug Bounty Platforms
- [HackerOne](https://www.hackerone.com/)
- [Bugcrowd](https://www.bugcrowd.com/)
- [Intigriti](https://www.intigriti.com/)
- [Google Vulnerability Reward Program](https://bughunters.google.com/)

### Responsible Disclosure Policy Templates
- [disclose.io — Policy Templates](https://disclose.io/programs/)
- [HackerOne Vulnerability Disclosure Policy Builder](https://www.hackerone.com/vulnerability-and-security-testing-policy-templates)

### Ethics & Professional Codes
- [ISC² Code of Ethics](https://www.isc2.org/ethics)
- [EC-Council Code of Ethics](https://www.eccouncil.org/code-of-ethics/)

---

## Module Progress Checklist

Before moving to Module 3, confirm you can:

- [ ] Explain what the Budapest Convention is and why it matters
- [ ] Describe GDPR's key requirements and the 72-hour breach notification rule
- [ ] Explain the CFAA and what "without authorization" means practically
- [ ] Identify which compliance framework applies to healthcare, payments, finance, and government
- [ ] Explain the difference between NIST CSF and ISO 27001
- [ ] Describe the responsible disclosure process and the 90-day standard
- [ ] Explain what bug bounty scope means and why out-of-scope matters legally
- [ ] List the 6 ethical principles for security professionals
- [ ] Complete the responsible disclosure policy lab

---

**Previous Module:** [← Module 1 — Introduction to Cybersecurity](Module_01_Introduction_to_Cybersecurity.md)

**Next Module:** [ Module 3 — Networking Deep Dive Part 1→](Module_03_Networking_Deep_Dive_Part_1.md)

---

*Module 2 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
