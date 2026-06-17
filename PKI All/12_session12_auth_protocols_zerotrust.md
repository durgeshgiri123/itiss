# Session 12: Authentication Protocols, FIDO, Zero Trust Architecture

---

## 1. Authentication Protocol Recap & Deep Dive

Authentication protocols are standardized methods that allow systems to **verify identity** over a network securely.

### Core Challenge: Replay Attacks
A replay attack captures a valid authentication message and retransmits it later to gain unauthorized access.

**Defenses against replay:**
- **Nonces** (Number used Once) — random values included in each challenge
- **Timestamps** — messages expire after a short window
- **Sequence Numbers** — each message numbered; out-of-order = rejected

---

## 2. Password-Based Authentication Protocols

### 2.1 PAP — Password Authentication Protocol
- Sends **username + password in plaintext** over the link
- No encryption → highly insecure
- Used in older PPP (Point-to-Point Protocol) connections
- **Vulnerable to:** eavesdropping, replay

### 2.2 CHAP — Challenge Handshake Authentication Protocol
- Uses **challenge-response with MD5 hashing** — password never sent
- Flow:
  1. Server sends a **random challenge**
  2. Client computes `MD5(challenge + shared_secret)`
  3. Server verifies by computing the same hash
- **Re-authenticates periodically** during session
- **Vulnerable to:** dictionary attack (if MD5 broken), MitM if no mutual auth

### 2.3 MS-CHAPv2
- Microsoft's version of CHAP
- Adds **mutual authentication** (server also authenticates to client)
- Still MD5-based → deprecated; vulnerable to offline brute force
- Should be used with PEAP/TLS tunneling

---

## 3. Kerberos Authentication Protocol

### What is Kerberos?
A **ticket-based, symmetric-key authentication protocol** designed for open networks. Developed at MIT. Used heavily in **Windows Active Directory**.

### Key Entities
| Entity | Role |
|--------|------|
| **KDC** (Key Distribution Center) | Central trusted server = AS + TGS |
| **AS** (Authentication Server) | Issues Ticket Granting Ticket (TGT) |
| **TGS** (Ticket Granting Server) | Issues service tickets |
| **Client** | User requesting access |
| **Service Server (SS)** | Resource the client wants to access |

### Kerberos Flow (6 Steps)
```
1. Client → AS:        "I want to log in" (username, plaintext)
2. AS → Client:        TGT encrypted with KDC secret + Session Key encrypted with user's password hash
3. Client → TGS:       TGT + Authenticator (timestamp encrypted with session key) + service request
4. TGS → Client:       Service Ticket (encrypted with service's secret key) + new session key
5. Client → SS:        Service Ticket + new Authenticator
6. SS → Client:        Mutual auth confirmation (optional)
```

### Key Points
- Uses **symmetric encryption (AES in modern Kerberos)**
- Tickets have **lifetime (usually 8–10 hours)**
- **Replay protection** via timestamps (clocks must be within 5 minutes — clock skew)
- **No password sent over network** — only hashes/encrypted tickets

### Kerberos Attacks
| Attack | Description |
|--------|-------------|
| **Pass-the-Ticket** | Steal a TGT/service ticket and reuse it |
| **Golden Ticket** | Forge a TGT using stolen KRBTGT hash (unlimited access) |
| **Silver Ticket** | Forge a service ticket using service account hash |
| **Kerberoasting** | Request service tickets for offline password cracking |
| **AS-REP Roasting** | Target accounts with pre-auth disabled |

> **Interview Q:** What is a Golden Ticket attack?
> Attacker steals the **KRBTGT** account password hash from the DC and forges a TGT, granting domain-wide access even after password changes.

---

## 4. RADIUS & TACACS+

### RADIUS — Remote Authentication Dial-In User Service
- **AAA protocol** (Authentication, Authorization, Accounting)
- UDP-based (ports 1812/1813)
- Encrypts only the **password** in the access-request packet
- Combines Authentication + Authorization in one response
- Widely used for: Wi-Fi (WPA2-Enterprise), VPN, network access

### TACACS+ — Terminal Access Controller Access Control System Plus
- Cisco proprietary AAA protocol
- **TCP-based** (port 49) — more reliable
- Encrypts the **entire payload** (more secure than RADIUS)
- **Separates** Authentication, Authorization, and Accounting
- Better for **network device management** (routers, switches)

### RADIUS vs TACACS+
| Feature | RADIUS | TACACS+ |
|---------|--------|---------|
| Transport | UDP | TCP |
| Encryption | Password only | Full payload |
| AAA | Combined | Separated |
| Use Case | Network access | Device admin |
| Open Standard | Yes | Cisco proprietary |

---

## 5. LDAP & Active Directory Authentication

### LDAP — Lightweight Directory Access Protocol
- Protocol for accessing and maintaining **directory services**
- Port **389** (plain), **636** (LDAPS — over TLS)
- Used to look up users, groups, computers in a directory
- Bind operation = authentication in LDAP

### Active Directory (AD)
- Microsoft's directory service using **LDAP + Kerberos + DNS**
- Organizes objects: Users, Groups, Computers, OUs (Organizational Units)
- **Domain Controller (DC)** = server running AD services
- **Forest > Domain > OU** hierarchy

---

## 6. SAML — Security Assertion Markup Language

- **XML-based** open standard for exchanging authentication/authorization data
- Enables **Single Sign-On (SSO)** between Identity Provider (IdP) and Service Provider (SP)
- Flow:
  1. User accesses SP
  2. SP redirects to IdP
  3. User authenticates at IdP
  4. IdP sends **SAML Assertion** (XML) back to SP
  5. SP grants access based on assertion

### SAML Assertion Types
- **Authentication** — User was authenticated at time X
- **Attribute** — User has attributes (email, role)
- **Authorization Decision** — User is permitted/denied

---

## 7. OAuth 2.0 & OpenID Connect (OIDC)

### OAuth 2.0 — Authorization Framework
- **NOT an authentication protocol** — it's for **authorization** (delegated access)
- Grants third-party apps limited access to user resources **without sharing passwords**
- Uses **Access Tokens**

### OAuth 2.0 Grant Types
| Grant Type | Use Case |
|-----------|---------|
| Authorization Code | Web apps (most secure) |
| Implicit | Single-page apps (deprecated) |
| Client Credentials | Machine-to-machine |
| Resource Owner Password | Trusted apps (legacy) |
| PKCE (Proof Key for Code Exchange) | Mobile/SPA apps |

### OpenID Connect (OIDC)
- **Authentication layer ON TOP of OAuth 2.0**
- Adds **ID Token** (JWT format) that contains user identity info
- Enables SSO for web/mobile apps
- `id_token` contains: `sub` (subject/user ID), `iss` (issuer), `aud` (audience), `exp` (expiry)

> **Tricky Q:** OAuth 2.0 is for authorization; OIDC is for authentication. OIDC uses OAuth 2.0 underneath.

---

## 8. FIDO — Fast Identity Online

### What is FIDO?
An open industry alliance (FIDO Alliance) that develops **strong, passwordless authentication standards** using public key cryptography.

### FIDO Standards Evolution
| Standard | Description |
|----------|-------------|
| **FIDO U2F** | Universal 2nd Factor — USB/NFC security key as 2FA |
| **FIDO UAF** | Universal Authentication Framework — passwordless (fingerprint, face) |
| **FIDO2** | Modern standard combining WebAuthn + CTAP |
| **WebAuthn** | W3C API for FIDO2 in browsers |
| **CTAP** | Client to Authenticator Protocol — how device talks to authenticator |

### FIDO2 Architecture
```
Browser/App (Relying Party Client)
        ↕ WebAuthn API
Browser/Platform
        ↕ CTAP2
Authenticator (Security Key / TPM / Biometric)
```

### FIDO2 Registration Flow
1. User initiates registration at Relying Party (RP)
2. RP sends **challenge**
3. Authenticator generates **public/private key pair** for this RP
4. Authenticator signs challenge with **private key**
5. **Public key** + signed challenge sent to RP
6. RP stores public key linked to user account

### FIDO2 Authentication Flow
1. User initiates login
2. RP sends challenge
3. Authenticator **signs challenge with stored private key**
4. RP verifies signature with stored public key
5. Access granted — **no password ever transmitted**

### FIDO2 Security Properties
- **Phishing resistant** — keys are bound to origin (RP ID), cannot be used on fake sites
- **No shared secrets** — private key never leaves device
- **No passwords** — eliminates credential stuffing
- **Replay resistant** — challenge is unique per authentication

### Authenticator Types
| Type | Example |
|------|---------|
| **Roaming Authenticator** | YubiKey, Google Titan Key |
| **Platform Authenticator** | Windows Hello, Touch ID, Face ID |

> **Interview Q:** How does FIDO2 prevent phishing?
> Keys are cryptographically bound to the **Relying Party ID (origin)**. A key registered at `bank.com` cannot be used on `fake-bank.com` — the origin check fails.

### Passkeys (Latest Evolution)
- **Passkeys** = FIDO2 credentials synced across devices via cloud (iCloud Keychain, Google Password Manager)
- Multi-device credentials — survives device loss
- Combines convenience of passwords with security of FIDO2

---

## 9. Zero Trust Architecture (ZTA)

### What is Zero Trust?
A security model based on the principle: **"Never Trust, Always Verify"**

Traditional perimeter security assumed: *inside the network = trusted*. Zero Trust assumes: **breach is inevitable; verify everything always**.

> **NIST SP 800-207** defines Zero Trust Architecture.

### Core Zero Trust Principles
1. **Verify explicitly** — Always authenticate and authorize based on all available data points
2. **Use least privilege access** — Limit access with Just-In-Time (JIT) and Just-Enough-Access (JEA)
3. **Assume breach** — Minimize blast radius; segment access; encrypt everything

### Zero Trust vs Traditional Perimeter Model
| Traditional | Zero Trust |
|------------|-----------|
| Trust inside network | No implicit trust anywhere |
| VPN-centric | Identity-centric |
| Flat network | Micro-segmentation |
| Perimeter firewall | Continuous verification |
| "Castle and moat" | "Verify every transaction" |

### Zero Trust Components (NIST)
| Component | Role |
|-----------|------|
| **Policy Engine (PE)** | Makes access decisions (allow/deny) |
| **Policy Administrator (PA)** | Executes decisions; manages session tokens |
| **Policy Enforcement Point (PEP)** | Enforces access; gateway between subject and resource |
| **Trust Algorithm** | Logic used by PE to evaluate requests |

### Zero Trust Pillars (Microsoft/CISA Model)
1. **Identity** — Verify every identity with strong auth (MFA, FIDO2)
2. **Devices** — Ensure device compliance/health
3. **Network** — Micro-segmentation, encrypt all traffic
4. **Applications** — Secure app access; shadow IT discovery
5. **Data** — Classify, label, protect data
6. **Infrastructure** — Harden cloud/on-prem infra

### Zero Trust Key Technologies
- **MFA** — Mandatory for all users
- **IAM** — Identity and Access Management
- **PAM** — Privileged Access Management
- **SIEM** — Security Information and Event Management
- **Micro-segmentation** — Network divided into small zones
- **SD-WAN / SASE** — Secure Access Service Edge
- **EDR** — Endpoint Detection and Response

### Software Defined Perimeter (SDP)
- Implements Zero Trust for network access
- All resources hidden (dark cloud) until authenticated
- **1-packet authorization (SPA)** — single encrypted packet to unlock access

---

## 10. IEEE 802.1X — Port-Based Network Access Control

- Standard for **network access control at the port level**
- Prevents unauthorized devices from connecting to LAN/Wi-Fi
- Components:
  - **Supplicant** — Client device
  - **Authenticator** — Switch/AP
  - **Authentication Server** — RADIUS server
- Uses **EAP (Extensible Authentication Protocol)** for actual authentication

### EAP Methods
| Method | Description |
|--------|-------------|
| **EAP-TLS** | Certificate-based — most secure |
| **PEAP** | Protected EAP — wraps EAP in TLS tunnel |
| **EAP-TTLS** | Tunneled TLS |
| **EAP-FAST** | Cisco's flexible authentication via secure tunneling |

---

## 11. Biometric Authentication

### Types of Biometrics
| Type | Example |
|------|---------|
| **Physiological** | Fingerprint, Iris, Face, DNA, Palm vein |
| **Behavioral** | Keystroke dynamics, Gait, Voice pattern |

### Biometric Accuracy Metrics
| Metric | Meaning |
|--------|---------|
| **FAR** (False Acceptance Rate) | Unauthorized user accepted — Type II error |
| **FRR** (False Rejection Rate) | Authorized user rejected — Type I error |
| **EER / CER** (Equal/Crossover Error Rate) | Point where FAR = FRR — lower = better |
| **CER** | Comparative metric for biometric systems |

> **Tricky Q:** Lower EER means better biometric accuracy. If FAR is reduced, FRR increases and vice versa.

### Biometric Attacks
- **Spoofing** — Fake fingerprint (gelatin), photo for face recognition
- **Replay** — Reusing recorded biometric data
- **Template Theft** — Stealing stored biometric templates

---

## 12. Tricky & Frequently Asked Interview Questions

**Q1: What is the difference between RADIUS and TACACS+?**
> RADIUS uses UDP, encrypts only the password, and combines auth+authorization. TACACS+ uses TCP, encrypts the full payload, and separates AAA — better for device administration.

**Q2: Explain the Kerberos golden ticket attack.**
> Attacker obtains the KRBTGT account's password hash (via DC compromise) and uses it to forge any TGT for any user, granting unlimited domain access. Even resetting user passwords won't help — KRBTGT must be reset twice.

**Q3: What is FIDO2 and why is it phishing-resistant?**
> FIDO2 uses public key cryptography; keys are bound to specific origins. A phishing site with a different domain cannot trigger the legitimate key.

**Q4: What is the difference between OAuth 2.0 and OpenID Connect?**
> OAuth 2.0 is an authorization framework for delegated access. OIDC adds an identity layer on top of OAuth 2.0 for authentication, providing an ID Token (JWT) with user identity claims.

**Q5: What is Zero Trust and what problem does it solve?**
> ZTA eliminates implicit trust based on network location. It solves the problem of lateral movement after a breach — even inside the network, every request is verified.

**Q6: What is EER in biometrics?**
> Equal Error Rate — the point where FAR equals FRR. A lower EER indicates a more accurate biometric system.

**Q7: What is SAML used for?**
> SAML is an XML-based standard for SSO — it allows users to authenticate once at an IdP and access multiple SPs without re-authenticating.

**Q8: What is clock skew in Kerberos?**
> Kerberos requires clocks to be synchronized within 5 minutes. A greater difference (clock skew) causes authentication failures because timestamps are used for replay prevention.

**Q9: How does 802.1X work?**
> The supplicant (client) connects to the authenticator (switch/AP) which blocks all traffic except EAP. The authenticator relays EAP messages to a RADIUS server. On successful auth, the port is opened.

**Q10: What are passkeys?**
> Passkeys are FIDO2 credentials that sync across devices via cloud (iCloud Keychain, Google PM). They replace passwords entirely with biometric/PIN-protected public key cryptography.

---

## Quick Revision Mnemonics

- **Kerberos** = KDC issues TGT → TGT gets Service Ticket → Service Ticket gets access
- **Zero Trust** = "Never Trust, Always Verify" (NTAV)
- **FIDO2** = WebAuthn (browser) + CTAP (device)
- **RADIUS vs TACACS+** = R=UDP+Password-only; T=TCP+Full-encrypt+Separated-AAA
- **Biometrics** = FAR (fake accepted) + FRR (real rejected) → EER = crossover point
