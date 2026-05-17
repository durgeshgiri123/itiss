# Session 3: Web Application Security Risks, Threat Modelling & Burp Suite

> **Exam Weight:** High | **Type:** Theory + Lab

---

## 1. Web Application Security Risks

### 1.1 Why Web Applications Are High-Risk
- Accessible from anywhere on the internet
- Complex codebases = more attack surface
- Often handle sensitive data (PII, financial)
- Dynamic content created from user input
- Mix of client-side and server-side logic

### 1.2 Attack Surface Components
```
[User] ─── HTTP/HTTPS ─── [Web Server] ─── [App Server] ─── [DB Server]
              ↓                  ↓                ↓                ↓
         Input fields       Web server        App code         Database
         Cookies            config            Libraries        Stored procs
         Headers            SSL/TLS           Sessions         User data
         URL params         File system       APIs             Schema
```

### 1.3 Common Web Application Risks

| Risk | Description | Example |
|------|-------------|---------|
| **Authentication failures** | Weak login mechanisms | No account lockout |
| **Session management flaws** | Predictable session IDs | Sequential tokens |
| **Injection** | Malicious input executed | SQLi, XSS |
| **Sensitive data exposure** | Unencrypted PII | Credit card in plain text |
| **Insecure deserialization** | Malicious serialized objects | Java object injection |
| **CSRF** | Forced actions on behalf of user | Fund transfer via forged request |
| **XXE** | XML External Entity injection | File read via XML parser |
| **Open Redirects** | Redirect to malicious site | `?redirect=http://evil.com` |

---

## 2. Identifying Application Security Risks

### 2.1 Risk Formula
```
Risk = Likelihood × Impact

Likelihood factors:
  - Skill level of attacker required
  - How easy is the exploit?
  - Is it automated?

Impact factors:
  - Financial damage
  - Reputation damage
  - Legal consequences
  - Data loss
```

### 2.2 Risk Rating Systems

#### CVSS (Common Vulnerability Scoring System)
- Industry standard for rating vulnerability severity
- Score: **0–10** (None → Low → Medium → High → Critical)
- CVSS v3.1 components:

| Metric Group | Metrics |
|-------------|---------|
| **Base** | Attack Vector, Complexity, Privileges Required, User Interaction, Scope, CIA Impact |
| **Temporal** | Exploit maturity, Remediation level, Report confidence |
| **Environmental** | Customized CIA requirements for your environment |

> **Tricky Q:** What does a CVSS score of 10.0 mean?
> **A:** Critical severity — network-accessible, no authentication required, no user interaction, full CIA impact.

#### DREAD Model (Older Microsoft model)
- **D**amage potential
- **R**eproducibility
- **E**xploitability
- **A**ffected users
- **R**iscoverability
- Score each 1–10, average = risk score

---

## 3. Threat Risk Modelling

### 3.1 What is Threat Modelling?
A **structured process** to identify, quantify, and address security threats in a system. Done during **design phase** (most effective) but can be done anytime.

**Goal:** Find vulnerabilities before attackers do.

### 3.2 STRIDE Model (Microsoft)

| Letter | Threat | Security Property Violated | Example |
|--------|--------|---------------------------|---------|
| **S** | Spoofing | Authentication | Fake IP address |
| **T** | Tampering | Integrity | Modifying database records |
| **R** | Repudiation | Non-repudiation | Denying an action was performed |
| **I** | Information Disclosure | Confidentiality | Data leakage |
| **D** | Denial of Service | Availability | SYN flood |
| **E** | Elevation of Privilege | Authorization | User becoming admin |

> **Tricky Q:** What does STRIDE stand for?
> **A:** Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege

> **Tricky Q:** Which STRIDE category maps to OWASP A01 Broken Access Control?
> **A:** Elevation of Privilege (E)

### 3.3 Threat Modelling Process (4 Steps)

```
Step 1: DECOMPOSE the application
  → Data Flow Diagrams (DFDs)
  → Identify trust boundaries, data stores, external entities

Step 2: IDENTIFY threats
  → Apply STRIDE to each component
  → Use attack trees

Step 3: RANK threats
  → Use CVSS or DREAD
  → Prioritize by risk score

Step 4: MITIGATE
  → Redesign, add controls, accept, or transfer risk
```

### 3.4 Attack Trees
Hierarchical diagrams showing how an attacker can achieve a goal.
```
Goal: Steal customer credit cards
├── SQL Injection → dump card table
├── XSS → steal session → impersonate user → view cards
├── Phishing → steal credentials → login as user
└── Insider threat → direct DB access
```

### 3.5 PASTA (Process for Attack Simulation and Threat Analysis)
7-stage risk-centric model:
1. Define objectives
2. Define technical scope
3. Application decomposition
4. Threat analysis
5. Vulnerability and weakness analysis
6. Attack enumeration
7. Risk/impact analysis

---

## 4. HTTP Fields (Headers) — Critical for Exams

### 4.1 Security-Relevant HTTP Request Headers

| Header | Purpose | Attack Relevance |
|--------|---------|-----------------|
| `Host` | Target hostname | Virtual host confusion |
| `User-Agent` | Client info | User-agent spoofing |
| `Referer` | Previous page | Information leakage |
| `Cookie` | Session data | Session hijacking |
| `Authorization` | Credentials | Credential exposure |
| `X-Forwarded-For` | Real IP behind proxy | IP spoofing |
| `Content-Type` | Request body format | CSRF, file upload bypass |

### 4.2 Security HTTP Response Headers

| Header | What it does | Example Value |
|--------|-------------|---------------|
| `Strict-Transport-Security` | Force HTTPS | `max-age=31536000; includeSubDomains` |
| `Content-Security-Policy` | Prevent XSS/clickjacking | `default-src 'self'` |
| `X-Frame-Options` | Prevent clickjacking | `DENY` or `SAMEORIGIN` |
| `X-Content-Type-Options` | Prevent MIME sniffing | `nosniff` |
| `X-XSS-Protection` | Browser XSS filter | `1; mode=block` |
| `Cache-Control` | Caching policy | `no-store` for sensitive pages |
| `Set-Cookie` flags | Cookie security | `HttpOnly; Secure; SameSite=Strict` |

> **Tricky Q:** What does the `HttpOnly` cookie flag do?
> **A:** Prevents JavaScript from accessing the cookie (mitigates cookie theft in XSS attacks). The cookie is only sent in HTTP requests.

> **Tricky Q:** What header prevents clickjacking?
> **A:** `X-Frame-Options: DENY` or `Content-Security-Policy: frame-ancestors 'none'`

> **Tricky Q:** What is HSTS?
> **A:** HTTP Strict Transport Security — tells browsers to only connect via HTTPS, preventing SSL stripping attacks.

### 4.3 HTTP Methods and Security Risks

| Method | Purpose | Risk |
|--------|---------|------|
| GET | Retrieve data | Data in URL (logs, history) |
| POST | Send data | CSRF vulnerabilities |
| PUT | Replace resource | Unauthorized file upload |
| DELETE | Remove resource | Unauthorized deletion |
| OPTIONS | List allowed methods | Information disclosure |
| TRACE | Echo request | Cross-Site Tracing (XST) |
| PATCH | Partial update | Injection if unsanitized |

> **Tricky Q:** Which HTTP method is used in Cross-Site Tracing (XST) attacks?
> **A:** TRACE — echoes the request back, including cookies, enabling XSS even with HttpOnly flag

---

## 5. Burp Suite — Complete Overview

### 5.1 What is Burp Suite?
A **web application security testing platform** by PortSwigger. Acts as a proxy between browser and web server, intercepting and modifying all traffic.

**Versions:** Community (free), Professional, Enterprise

### 5.2 Core Components

#### Proxy
- Intercepts HTTP/HTTPS traffic between browser and server
- Can view, modify, and replay requests
- Must configure browser to use Burp as proxy (default: `127.0.0.1:8080`)
- Requires **Burp CA certificate** installed in browser for HTTPS

#### Spider (now called Crawler)
- Automatically crawls the web application
- Maps all pages, endpoints, forms, and links
- Builds a site map for attack surface discovery

#### Scanner (Pro only)
- Automated vulnerability scanning
- Detects: SQLi, XSS, XXE, SSRF, open redirects, etc.
- Passive scan (analyzes traffic) + Active scan (sends test payloads)

#### Intruder
- Automated customized attacks
- 4 attack types:

| Type | Description | Use Case |
|------|-------------|----------|
| **Sniper** | One payload position, one payload list | Single field fuzzing |
| **Battering Ram** | Same payload in all positions | Same username & password test |
| **Pitchfork** | Multiple payload lists, parallel | Username + matching password list |
| **Cluster Bomb** | Multiple payload lists, all combinations | Credential brute force |

> **Tricky Q:** Which Burp Intruder attack type tests all combinations of multiple payload lists?
> **A:** Cluster Bomb

#### Repeater
- Manually replay and modify individual requests
- Great for testing specific vulnerabilities
- Change parameters, headers, methods manually

#### Decoder
- Encode/decode: URL, HTML, Base64, Hex, Gzip, etc.
- Useful for analyzing obfuscated payloads

#### Comparer
- Diff tool to compare two requests/responses
- Useful for finding subtle differences in authentication responses

#### Sequencer
- Analyzes randomness of session tokens
- Tests if tokens are predictable (poor entropy)

#### Extender
- Plugin system (BApp Store)
- Custom extensions in Java/Python

### 5.3 Configuring Burp Suite with Browser
```
1. Open Burp Suite → Proxy tab → Options → Proxy Listeners
   → Confirm listening on 127.0.0.1:8080

2. Browser → Settings → Manual Proxy:
   HTTP Proxy: 127.0.0.1  Port: 8080
   Also use for HTTPS: ✓

3. Visit: http://burp → Download CA certificate
4. Browser → Import CA certificate → Trust for websites

5. Burp Proxy → Intercept is ON
6. Browse target site → requests appear in Burp
```

---

## 6. Exam Questions Bank

**Q1:** What is the STRIDE model used for?
**A:** Threat modelling — categorizing threats as Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege

**Q2:** What CVSS score range is considered "Critical"?
**A:** 9.0 – 10.0

**Q3:** What port does Burp Suite proxy listen on by default?
**A:** 8080 (127.0.0.1:8080)

**Q4:** Which Burp Suite tool analyzes session token randomness?
**A:** Sequencer

**Q5:** What HTTP header prevents MIME-type sniffing?
**A:** `X-Content-Type-Options: nosniff`

**Q6:** What is passive vs active scanning in Burp Suite?
**A:** Passive = analyzes existing traffic without sending new requests. Active = sends test payloads to detect vulnerabilities.

**Q7:** What does a Data Flow Diagram (DFD) show in threat modelling?
**A:** How data moves through a system, trust boundaries, processes, data stores, and external entities

**Q8:** What is an "attack surface"?
**A:** All the points where an attacker can try to enter or extract data from a system

---

*Next: Session 4 → Data Extraction, Advanced Exploitation, HTTP Methods, Wireshark*
