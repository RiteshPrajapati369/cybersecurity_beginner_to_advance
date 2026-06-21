# Module 5: Cryptography & PKI (Public Key Infrastructure)

> **Phase 1 — Absolute Foundations**
> 🟡 Difficulty: Beginner–Intermediate | ⏱ Estimated Time: 8–10 hours | 📁 Folder: `01_FOUNDATIONS`

---

## Table of Contents

1. [Introduction to Cryptography](#1-introduction-to-cryptography)
2. [Symmetric Encryption](#2-symmetric-encryption)
3. [Asymmetric Encryption](#3-asymmetric-encryption)
4. [Hash Functions](#4-hash-functions)
5. [Digital Signatures](#5-digital-signatures)
6. [Digital Certificates](#6-digital-certificates)
7. [PKI — Public Key Infrastructure](#7-pki--public-key-infrastructure)
8. [SSL/TLS](#8-ssltls)
9. [Common Cryptographic Attacks](#9-common-cryptographic-attacks)
10. [Hands-On Lab: Hash Cracking & SSL Certificate Inspection](#10-hands-on-lab-hash-cracking--ssl-certificate-inspection)
11. [Summary & Key Takeaways](#11-summary--key-takeaways)
12. [Quiz: Test Your Knowledge](#12-quiz-test-your-knowledge)
13. [Resources](#13-resources)

---

## 1. Introduction to Cryptography

### 1.1 What is Cryptography?

**Cryptography** is the science of securing information by transforming it into an unreadable format so that only authorized parties can read it.

The word comes from the Greek: *kryptos* (hidden) + *graphein* (to write).

Cryptography is the **mathematical backbone** of almost all cybersecurity:
- When you log into a website over HTTPS — cryptography
- When you send a WhatsApp message — cryptography (end-to-end encryption)
- When you store a password in a database — cryptography (hashing)
- When you verify a software download hasn't been tampered with — cryptography (hashing + digital signatures)
- When a bank processes your transaction — cryptography

---

### 1.2 Core Terminology

| Term | Definition |
|---|---|
| **Plaintext** | The original, readable data |
| **Ciphertext** | The encrypted, unreadable output |
| **Encryption** | The process of converting plaintext → ciphertext |
| **Decryption** | The process of converting ciphertext → plaintext |
| **Key** | A secret value used to encrypt or decrypt data |
| **Cipher** | The algorithm used to perform encryption/decryption |
| **Cryptanalysis** | The study of breaking ciphers without the key |
| **Cryptosystem** | The complete system: algorithm + key management + protocols |

---

### 1.3 Goals of Cryptography — The CIA Triad + More

Cryptography directly supports the CIA Triad and extends it:

| Goal | How Cryptography Achieves It |
|---|---|
| **Confidentiality** | Encryption prevents unauthorized reading |
| **Integrity** | Hashing detects unauthorized modification |
| **Authentication** | Digital signatures verify identity |
| **Non-repudiation** | Digital signatures prevent denial of actions |
| **Availability** | Cryptography itself doesn't help here — but key availability matters |

---

### 1.4 Brief History of Cryptography

Understanding the history gives context for why modern cryptography is designed the way it is.

| Era | Cipher | How It Works | Weakness |
|---|---|---|---|
| Ancient | Caesar Cipher | Shift each letter by N positions | Only 25 possible keys — brute force trivial |
| Medieval | Vigenère Cipher | Polyalphabetic substitution with keyword | Kasiski examination breaks it |
| WWI/WWII | Enigma Machine | Electromechanical substitution | Broken by Alan Turing at Bletchley Park |
| 1970s | DES (IBM/NSA) | Block cipher, 56-bit key | 56-bit key too short — brute forced in 1998 |
| 1990s | RSA, AES | Modern public key + symmetric | Still in use — correctly implemented, secure |
| Today | AES-256, ECC, ChaCha20 | Modern standards | Secure against all known attacks |

> 💡 **Kerckhoffs's Principle (1883):** A cryptosystem should be secure even if everything about the system, except the key, is public knowledge. Modern cryptography follows this — AES's algorithm is public; its security comes entirely from the key.

---

### 1.5 Types of Cryptography

There are three main categories:

```
Cryptography
├── Symmetric (same key for encrypt + decrypt)
├── Asymmetric (public key + private key pair)
└── Hashing (one-way transformation — no key needed)
```

Each serves different purposes. We'll cover all three in depth.

---

## 2. Symmetric Encryption

### 2.1 How Symmetric Encryption Works

In symmetric encryption, the **same key** is used to both encrypt and decrypt data.

```
Sender:  Plaintext + Key → [Encryption Algorithm] → Ciphertext
         (send ciphertext over network)
Receiver: Ciphertext + Key → [Decryption Algorithm] → Plaintext
```

**The key challenge:** How do both parties securely share the key? If the key is intercepted, all communication is compromised. This is called the **key distribution problem** — solved by asymmetric cryptography.

**Advantages:**
- Very fast — suitable for encrypting large volumes of data
- Computationally efficient
- Simple conceptually

**Disadvantages:**
- Key distribution problem — how do you securely share the key?
- Key management at scale — 1,000 users communicating = 499,500 unique keys needed
- No inherent authentication — knowing the key doesn't prove who sent the message

---

### 2.2 How Block Ciphers Work

Most modern symmetric ciphers are **block ciphers** — they process data in fixed-size blocks.

```
Input Plaintext:  "HELLO WORLD THIS IS A SECRET MESSAGE"
Block size:       128 bits (16 bytes)
                  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
Blocks:           │ HELLO WORLD │ │  THIS IS A  │ │ SECRET MES. │
                  └─────────────┘ └─────────────┘ └─────────────┘
Each block encrypted independently (or with chaining — see modes below)
```

---

### 2.3 Block Cipher Modes of Operation

The **mode of operation** determines how each block is processed. This is critical — the wrong mode can destroy security even with a strong algorithm.

#### ECB — Electronic Codebook (NEVER USE)

Each block is encrypted **independently** with the same key.

**Problem:** Identical plaintext blocks produce identical ciphertext blocks — patterns in the data are visible in the ciphertext.

Famous demonstration: The ECB Penguin — encrypting a bitmap image with ECB produces an encrypted image where the penguin outline is still visible.

```
Plaintext:   [BLOCK A][BLOCK A][BLOCK B]
Ciphertext:  [ENC-A  ][ENC-A  ][ENC-B  ]  ← same plaintext = same ciphertext
```

#### CBC — Cipher Block Chaining (Common, but has issues)

Each plaintext block is XORed with the **previous ciphertext block** before encryption. An **Initialization Vector (IV)** is used for the first block.

```
Block 1: IV XOR Plaintext1 → Encrypt → Ciphertext1
Block 2: Ciphertext1 XOR Plaintext2 → Encrypt → Ciphertext2
```

**Pros:** Patterns in plaintext don't appear in ciphertext
**Cons:** Sequential (can't parallelize); padding oracle attacks (POODLE)

#### CTR — Counter Mode (Recommended)

Converts block cipher into stream cipher. Encrypts a counter value and XORs with plaintext.

**Pros:** Parallelizable; no padding needed; random access
**Cons:** IV/nonce must never be reused with same key (catastrophic if reused)

#### GCM — Galois/Counter Mode (Best Practice)

CTR mode + **authentication tag** (GHASH). Provides both confidentiality AND integrity/authenticity.

**This is what TLS 1.3 uses.** Also called **AEAD (Authenticated Encryption with Associated Data)**.

---

### 2.4 AES — Advanced Encryption Standard

**AES** is the **gold standard** of symmetric encryption. Adopted by NIST in 2001, it replaced DES.

- **Key sizes:** 128-bit, 192-bit, 256-bit
- **Block size:** 128 bits (always)
- **Structure:** Substitution-Permutation Network (SPN)
- **Rounds:** 10 (AES-128), 12 (AES-192), 14 (AES-256)
- **Used in:** TLS, WPA2/WPA3, BitLocker, FileVault, Signal, WhatsApp, SSH, IPsec, PGP

**Is AES breakable?**
- No known practical attacks against correctly implemented AES
- AES-256: Would take longer than the age of the universe to brute force on current hardware
- **Quantum computing concern:** Grover's algorithm halves effective key length (AES-256 → ~128-bit security). AES-256 still considered quantum-safe; AES-128 would need upgrading

**AES in Practice:**
```
AES-128-CBC  → Common in older TLS (avoid in new implementations)
AES-256-CBC  → Better but still has CBC weaknesses
AES-256-GCM  → Best practice — what you should recommend
AES-128-GCM  → Also acceptable, widely used in TLS 1.3
```

---

### 2.5 DES — Data Encryption Standard (Legacy/Broken)

- **Key size:** 56 bits
- **Block size:** 64 bits
- **Developed:** IBM, adopted by NSA, 1977
- **Status:** **BROKEN** — do not use

**Why it's broken:** 56-bit key = only 2^56 possible keys = ~72 quadrillion. In 1998, the EFF's "Deep Crack" machine cracked DES in 22 hours for $250,000. Today, it can be cracked in minutes.

---

### 2.6 3DES — Triple DES (Legacy/Deprecated)

Applies DES three times with different keys to extend effective key length.

```
Encrypt(Key1) → Decrypt(Key2) → Encrypt(Key3) = 3DES
```

- **Effective key size:** 112 bits (168-bit with 3 independent keys, but meet-in-the-middle attacks reduce to 112)
- **Status:** Deprecated by NIST in 2019, disallowed after 2023
- **Still seen in:** Legacy banking systems, old TLS configurations, payment terminals

---

### 2.7 ChaCha20

**ChaCha20** is a modern stream cipher designed by Daniel Bernstein.

- **Key size:** 256 bits
- **Type:** Stream cipher (not block cipher)
- **Paired with:** Poly1305 MAC for authentication → **ChaCha20-Poly1305**
- **Used in:** TLS 1.3, WireGuard VPN, SSH, Android (preferred over AES when no hardware acceleration)

**Why ChaCha20?**
- Faster than AES on systems without hardware AES acceleration (mobile devices, IoT)
- No timing side-channel vulnerabilities (unlike AES without hardware acceleration)
- Google made it the default for HTTPS on Android when AES hardware acceleration isn't available

---

### 2.8 RC4 (Broken — Historical Knowledge)

- Stream cipher
- **Status:** **BROKEN** — prohibited in TLS since RFC 7465 (2015)
- Was widely used in WEP Wi-Fi encryption and early SSL/TLS
- Multiple practical attacks: BEAST, RC4 biases, Fluhrer-Mantin-Shamir attack

---

### 2.9 Symmetric Algorithm Comparison

| Algorithm | Key Size | Block Size | Status | Use Case |
|---|---|---|---|---|
| AES-128 | 128-bit | 128-bit | ✅ Secure | TLS, general encryption |
| AES-256 | 256-bit | 128-bit | ✅ Most secure | High-security, government |
| ChaCha20 | 256-bit | Stream | ✅ Secure | Mobile, IoT, WireGuard |
| 3DES | 112-bit eff. | 64-bit | ⚠️ Deprecated | Legacy systems only |
| DES | 56-bit | 64-bit | ❌ Broken | Never use |
| RC4 | 40–2048-bit | Stream | ❌ Broken | Never use |

---

## 3. Asymmetric Encryption

### 3.1 The Key Distribution Problem

Symmetric encryption has a fundamental problem: **How do you securely share the secret key?**

If you want to communicate securely with someone you've never met (e.g., a website):
- You can't send the key over the internet — it could be intercepted
- You can't meet in person — that's impractical at internet scale
- You need a way to establish a shared secret without ever exchanging it directly

**Asymmetric cryptography solves this.**

---

### 3.2 How Asymmetric Encryption Works

Asymmetric encryption uses a **mathematically linked key pair**:

- **Public key** — Can be shared openly with anyone
- **Private key** — Must be kept completely secret by the owner

The keys have a special mathematical relationship:
- What one key **encrypts**, only the other key can **decrypt**
- You **cannot derive the private key from the public key** (computationally infeasible)

```
Key Generation:
└── Generate mathematically linked pair: (Public Key, Private Key)

Encryption (for confidentiality):
└── Sender encrypts with recipient's PUBLIC key
└── Only recipient can decrypt with their PRIVATE key

Signing (for authentication):
└── Sender signs with their own PRIVATE key
└── Anyone can verify with sender's PUBLIC key
```

---

### 3.3 RSA — Rivest-Shamir-Adleman

**RSA** is the most widely known asymmetric algorithm, invented in 1977.

**Mathematical foundation:** The difficulty of **factoring the product of two large prime numbers**.

```
Choose two large primes: p = 61, q = 53  (in practice: 1024–4096 bit primes)
n = p × q = 3233  (public modulus)
e = 17  (public exponent, often 65537)
Public key:  (e, n) = (17, 3233)
Private key: (d, n) where d is derived from p, q, e

Encryption: C = M^e mod n
Decryption: M = C^d mod n
```

**Key sizes and security:**

| Key Size | Security Level | Status |
|---|---|---|
| 512-bit | Broken | Do not use |
| 1024-bit | Broken | Do not use |
| 2048-bit | ~112-bit security | Minimum acceptable (deprecated after 2030) |
| 3072-bit | ~128-bit security | Recommended |
| 4096-bit | ~140-bit security | High security, slower |

**RSA Weaknesses:**
- Slow — only suitable for small amounts of data (keys, signatures)
- In practice, RSA encrypts a **symmetric key**, then symmetric encryption encrypts the actual data (hybrid encryption)
- Vulnerable to quantum computers (Shor's algorithm)
- **Padding is critical** — raw RSA (textbook RSA) is insecure; must use OAEP padding

**RSA in Practice:**
- TLS key exchange (being replaced by ECDHE)
- SSH key authentication
- Code signing
- Email encryption (PGP/S/MIME)
- Digital certificates

---

### 3.4 ECC — Elliptic Curve Cryptography

**ECC** is a modern approach to asymmetric cryptography based on the mathematics of **elliptic curves over finite fields**.

**Mathematical foundation:** The **elliptic curve discrete logarithm problem (ECDLP)** — given a point on the curve and a scalar multiple of it, finding the scalar is computationally infeasible.

**Why ECC is better than RSA:**

| Security Level | RSA Key Size | ECC Key Size | ECC Advantage |
|---|---|---|---|
| 80-bit | 1024-bit | 160-bit | 6x smaller key |
| 128-bit | 3072-bit | 256-bit | 12x smaller key |
| 256-bit | 15360-bit | 512-bit | 30x smaller key |

Smaller keys = faster computation, less power, less bandwidth — critical for mobile devices and IoT.

**Common ECC Curves:**

| Curve | Key Size | Used In | Notes |
|---|---|---|---|
| P-256 (secp256r1) | 256-bit | TLS, HTTPS, certificates | Most widely used |
| P-384 (secp384r1) | 384-bit | NSA Suite B, high security | Government use |
| Curve25519 | 255-bit | SSH, Signal, WireGuard | Designed by Bernstein; considered more trustworthy than NIST curves |
| secp256k1 | 256-bit | Bitcoin, Ethereum | Cryptocurrency |

**ECDSA** (Elliptic Curve Digital Signature Algorithm) — signing
**ECDH** (Elliptic Curve Diffie-Hellman) — key exchange

---

### 3.5 Diffie-Hellman Key Exchange

**Diffie-Hellman (DH)** is not an encryption algorithm — it's a **key exchange protocol** that allows two parties to establish a shared secret over an insecure channel.

Published in 1976 by Whitfield Diffie and Martin Hellman — a revolutionary idea.

**How it works (simplified with colors analogy):**

```
Both agree on a public "common paint": Yellow

Alice picks secret color: Red
Alice mixes: Yellow + Red = Orange → sends Orange to Bob

Bob picks secret color: Blue
Bob mixes: Yellow + Blue = Green → sends Green to Alice

Alice: Green + Red = Brown (shared secret)
Bob:   Orange + Blue = Brown (same shared secret!)

An eavesdropper sees: Yellow, Orange, Green — but cannot derive Brown
```

**Mathematical version:**
```
Public parameters: large prime p, generator g

Alice: picks private a, sends A = g^a mod p
Bob:   picks private b, sends B = g^b mod p

Alice computes: s = B^a mod p = g^(ab) mod p
Bob computes:   s = A^b mod p = g^(ab) mod p
Both get the same secret s!
```

**DHE (Diffie-Hellman Ephemeral):** New DH keys generated for each session → provides **Perfect Forward Secrecy (PFS)**

**ECDHE (Elliptic Curve DHE):** DH using elliptic curves → smaller keys, faster → what TLS 1.3 uses exclusively

---

### 3.6 Hybrid Encryption — How It's Actually Used

Asymmetric encryption is slow. Symmetric encryption is fast but has the key distribution problem.

**The solution:** Use both together.

```
1. Generate a random symmetric key (session key)
2. Encrypt the session key with recipient's RSA/ECC public key
3. Encrypt the actual data with the symmetric session key (AES)
4. Send: [Encrypted session key] + [Encrypted data]

Recipient:
1. Decrypt session key with their private key
2. Use session key to decrypt the data
```

This is exactly what HTTPS/TLS does:
- **Asymmetric:** Used only for authentication and key exchange
- **Symmetric (AES-GCM):** Used for the actual data

---

### 3.7 Asymmetric Algorithm Comparison

| Algorithm | Type | Key Size (secure) | Status | Use Case |
|---|---|---|---|---|
| RSA | Encryption/Signing | 2048-bit min | ✅ Secure (for now) | Certificates, legacy TLS |
| ECDSA | Signing | P-256 (256-bit) | ✅ Secure | TLS, SSH, code signing |
| ECDH/ECDHE | Key Exchange | P-256 (256-bit) | ✅ Secure | TLS key exchange |
| DH/DHE | Key Exchange | 2048-bit min | ⚠️ Being replaced | Legacy TLS |
| ElGamal | Encryption | 2048-bit | ⚠️ Less common | PGP |

---

## 4. Hash Functions

### 4.1 What is a Hash Function?

A **cryptographic hash function** takes an input of **any size** and produces a **fixed-size output** (the hash, digest, or checksum).

```
Input (any size):   "Hello"
Hash Function:      SHA-256
Output (256 bits):  185f8db32921bd46d35...  (64 hex characters)

Input:              "Hello World" (just added " World")
Output:             a591a6d40bf420404a...  (completely different hash)
```

**Key properties of a cryptographic hash function:**

| Property | Meaning |
|---|---|
| **Deterministic** | Same input always produces same output |
| **Fixed output size** | SHA-256 always outputs 256 bits, regardless of input size |
| **One-way (Pre-image resistance)** | Cannot reverse the hash to find the input |
| **Avalanche effect** | Small change in input → completely different hash |
| **Collision resistance** | Computationally infeasible to find two inputs with same hash |
| **Second pre-image resistance** | Given input X, cannot find another input Y where H(X) = H(Y) |

---

### 4.2 MD5 — Message Digest 5 (Broken)

- **Output:** 128 bits (32 hex characters)
- **Designed by:** Ron Rivest, 1991
- **Status:** ❌ **BROKEN for security purposes**

**Why MD5 is broken:**
- **Collision attacks** are practical — two different files can have the same MD5 hash
- In 2004, researchers demonstrated MD5 collisions
- In 2008, researchers created rogue CA certificates using MD5 collisions
- Flame malware (2012) used MD5 collision to forge a Microsoft code signing certificate

**Still used for:** Non-security checksums (verifying file integrity where collision attacks aren't a threat model), legacy database password storage (never do this for new systems)

```
MD5("hello") = 5d41402abc4b2a76b9719d911017c592
MD5("world") = 7d793037a0760186574b0282f2f435e7
```

---

### 4.3 SHA-1 — Secure Hash Algorithm 1 (Broken)

- **Output:** 160 bits (40 hex characters)
- **Designed by:** NSA, 1993
- **Status:** ❌ **BROKEN**

**Why SHA-1 is broken:**
- Theoretical collisions identified in 2005
- Google's **SHAttered attack (2017):** First practical SHA-1 collision — two different PDF files with the same SHA-1 hash, at a cost of ~$110,000 in computing
- **Deprecated by major browsers** for TLS certificates in 2017

```
SHA1("hello") = aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
```

---

### 4.4 SHA-2 Family (Current Standard)

SHA-2 is a family of hash functions designed by the NSA:

| Algorithm | Output Size | Hex Characters | Status | Common Use |
|---|---|---|---|---|
| SHA-224 | 224 bits | 56 chars | ✅ Secure | Rarely used |
| SHA-256 | 256 bits | 64 chars | ✅ Most used | TLS, Bitcoin, code signing |
| SHA-384 | 384 bits | 96 chars | ✅ Secure | NSA Suite B, TLS |
| SHA-512 | 512 bits | 128 chars | ✅ Most secure | High-security applications |
| SHA-512/256 | 256 bits | 64 chars | ✅ Secure | Faster than SHA-256 on 64-bit |

```
SHA256("hello") = 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
SHA512("hello") = 9b71d224bd62f3785d96d46ad3ea3d73319bfbc2890caadae2dff72519673ca72323c3d99ba5c11d7c7acc6e14b8c5da0c4663475c2e5c3adef46f73bcdec043
```

**SHA-256 in practice:**
- Bitcoin proof-of-work uses double SHA-256
- X.509 certificates use SHA-256 for signing (SHA256withRSA, SHA256withECDSA)
- TLS 1.3 uses SHA-256 and SHA-384 in its HMAC constructions
- File integrity verification (software downloads)

---

### 4.5 SHA-3 (Keccak)

- **Designed by:** Guido Bertoni, Joan Daemen, et al.
- **Adopted by NIST:** 2015
- **Different design:** Uses "sponge construction" — completely different from SHA-2

SHA-3 was created as a backup in case weaknesses were found in SHA-2. SHA-2 has remained secure, so SHA-3 is less common — but it's the right choice if you want algorithm diversity.

| Algorithm | Output |
|---|---|
| SHA3-224 | 224 bits |
| SHA3-256 | 256 bits |
| SHA3-384 | 384 bits |
| SHA3-512 | 512 bits |
| SHAKE128 | Variable length |
| SHAKE256 | Variable length |

---

### 4.6 Password Hashing — bcrypt, scrypt, Argon2

**General-purpose hash functions (SHA-256, SHA-512) are WRONG for passwords.** Here's why:

The problem with fast hashes for passwords:
```
Modern GPU: Can compute 10+ billion SHA-256 hashes per second
Password: "password123"
Time to crack with brute force: Milliseconds
```

**Password hashing algorithms are deliberately slow** to make brute-force and dictionary attacks impractical.

---

#### bcrypt

- **Designed:** 1999 by Niels Provos and David Mazières
- **Based on:** Blowfish cipher
- **Work factor:** Configurable cost factor (2^cost iterations)
- **Output:** 60-character string including algorithm, cost, salt, and hash
- **Max password length:** 72 bytes (passwords longer than 72 bytes are truncated)

```
$2b$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewdBPj/VcSAg/m2G
│  │  │                    │
│  │  └─ cost factor (12)  └─ salt+hash (53 chars)
│  └─ version (2b)
└─ algorithm (bcrypt)
```

**Cost factor 12 means 2^12 = 4,096 iterations.** A GPU that could compute 10 billion SHA-256 hashes/second can only compute ~10,000 bcrypt/second (cost 12).

---

#### scrypt

- **Designed:** 2009 by Colin Percival
- **Improvement over bcrypt:** Also **memory-hard** — requires large amounts of RAM, making GPU/ASIC attacks much more expensive
- **Parameters:** N (CPU/memory cost), r (block size), p (parallelization)
- **Used in:** Litecoin proof-of-work, some Linux systems

---

#### Argon2 (Winner — Use This)

- **Winner of Password Hashing Competition (PHC) 2015**
- **Three variants:**
  - **Argon2d** — Resistant to GPU attacks, vulnerable to side-channel attacks
  - **Argon2i** — Resistant to side-channel attacks
  - **Argon2id** — Hybrid — **recommended for password hashing**
- **Parameters:** Memory (m), iterations (t), parallelism (p)
- **Recommended by:** OWASP, NIST SP 800-63B

**OWASP recommended minimum parameters:**
```
Argon2id:
  m = 19 MB memory
  t = 2 iterations
  p = 1 parallelism
```

---

#### Password Hashing Comparison

| Algorithm | Year | Memory-Hard | GPU-Resistant | Recommendation |
|---|---|---|---|---|
| MD5 | 1991 | ❌ | ❌ | Never use for passwords |
| SHA-256 | 2001 | ❌ | ❌ | Never use for passwords |
| bcrypt | 1999 | ❌ | ⚠️ Moderate | Acceptable (legacy) |
| scrypt | 2009 | ✅ | ✅ | Good |
| Argon2id | 2015 | ✅ | ✅ | **Best practice** |

---

### 4.7 HMAC — Hash-based Message Authentication Code

**HMAC** combines a hash function with a secret key to provide **both integrity and authenticity**.

```
HMAC(key, message) = Hash(key XOR opad || Hash(key XOR ipad || message))

Example:
HMAC-SHA256("secret_key", "Transfer $100 to Alice") = 8f3a2b1c...
```

**Why HMAC instead of just hashing?**

A plain hash verifies integrity but not authenticity — anyone can compute a hash. HMAC requires knowledge of the secret key, so only parties with the key can create or verify it.

**Used in:**
- JWT (JSON Web Tokens) — `HS256` is HMAC-SHA256
- TLS 1.2 for MAC in cipher suites
- API authentication (AWS Signature Version 4 uses HMAC-SHA256)
- Cookie signing

---

### 4.8 Hash Uses in Cybersecurity

| Use Case | How Hashing Is Used |
|---|---|
| Password storage | Store `hash(salt + password)` — never store plaintext |
| File integrity | Hash a file before/after transfer — compare hashes |
| Digital signatures | Sign `hash(message)` — not the message itself |
| Blockchain | Each block contains hash of previous block — creates immutable chain |
| Certificate fingerprints | Identify a certificate by its hash |
| Malware detection | Antivirus compares file hashes against known malware hashes |
| Forensics | Hash evidence files to prove they haven't been tampered with |

---

## 5. Digital Signatures

### 5.1 What is a Digital Signature?

A **digital signature** is a cryptographic mechanism that provides:
- **Authentication** — Proof of who created/sent the message
- **Integrity** — Proof the message hasn't been tampered with
- **Non-repudiation** — The signer cannot deny having signed

Think of it as the digital equivalent of a handwritten signature — but mathematically verifiable and impossible to forge without the private key.

---

### 5.2 How Digital Signatures Work

```
SIGNING (sender):
1. Sender creates message M
2. Sender computes hash: H = SHA-256(M)
3. Sender encrypts hash with PRIVATE key: Signature = RSA_Sign(PrivKey, H)
4. Sender sends: [Message M] + [Signature]

VERIFICATION (receiver):
1. Receiver gets: [Message M] + [Signature]
2. Receiver decrypts signature with sender's PUBLIC key: H' = RSA_Verify(PubKey, Signature)
3. Receiver computes hash of received message: H = SHA-256(M)
4. If H == H': ✅ Signature valid — message is authentic and unmodified
   If H != H': ❌ Signature invalid — message tampered or wrong sender
```

**Why hash the message first?**
- RSA can only sign small amounts of data (key size limits)
- Hashing produces a fixed-size digest (e.g., 256 bits for SHA-256)
- Signing the hash is equivalent to signing the whole message

---

### 5.3 Digital Signature Algorithms

| Algorithm | Based On | Key Size | Status | Used In |
|---|---|---|---|---|
| RSA-PSS | RSA | 2048-bit min | ✅ Secure | TLS certificates, code signing |
| RSA-PKCS#1 v1.5 | RSA | 2048-bit min | ⚠️ Legacy | Old TLS, email |
| ECDSA | ECC | P-256 | ✅ Recommended | TLS, SSH, Bitcoin |
| EdDSA (Ed25519) | Edwards curve | 256-bit | ✅ Best practice | SSH, WireGuard, modern apps |
| DSA | Discrete log | 2048-bit | ⚠️ Deprecated | Legacy |

**Ed25519** is the modern recommended signing algorithm:
- Designed by Daniel Bernstein
- Faster than ECDSA
- No random number requirement (ECDSA is vulnerable if RNG is weak — Sony PS3 was hacked because of a weak RNG in ECDSA)
- Immune to several side-channel attacks
- Used in: OpenSSH, Signal, Tor, WireGuard

---

### 5.4 Real-World Applications of Digital Signatures

**Code Signing:**
- Software developers sign their code with a private key
- Users' OS verifies the signature before running
- Windows requires kernel drivers to be signed
- Apple requires all Mac apps to be signed and notarized

**Email Signing (S/MIME and PGP):**
- Sign emails to prove they came from you
- Recipients can verify your signature
- Prevents email spoofing for parties that check signatures

**Document Signing:**
- PDF signatures (Adobe Acrobat)
- Electronic contracts (DocuSign uses digital signatures under the hood)
- Legal validity in most jurisdictions (EU eIDAS, US ESIGN Act)

**TLS Certificates:**
- Certificate Authorities sign certificates
- Your browser verifies the CA's signature
- Proves the certificate belongs to the legitimate website

**Blockchain:**
- Bitcoin transactions are signed with ECDSA (secp256k1)
- Proves the transaction was authorized by the private key owner

---

## 6. Digital Certificates

### 6.1 The Problem Digital Certificates Solve

Asymmetric encryption solves key distribution — but creates a new problem: **How do you know a public key actually belongs to who you think it does?**

**Scenario (Man-in-the-Middle attack):**
```
You want to connect to bank.com
Attacker intercepts and sends you THEIR public key instead
You encrypt data with attacker's key
Attacker decrypts, reads your banking credentials, re-encrypts with real bank's key
You and the bank think you're communicating securely — but attacker is in the middle
```

**Digital certificates solve this** by having a **trusted third party (Certificate Authority)** vouch for the binding between a public key and an identity.

---

### 6.2 What a Digital Certificate Contains

A digital certificate is essentially a **digitally signed document** that says:

> "I, TrustedCA, certify that the public key [KEY] belongs to [ENTITY] and is valid from [DATE] to [DATE]."

**X.509 Certificate Structure** (the standard format):

| Field | Description | Example |
|---|---|---|
| Version | X.509 version (usually v3) | v3 |
| Serial Number | Unique identifier assigned by CA | 12:34:56:78:9a:bc |
| Signature Algorithm | Algorithm used to sign the cert | SHA256withRSA |
| Issuer | Who issued/signed the cert | DigiCert Inc |
| Validity Period | Not Before / Not After | 2024-01-01 to 2025-01-01 |
| Subject | Who the cert belongs to | CN=bank.com, O=Bank Corp |
| Public Key | The entity's public key | RSA 2048-bit or EC P-256 |
| Subject Alt Names (SAN) | Other domains covered | bank.com, www.bank.com |
| Key Usage | What the key can be used for | Digital Signature, Key Encipherment |
| Extended Key Usage | Specific purposes | TLS Web Server Authentication |
| CRL Distribution Points | Where to check if cert is revoked | http://crl.digicert.com/... |
| OCSP | Online Certificate Status Protocol URL | http://ocsp.digicert.com |
| CA's Digital Signature | Signs all of the above | [signature bytes] |

---

### 6.3 Types of Certificates

#### By Validation Level

| Type | Validation | Issued In | Trust Level | Use Case |
|---|---|---|---|---|
| **DV (Domain Validation)** | Proves domain control only | Minutes | Low | Personal sites, blogs |
| **OV (Organization Validation)** | Verifies organization exists | Days | Medium | Business websites |
| **EV (Extended Validation)** | Thorough vetting of organization | Weeks | High | Banks, e-commerce |

Note: Major browsers removed the green address bar for EV certs (2019) as it gave false security assurance.

---

#### By Usage

| Certificate Type | Purpose |
|---|---|
| **TLS/SSL Server Certificate** | Authenticates a website server |
| **TLS/SSL Client Certificate** | Authenticates a client to a server (mutual TLS) |
| **Code Signing Certificate** | Signs software to verify publisher |
| **Email Certificate (S/MIME)** | Signs and encrypts email |
| **Root CA Certificate** | Self-signed; top of trust chain |
| **Intermediate CA Certificate** | Signed by root; issues end-entity certs |
| **Wildcard Certificate** | Covers *.domain.com (all subdomains) |
| **SAN Certificate** | Covers multiple specific domains |

---

#### Wildcard vs SAN

```
Wildcard:  *.bank.com
  Covers:  www.bank.com, login.bank.com, api.bank.com
  Doesn't cover: bank.com itself, sub.sub.bank.com

SAN cert:  SANs: bank.com, www.bank.com, mobile.bank.com, api.bank.com
  Covers:  Exactly those domains listed
```

---

### 6.4 Certificate Revocation

What if a certificate needs to be invalidated before it expires? (Private key compromised, company changed name, certificate issued fraudulently)

**Two mechanisms:**

#### CRL — Certificate Revocation List
- CA publishes a list of revoked certificate serial numbers
- Clients download and check the list
- **Problem:** CRLs can be large; downloaded infrequently; can be stale

#### OCSP — Online Certificate Status Protocol
- Client queries CA's OCSP server in real time for each certificate
- CA responds: Good / Revoked / Unknown
- **Problem:** Privacy (CA knows which sites you visit); OCSP server can be slow; if OCSP server is down, most browsers "soft-fail" (accept the cert)

#### OCSP Stapling (Best Practice)
- Server queries its own OCSP status and includes the signed response in the TLS handshake
- Client doesn't need to contact CA directly
- Solves privacy and performance issues

---

## 7. PKI — Public Key Infrastructure

### 7.1 What is PKI?

**Public Key Infrastructure (PKI)** is the complete ecosystem of:
- Policies
- Procedures
- Hardware
- Software
- People

...that manage the creation, distribution, storage, revocation, and trust of **digital certificates and cryptographic keys**.

PKI is the foundation of trust on the internet. Every time you see HTTPS, you're relying on PKI.

---

### 7.2 PKI Components

#### Certificate Authority (CA)

The **CA** is a trusted organization that:
- Issues digital certificates
- Verifies the identity of certificate applicants
- Signs certificates with its private key
- Maintains certificate revocation lists (CRL)
- Operates under strict security standards (physical security, HSMs, audit requirements)

**Root CAs:**
- Self-signed — trust them because they're pre-installed in your OS/browser
- Kept **offline** in physically secure facilities (sometimes underground vaults)
- Rarely used directly — instead, they sign Intermediate CAs

**Intermediate CAs:**
- Signed by a Root CA
- Used for day-to-day certificate issuance
- If an Intermediate CA is compromised, it can be revoked without touching the Root
- Example: DigiCert's Root CA signs "DigiCert TLS RSA SHA256 2020 CA1" (Intermediate), which signs your bank's certificate

**Major Public CAs:**
- DigiCert (largest — owns Symantec, Thawte, GeoTrust)
- Sectigo (formerly Comodo)
- GlobalSign
- Let's Encrypt (free, automated, 90-day certs)
- Entrust
- IdenTrust (issues certificates to Let's Encrypt's Intermediate)

---

#### Registration Authority (RA)

The RA acts as an **intermediary between the applicant and the CA**:
- Accepts certificate requests
- Verifies applicant identity
- Forwards validated requests to CA
- Distributes certificates to applicants

In many organizations, the CA and RA functions are combined.

---

#### Validation Authority (VA)

The VA **verifies certificate status** in real time:
- Responds to OCSP queries
- Answers "Is this certificate still valid?"
- May be operated by the CA or a third party

---

#### Certificate Store

The **certificate store** is where trusted root certificates are stored on your device:
- **Windows:** `certmgr.msc` → Trusted Root Certification Authorities
- **macOS:** Keychain Access → System Roots
- **Linux:** `/etc/ssl/certs/` or `/etc/ca-certificates/`
- **Firefox:** Uses its own built-in certificate store (not the OS store)
- **Chrome/Edge:** Uses the OS certificate store

These pre-installed root certificates are the **trust anchors** for the entire web PKI.

---

### 7.3 Certificate Trust Chain (Chain of Trust)

Every TLS certificate you encounter has a **chain of trust** connecting it to a Root CA:

```
Root CA (self-signed, in your trust store)
    └── signs ──→ Intermediate CA
                      └── signs ──→ End-Entity Certificate (bank.com)

Verification:
1. Is bank.com's cert signed by a trusted Intermediate?
2. Is the Intermediate signed by a trusted Root?
3. Is the Root in my trust store?
4. Is the certificate within its validity period?
5. Is the certificate not revoked?
If all YES → TRUSTED
```

**Why not just have Root CAs sign everything?**
- Root CA private keys are kept offline in vaults
- If a Root CA key were compromised, ALL certificates in the chain would be invalid
- Intermediate CAs keep the Root isolated from day-to-day operations
- If an Intermediate is compromised, only that Intermediate's certs are affected

---

### 7.4 PKI in Enterprise (Private PKI)

Organizations often run their **own internal PKI** for internal systems:

- Internal websites (intranet, HR portal, ticketing systems)
- VPN authentication (client certificates)
- Wi-Fi authentication (802.1X with EAP-TLS)
- Mutual TLS (mTLS) between microservices
- Code signing for internal tools

**Internal PKI tools:**
- **Microsoft Active Directory Certificate Services (AD CS)** — most common in Windows environments
- **HashiCorp Vault** — secrets management including PKI
- **EJBCA** — open source enterprise PKI
- **Dogtag Certificate System** — Red Hat's PKI

---

### 7.5 HSM — Hardware Security Module

An **HSM** is a physical device that:
- Generates cryptographic keys
- Stores private keys securely
- Performs cryptographic operations **inside the device** — keys never leave
- Is tamper-resistant (will wipe keys if physically attacked)

Used by:
- Certificate Authorities to protect their Root CA private keys
- Banks to protect encryption keys for financial transactions
- Enterprises for PKI and code signing keys

**Examples:**
- Thales nShield (enterprise)
- Utimaco HSMs
- AWS CloudHSM (cloud-based)
- YubiHSM (lower-cost)

---

## 8. SSL/TLS

### 8.1 What is TLS?

**TLS (Transport Layer Security)** is the cryptographic protocol that secures the vast majority of internet communications. It's what HTTPS uses.

**Common misconception:** People say "SSL" but mean TLS. SSL (Secure Sockets Layer) was the predecessor — SSL 2.0, SSL 3.0 are all **broken and disabled**. The correct modern term is TLS.

| Version | Year | Status |
|---|---|---|
| SSL 2.0 | 1995 | ❌ Broken (DROWN attack) |
| SSL 3.0 | 1996 | ❌ Broken (POODLE attack) |
| TLS 1.0 | 1999 | ❌ Deprecated (BEAST attack) |
| TLS 1.1 | 2006 | ❌ Deprecated |
| TLS 1.2 | 2008 | ⚠️ Acceptable (with strong cipher suites) |
| TLS 1.3 | 2018 | ✅ **Required for new systems** |

---

### 8.2 What TLS Provides

| Property | How TLS Provides It |
|---|---|
| **Confidentiality** | Symmetric encryption (AES-GCM, ChaCha20-Poly1305) |
| **Integrity** | AEAD cipher suites (authentication built-in) |
| **Authentication** | Digital certificates (server proves its identity) |
| **Perfect Forward Secrecy** | Ephemeral key exchange (ECDHE) |

---

### 8.3 TLS 1.3 Handshake (Modern)

TLS 1.3 dramatically simplified and improved the handshake:

```
Client                                          Server
  │                                               │
  │── ClientHello ──────────────────────────────→ │
  │   (supported cipher suites, key_share)         │
  │                                               │
  │ ←────────────────────────── ServerHello ──── │
  │   (chosen cipher suite, key_share)             │
  │                                               │
  [Both derive shared secret from ECDHE key_share]
  [All subsequent messages are ENCRYPTED]
  │                                               │
  │ ←──────────────── {Certificate} ──────────── │
  │ ←──────────────── {CertificateVerify} ─────── │
  │ ←──────────────── {Finished} ──────────────── │
  │                                               │
  │── {Finished} ───────────────────────────────→ │
  │                                               │
  [Application data flows — encrypted both ways]
```

**TLS 1.3 improvements over TLS 1.2:**
- **1-RTT handshake** (vs 2-RTT for TLS 1.2) — faster
- **0-RTT resumption** — send data with first message on reconnect (with caveats)
- **Removed weak algorithms:** RSA key exchange, DH without forward secrecy, RC4, DES, 3DES, MD5, SHA-1
- **Mandatory forward secrecy** — ECDHE only for key exchange
- **Encrypted more of the handshake** — Certificate is now encrypted in TLS 1.3
- **Fewer cipher suites** — Only AEAD ciphers (AES-GCM, ChaCha20-Poly1305)

---

### 8.4 TLS Cipher Suites

A **cipher suite** is a combination of algorithms used in TLS. It specifies:
- Key exchange algorithm
- Authentication algorithm
- Bulk encryption algorithm
- MAC/hash algorithm

**TLS 1.2 cipher suite example:**
```
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
│   │         │        │       │    │
│   │         │        │       │    └─ HMAC hash (SHA-384)
│   │         │        │       └─ Encryption mode (GCM)
│   │         │        └─ Encryption algorithm (AES-256)
│   │         └─ Authentication (RSA certificate)
│   └─ Key exchange (ECDHE — provides forward secrecy)
└─ Protocol (TLS)
```

**TLS 1.3 cipher suite example:**
```
TLS_AES_256_GCM_SHA384
│   │       │   │
│   │       │   └─ Hash for HKDF key derivation (SHA-384)
│   │       └─ Encryption mode (GCM)
│   └─ Encryption algorithm (AES-256)
└─ Protocol (TLS 1.3)
```
Note: TLS 1.3 cipher suites don't specify key exchange or authentication — ECDHE and certificate-based auth are mandatory.

---

### 8.5 Perfect Forward Secrecy (PFS)

**Perfect Forward Secrecy** means that if an attacker records your encrypted traffic today and later compromises your private key, they **still cannot decrypt the recorded traffic**.

**How it works:**
- TLS with ECDHE generates a **new ephemeral key pair for every session**
- The session key is derived from the ephemeral DH exchange
- The ephemeral private key is discarded after the session
- The server's long-term private key is used only for authentication, not key derivation

**Without PFS (e.g., RSA key exchange in old TLS):**
- Session key is encrypted with server's long-term RSA key
- Attacker records traffic, gets server's private key later (breach, court order, NSL)
- Can decrypt all historical recorded traffic

**With PFS (ECDHE):**
- Session key derived from ephemeral DH
- Ephemeral keys discarded — gone forever
- Past sessions are secure even if long-term key is compromised

---

### 8.6 HSTS — HTTP Strict Transport Security

**HSTS** is an HTTP security header that instructs browsers to **only connect to a site over HTTPS — never HTTP**.

```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

- `max-age`: How long (in seconds) to enforce HTTPS (31536000 = 1 year)
- `includeSubDomains`: Apply to all subdomains
- `preload`: Submit to browser preload list — HTTPS enforced even on first visit

**Why it matters:** Prevents SSL stripping attacks where an attacker downgrades your HTTPS connection to HTTP.

---

### 8.7 Certificate Transparency (CT)

**Certificate Transparency** is a public, append-only log of all publicly issued TLS certificates.

- All publicly trusted CAs are required to submit certificates to CT logs
- Anyone can query CT logs to find certificates issued for a domain
- Organizations can monitor CT logs for unauthorized certificates issued for their domains

**Tools:**
- [crt.sh](https://crt.sh) — Search CT logs for any domain
- [Certificate Transparency Monitor](https://developers.facebook.com/tools/ct/) — Facebook's CT monitoring

**Security value:** If an attacker gets a CA to issue a fraudulent certificate for your domain (e.g., google.com), you can detect it in CT logs. This is what helped detect the DigiNotar compromise in 2011 (Iranian CA issued fraudulent Google certificate).

---

## 9. Common Cryptographic Attacks

Understanding how cryptography fails helps you implement it correctly and identify vulnerabilities during security assessments.

---

### 9.1 Brute Force Attack

**What:** Try every possible key until the correct one is found.

**Effective against:** Weak keys (short key lengths), weak passwords

**Defense:** Use sufficient key lengths (AES-256, RSA-2048+, ECC P-256+)

**Example:** DES 56-bit key — 2^56 = ~72 quadrillion possible keys. Brute forced in 22 hours in 1998.

---

### 9.2 Dictionary Attack

**What:** Try a list of common words, phrases, and passwords rather than all possible combinations.

**Effective against:** Weak passwords (dictionary words, common substitutions)

**Defense:** Use strong, random passwords; use bcrypt/Argon2 for password hashing (slows the attack dramatically)

---

### 9.3 Rainbow Table Attack

**What:** Pre-computed table of hashes for a large set of possible passwords. Instead of computing hashes at attack time, look up pre-computed values.

```
Attacker has stolen hashed passwords:
5d41402abc4b2a76b9719d911017c592  ← MD5 hash

Rainbow table lookup:
5d41402abc4b2a76b9719d911017c592  → "hello"

Cracked instantly!
```

**Effective against:** Unsalted hashes (MD5, SHA-1, even SHA-256 without salt)

**Defense:** **Salting** — add a random value to the password before hashing. A unique salt per user means rainbow tables are ineffective (attacker would need a separate table for each salt).

```
Without salt: MD5("password") = 5f4dcc3b5aa765d61d8327deb882cf99
With salt:    MD5("x7kL9m" + "password") = completely different hash
```

---

### 9.4 Birthday Attack

**What:** Exploits the birthday paradox — it's much easier to find **any two inputs that hash to the same value** (collision) than to find an input that hashes to a specific value.

**Why it's called "birthday":** In a room of 23 people, there's a 50% chance two share a birthday — much less than you'd intuitively expect.

**Impact on hash security:**
- SHA-256 has 256-bit output → collision resistance is ~128-bit (2^128 operations)
- SHA-1 has 160-bit output → collision resistance is ~80-bit (2^80 operations)
- This is why MD5 and SHA-1 are broken — practical collisions were found

---

### 9.5 Padding Oracle Attack

**What:** Exploits error messages from servers that reveal whether decrypted data has valid padding.

**Affects:** AES-CBC mode when error messages differ between "decryption failed" and "padding invalid"

**Famous examples:**
- **POODLE** (2014) — SSL 3.0 CBC padding oracle
- **BEAST** (2011) — TLS 1.0 CBC chosen-plaintext
- **Lucky 13** — TLS MAC timing oracle

**Defense:** Use AES-GCM instead of AES-CBC; don't reveal specific error reasons; use `constant-time` comparison functions

---

### 9.6 Downgrade Attack

**What:** Attacker manipulates negotiation to force both parties to use an older, weaker protocol or cipher suite.

**Famous examples:**
- **POODLE** — Forced downgrade to SSL 3.0
- **FREAK** — Forced downgrade to export-grade RSA (512-bit)
- **Logjam** — Forced downgrade to export-grade DH (512-bit)
- **DROWN** — Used SSLv2 to attack TLS connections

**Defense:** Disable all insecure protocols (SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1); enforce TLS 1.2 minimum, TLS 1.3 preferred

---

### 9.7 MITM — Cryptographic Man-in-the-Middle

**What:** Attacker positions between two parties and intercepts/relays communications, presenting their own keys.

**Defense:** Certificates and PKI — the server presents a certificate signed by a trusted CA, and the client verifies it. Without a valid trusted certificate, MITM fails (unless the attacker can compromise a CA).

---

### 9.8 Key Reuse Attacks

**What:** Reusing the same key/nonce for multiple encryptions breaks many cipher schemes.

**Example — Nonce reuse in CTR/GCM:**
```
C1 = P1 XOR KeyStream(Nonce, Key)
C2 = P2 XOR KeyStream(Nonce, Key)  ← Same nonce!

C1 XOR C2 = P1 XOR P2  ← Plaintext XORed together — can recover both!
```

**Famous example:** WEP Wi-Fi — reused IVs (initialization vectors) due to small IV space, allowing complete decryption.

**Defense:** Never reuse nonces/IVs; use ephemeral keys; implement counter-based nonce generation

---

### 9.9 Quantum Computing Threats

Quantum computers threaten current asymmetric cryptography:

| Algorithm | Quantum Threat | Algorithm Used | Status |
|---|---|---|---|
| RSA | **Shor's algorithm** breaks it | Factoring | Vulnerable |
| ECDSA/ECDH | **Shor's algorithm** breaks it | Discrete log | Vulnerable |
| AES-256 | **Grover's algorithm** halves security | Search | AES-256 remains secure (128-bit post-quantum) |
| SHA-256 | Grover halves effective security | Search | SHA-256 remains secure |

**Post-Quantum Cryptography (PQC):**
NIST finalized its first PQC standards in 2024:
- **CRYSTALS-Kyber (FIPS 203)** — Key encapsulation (replaces RSA/ECDH)
- **CRYSTALS-Dilithium (FIPS 204)** — Digital signatures (replaces RSA/ECDSA)
- **SPHINCS+ (FIPS 205)** — Hash-based signatures (backup)

Migration to PQC is a long-term project — organizations should inventory their cryptographic usage now ("cryptographic agility").

---

## 10. Hands-On Lab: Hash Cracking & SSL Certificate Inspection

### Lab Part A: Hash Identification and Cracking

**Objective:** Identify hash types and crack weak hashes using online tools and Hashcat.

---

#### Step 1: Hash Identification

Identify the hash type for each of the following:

```
Hash 1: 5d41402abc4b2a76b9719d911017c592
Hash 2: aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
Hash 3: 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
Hash 4: $2b$12$LQv3c1yqBWVHxkd0LHAkCOYz6TtxMQJqhN8/LewdBPj/VcSAg/m2G
Hash 5: $argon2id$v=19$m=19456,t=2,p=1$...
```

**Tool:** Use `hashid` or `hash-identifier` in Kali:
```bash
hashid 5d41402abc4b2a76b9719d911017c592
hash-identifier
```

Or use the online tool: [https://hashes.com/en/tools/hash_identifier](https://hashes.com/en/tools/hash_identifier)

**Expected answers:**
- Hash 1: MD5
- Hash 2: SHA-1
- Hash 3: SHA-256
- Hash 4: bcrypt
- Hash 5: Argon2id

---

#### Step 2: Crack MD5 and SHA-1 Hashes Online

Visit [CrackStation](https://crackstation.net/) and try these hashes:

```
5d41402abc4b2a76b9719d911017c592
7c4a8d09ca3762af61e59520943dc26494f8941b
e10adc3949ba59abbe56e057f20f883e
```

Note: CrackStation has a database of over 15 billion entries. These should crack instantly.

**Record what password each hash represents.**

---

#### Step 3: Crack Hashes with Hashcat (Kali Linux)

First, make sure you have a wordlist:
```bash
ls /usr/share/wordlists/
# rockyou.txt is the most common — may need to decompress:
gunzip /usr/share/wordlists/rockyou.txt.gz
```

Create a file with hashes to crack:
```bash
cat > hashes.txt << 'EOF'
5d41402abc4b2a76b9719d911017c592
e10adc3949ba59abbe56e057f20f883e
827ccb0eea8a706c4c34a16891f84e7b
EOF
```

Run Hashcat (MD5 = mode 0):
```bash
hashcat -m 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

After cracking, show results:
```bash
hashcat -m 0 hashes.txt --show
```

**Common Hashcat modes:**

| Hash Type | Mode |
|---|---|
| MD5 | 0 |
| SHA-1 | 100 |
| SHA-256 | 1400 |
| SHA-512 | 1700 |
| bcrypt | 3200 |
| NTLM | 1000 |
| NetNTLMv2 | 5600 |
| WPA-PBKDF2 | 22000 |

---

#### Step 4: Understand Salting

Try cracking this salted hash on CrackStation:
```
$apr1$xyz12345$hashed_password_with_salt_value
```

Observe: Salted hashes often fail to crack with rainbow tables because no pre-computed table exists for this specific salt.

**Question to answer:** Why does adding a unique salt per user make rainbow table attacks impractical?

---

### Lab Part B: SSL Certificate Inspection

**Objective:** Inspect a real TLS certificate and understand its contents.

---

#### Method 1: Browser Inspection

1. Go to `https://www.google.com` (or any HTTPS site)
2. Click the padlock icon → Connection is secure → Certificate is valid
3. View Certificate
4. Record:
   - Subject (who the cert belongs to)
   - Issuer (which CA issued it)
   - Validity period (Not Before / Not After)
   - Signature algorithm
   - Public key algorithm and size
   - Subject Alternative Names (SANs)
   - Serial number

---

#### Method 2: OpenSSL Command Line

```bash
# View a website's certificate
openssl s_client -connect google.com:443 -showcerts 2>/dev/null | openssl x509 -noout -text

# Just the key fields
openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -subject -issuer -dates -fingerprint

# Check certificate expiry
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -enddate

# Check what TLS version the server supports
openssl s_client -connect google.com:443 -tls1_3 2>&1 | head -5
openssl s_client -connect google.com:443 -tls1_2 2>&1 | head -5
openssl s_client -connect google.com:443 -ssl3 2>&1 | head -5   # Should fail

# View the full certificate chain
openssl s_client -connect google.com:443 -showcerts 2>/dev/null
```

---

#### Method 3: crt.sh — Certificate Transparency

1. Go to [https://crt.sh/?q=google.com](https://crt.sh/?q=google.com)
2. View all certificates ever issued for google.com
3. Click on a certificate to view its full details
4. Notice: How many certificates exist? How many different CAs have issued certs for this domain?

---

#### Step: Generate Your Own Self-Signed Certificate

```bash
# Generate a private key (RSA 2048)
openssl genrsa -out mykey.pem 2048

# Generate a Certificate Signing Request (CSR)
openssl req -new -key mykey.pem -out mycsr.csr -subj "/CN=testlab.local/O=TestOrg/C=US"

# Self-sign the certificate (valid 365 days)
openssl x509 -req -days 365 -in mycsr.csr -signkey mykey.pem -out mycert.pem

# View the certificate
openssl x509 -in mycert.pem -noout -text

# Check what you created
openssl x509 -in mycert.pem -noout -subject -issuer -dates
```

**Question:** Why do browsers show a warning for self-signed certificates even though they use the same encryption?

---

### Lab Deliverable

Save your findings as `lab_05_cryptography.md` with:
1. Hash identification results for all 5 hashes
2. Cracked passwords from CrackStation
3. Hashcat output showing cracked hashes
4. Certificate inspection output from OpenSSL for google.com
5. Answers to both lab questions
6. Self-signed certificate details

---

## 11. Summary & Key Takeaways

### What You Learned in Module 5

**Cryptography Fundamentals:**
- Cryptography provides confidentiality (encryption), integrity (hashing), authentication and non-repudiation (digital signatures)
- Kerckhoffs's Principle — security must come from the key, not the algorithm's secrecy
- Three types: Symmetric (one key), Asymmetric (key pair), Hashing (one-way)

**Symmetric Encryption:**
- Same key encrypts and decrypts — fast but has key distribution problem
- AES-256-GCM is the gold standard; use AEAD modes
- DES is broken (56-bit); 3DES deprecated; RC4 broken — never use
- ChaCha20-Poly1305 is the modern alternative (excellent for mobile/IoT)
- Block cipher modes matter: ECB is insecure, GCM is best practice

**Asymmetric Encryption:**
- Public/private key pair solves key distribution
- RSA (factoring problem), ECC (elliptic curve discrete log) — ECC gives same security with much smaller keys
- Diffie-Hellman key exchange allows shared secret without transmitting it
- In practice: hybrid encryption (asymmetric for key exchange, symmetric for data)

**Hash Functions:**
- One-way, fixed-output, deterministic
- MD5 and SHA-1 are broken — collision attacks practical
- SHA-256 and SHA-3 are current standards
- For passwords: Argon2id > scrypt > bcrypt >> SHA-anything (never use fast hashes for passwords)
- Salting defeats rainbow table attacks
- HMAC adds authentication to hash functions

**Digital Signatures:**
- Sign with private key, verify with public key
- Provides authentication + integrity + non-repudiation
- Ed25519 is the modern recommendation; ECDSA widely used; RSA-PSS acceptable
- Used in TLS, SSH, code signing, email, documents, blockchain

**Digital Certificates & PKI:**
- X.509 certificates bind public keys to identities, signed by a CA
- Trust chain: Root CA → Intermediate CA → End-Entity Certificate
- Root CAs are pre-installed in OS/browser trust stores
- Certificate revocation: CRL (stale) or OCSP (real-time); OCSP Stapling is best practice
- DV/OV/EV validation levels; wildcards vs SAN certs

**TLS:**
- TLS 1.3 is the standard — TLS 1.0/1.1 deprecated, SSL is broken
- Provides confidentiality (AES-GCM), integrity (AEAD), authentication (certificates), PFS (ECDHE)
- Cipher suites specify all algorithms in one named package
- Perfect Forward Secrecy ensures past traffic is safe even if long-term key is compromised
- HSTS prevents SSL stripping; Certificate Transparency enables detection of rogue certificates

**Attacks:**
- Brute force → use long keys
- Dictionary/Rainbow tables → use strong passwords + salting + slow hash functions
- Padding oracle → use AEAD (GCM) not CBC
- Downgrade attacks → disable old protocols
- Quantum threat → RSA/ECC vulnerable to Shor's algorithm; NIST PQC standards finalized

---

## 12. Quiz: Test Your Knowledge

1. What are the three main types of cryptography?

2. Why is ECB mode considered insecure?

3. What is the current recommended block cipher mode for AES?

4. What is the "key distribution problem" and how does asymmetric cryptography solve it?

5. Why does ECC require smaller key sizes than RSA to achieve the same security level?

6. What is Diffie-Hellman used for, and why is the "ephemeral" (DHE/ECDHE) version important?

7. Why should SHA-256 (a fast hash) never be used for storing passwords?

8. What is a salt, and how does it defeat rainbow table attacks?

9. What is the difference between Argon2d, Argon2i, and Argon2id?

10. When you digitally sign a document, which key do you sign with? Which key does the recipient use to verify?

11. What is the difference between a Root CA and an Intermediate CA, and why do CAs use intermediate certificates?

12. What does "Perfect Forward Secrecy" mean and which key exchange mechanism provides it?

13. What are the key improvements of TLS 1.3 over TLS 1.2?

14. What is OCSP Stapling and why is it better than regular OCSP?

15. Why are MD5 and SHA-1 considered broken for cryptographic use?

16. What quantum computing algorithms threaten RSA and ECC, and which NIST-standardized algorithms replace them?

<details>
<summary>📋 Click to reveal answers</summary>

1. **Symmetric** (one shared key), **Asymmetric** (public/private key pair), **Hashing** (one-way, no key)

2. ECB encrypts each block **independently** — identical plaintext blocks produce identical ciphertext blocks, **revealing patterns** in the data (ECB Penguin example)

3. **AES-GCM** (Galois/Counter Mode) — provides authenticated encryption (AEAD), combines confidentiality and integrity

4. The key distribution problem: how to securely share a symmetric key without it being intercepted. Asymmetric crypto solves this by using public keys (can be shared openly) for encryption — only the private key (kept secret) can decrypt

5. ECC is based on the **elliptic curve discrete logarithm problem**, which is computationally harder to solve than RSA's integer factorization — meaning smaller keys provide equivalent security (e.g., 256-bit ECC ≈ 3072-bit RSA)

6. DH is used for **key exchange** — establishing a shared secret without transmitting it. Ephemeral (DHE/ECDHE) generates new keys per session, providing **Perfect Forward Secrecy** — past sessions remain secure even if long-term keys are compromised

7. SHA-256 is **intentionally fast** — GPUs can compute billions of hashes per second, making brute-force and dictionary attacks trivial. Password hashing needs to be **deliberately slow** (Argon2id, bcrypt)

8. A **salt** is a random unique value added to each password before hashing. It means two identical passwords produce different hashes, and attackers cannot use pre-computed rainbow tables — they'd need a separate table for every unique salt

9. **Argon2d** — resistant to GPU attacks (vulnerable to side-channels); **Argon2i** — resistant to side-channels (less GPU-resistant); **Argon2id** — hybrid, best of both; **recommended for password hashing**

10. You sign with your **private key**. The recipient verifies with your **public key**

11. Root CAs are self-signed and their private keys are kept **offline** in secure vaults. Intermediate CAs are signed by the Root and used for day-to-day certificate issuance. If an Intermediate is compromised, only it is revoked — the Root (and all other Intermediates) remain trusted

12. PFS means **past sessions remain secure even if the long-term private key is later compromised**. Achieved by **ECDHE** — new ephemeral key pairs generated per session, discarded after use; the long-term key is used only for authentication

13. TLS 1.3 improvements: **1-RTT handshake** (faster); removed weak algorithms (RSA key exchange, DH without PFS, RC4, DES, MD5, SHA-1); **mandatory ECDHE** for key exchange; **AEAD-only** cipher suites; **encrypted certificate** in handshake; 0-RTT resumption

14. **OCSP Stapling:** The server queries its own OCSP status and includes the signed response in the TLS handshake. The client doesn't need to contact the CA — improving **privacy** (CA doesn't see your connections), **performance** (no extra roundtrip), and **reliability** (no dependency on CA's OCSP server being up)

15. MD5 and SHA-1 are broken because **practical collision attacks** exist — two different inputs can be found that produce the same hash. For MD5, collisions are computed in seconds; SHA-1 was collided by Google's SHAttered attack (2017). Collision resistance is a fundamental requirement for cryptographic hash functions

16. **Shor's algorithm** breaks RSA and ECC on a quantum computer. NIST-standardized replacements: **CRYSTALS-Kyber (FIPS 203)** for key encapsulation and **CRYSTALS-Dilithium (FIPS 204)** for digital signatures

</details>

---

## 13. Resources

### Official Standards & Documentation
- [NIST SP 800-175B — Cryptographic Standards](https://csrc.nist.gov/publications/detail/sp/800-175b/rev-1/final)
- [NIST Post-Quantum Cryptography Standards](https://csrc.nist.gov/projects/post-quantum-cryptography)
- [RFC 8446 — TLS 1.3](https://datatracker.ietf.org/doc/html/rfc8446)
- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)

### Tools
- [CrackStation](https://crackstation.net/) — Online hash cracking (MD5, SHA-1, SHA-256)
- [Hashcat](https://hashcat.net/hashcat/) — Advanced password recovery
- [crt.sh](https://crt.sh/) — Certificate Transparency log search
- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/) — Test a website's TLS configuration
- [badssl.com](https://badssl.com/) — Test browser responses to bad TLS configurations

### Learning & Visualization
- [Cryptopaphy.io](https://cryptography.io/en/latest/) — Python cryptography library docs
- [Illustrated TLS 1.3](https://tls13.xargs.org/) — Visual walkthrough of every TLS 1.3 byte
- [Illustrated TLS 1.2](https://tls12.xargs.org/) — Same for TLS 1.2
- [Dan Boneh's Cryptography Course](https://www.coursera.org/learn/crypto) — Stanford Coursera (free to audit)

### Books
- *Serious Cryptography* by Jean-Philippe Aumasson — Best practical cryptography book
- *Cryptography Engineering* by Ferguson, Schneier, Kohno — Deep dive
- *The Code Book* by Simon Singh — Accessible history of cryptography

---

## Module Progress Checklist

Before moving to Module 6, confirm you can:

- [ ] Explain the difference between symmetric, asymmetric, and hash-based cryptography
- [ ] Name the recommended symmetric cipher and mode (AES-256-GCM) and explain why ECB is insecure
- [ ] Explain why ECC needs smaller keys than RSA for equivalent security
- [ ] Describe how Diffie-Hellman key exchange works and why ECDHE provides Perfect Forward Secrecy
- [ ] Identify which hash algorithms are broken (MD5, SHA-1) and which are current standard (SHA-256, SHA-3)
- [ ] Explain why Argon2id is required for password hashing instead of SHA-256
- [ ] Explain how a digital signature is created and verified
- [ ] Describe the X.509 certificate structure and what each field means
- [ ] Explain the PKI trust chain: Root CA → Intermediate CA → End-Entity Certificate
- [ ] Explain TLS 1.3's improvements over TLS 1.2
- [ ] Describe Perfect Forward Secrecy and why it matters
- [ ] Identify at least 5 cryptographic attacks and their defenses
- [ ] Complete the hash cracking lab and SSL certificate inspection lab

---

**Previous Module:** [← Module 4 — Networking Deep Dive Part 2](Module_04_Networking_Deep_Dive_Part_2.md)

**Next Module:** [Module 6 — Windows Security & Administration →](Module_06_Windows_Security_&_Administration.md)

---

*Module 5 | Phase 1: Absolute Foundations | cybersecurity_beginner_to_advance*
