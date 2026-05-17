# Sessions 6–9: Security Management, Cyber Laws & Ethical Hacking Foundations

> **Exam Weight:** High (Theory-heavy) | **Type:** Theory

---

# SESSION 6: Security Management Concepts & Principles

## 1. Information Security Fundamentals

### 1.1 CIA Triad (Must Know!)

| Property | Meaning | Threat | Control |
|----------|---------|--------|---------|
| **Confidentiality** | Only authorized access | Eavesdropping, data breach | Encryption, access control |
| **Integrity** | Data is accurate & unaltered | Tampering, man-in-middle | Hash functions, digital signatures |
| **Availability** | Systems accessible when needed | DoS, hardware failure | Redundancy, backups, load balancing |

**Extended to CIAAN (also important):**
- **Authentication** — verifying identity
- **Non-repudiation** — can't deny an action (digital signatures)

> **Tricky Q:** A hacker encrypts all your files and demands ransom. Which CIA properties are violated?
> **A:** Availability (can't access files) and Confidentiality (files accessed by attacker). Possibly Integrity if files are modified.

### 1.2 Security Management Principles

| Principle | Description |
|-----------|-------------|
| **Least Privilege** | Give minimum permissions needed |
| **Defense in Depth** | Multiple layers of security (not one solution) |
| **Fail Secure / Fail Safe** | On failure, default to secure state (deny all) |
| **Separation of Duties** | No single person controls critical process |
| **Need to Know** | Access only to information required for job |
| **Accountability** | Actions can be traced to an individual |
| **Open Design** | Security should not depend on secrecy of mechanism (Kerckhoffs's principle) |

> **Tricky Q:** What is Kerckhoffs's principle?
> **A:** A cryptographic system should be secure even if everything about the system (except the key) is public knowledge. Security through obscurity is NOT a valid security model.

### 1.3 Security Policies
- **Security Policy** — high-level rules (what is required)
- **Security Standard** — specific requirements (how to measure compliance)
- **Security Guideline** — recommendations (not mandatory)
- **Security Procedure** — step-by-step instructions (how to do it)

### 1.4 Risk Management

```
Risk = Threat × Vulnerability × Asset Value

Risk Treatment Options:
├── AVOID    → Eliminate the activity
├── MITIGATE → Reduce likelihood or impact (controls)
├── TRANSFER → Insurance, outsourcing
└── ACCEPT   → Acknowledge and accept the risk
```

---

## 2. Human Side of Information Security

### 2.1 Social Engineering — The Human Weakness
**"The weakest link in security is the human."**

Social engineering exploits **human psychology** rather than technical vulnerabilities.

#### Types of Social Engineering

| Attack | Description | Example |
|--------|-------------|---------|
| **Phishing** | Mass email impersonating trusted entity | Fake bank email |
| **Spear Phishing** | Targeted phishing (specific person/org) | Email to CFO from "CEO" |
| **Whaling** | Spear phishing targeting executives | CEO/CFO targeted |
| **Vishing** | Voice/phone phishing | Fake tech support call |
| **Smishing** | SMS phishing | "Your account is locked, click here" |
| **Baiting** | Physical media left for victim to use | USB drive in parking lot |
| **Pretexting** | Creating fabricated scenario | Impersonating IT support |
| **Tailgating** | Unauthorized physical access by following | Following someone through badge door |
| **Quid Pro Quo** | Offering service in exchange for info | "I'll fix your computer, give me password" |
| **Watering Hole** | Infecting websites targets visit | Compromise an industry forum |

> **Tricky Q:** What is the difference between phishing and spear phishing?
> **A:** Phishing = generic bulk email to many people. Spear phishing = targeted, personalized email to specific individual using their name, organization, context.

> **Tricky Q:** What is tailgating vs piggybacking?
> **A:** Tailgating = following someone without their knowledge. Piggybacking = following someone WITH their knowledge and consent (they hold the door).

### 2.2 Security Awareness Training
- Train employees to recognize social engineering
- Test with **simulated phishing campaigns**
- Report suspicious activity
- Clear desk policy
- Visitor management procedures

---

## 3. Threats to Information Systems

### 3.1 Threat Categories

| Category | Examples |
|----------|---------|
| **Human threats (unintentional)** | Employee mistakes, misconfiguration |
| **Human threats (intentional)** | Hackers, insiders, espionage |
| **Environmental** | Fire, flood, earthquake |
| **Technical** | Hardware failure, software bugs |
| **Operational** | Poor processes, inadequate training |

### 3.2 Insider Threats
- **Malicious insider** — intentionally steals/damages
- **Negligent insider** — accidentally causes breach
- **Compromised insider** — credentials stolen, used by external attacker

> **Tricky Q:** Which type of insider threat is hardest to detect?
> **A:** Compromised insider — their activities look like legitimate user behavior since they use valid credentials.

### 3.3 Advanced Persistent Threat (APT)
- **Long-term**, stealthy attack campaign
- Usually **nation-state sponsored**
- Goal: espionage, data theft (not immediate disruption)
- Phases: Reconnaissance → Intrusion → Persistence → Lateral Movement → Exfiltration

---

---

# SESSION 7: Security in Mobile/Wireless, Credit Card Frauds

## 1. Mobile Security Threats

### 1.1 Wireless Security Risks

| Risk | Description |
|------|-------------|
| **Eavesdropping** | Intercepting wireless traffic |
| **Evil Twin AP** | Fake WiFi hotspot with same SSID |
| **Rogue Access Point** | Unauthorized AP on corporate network |
| **War Driving** | Driving around scanning for open WiFi |
| **War Chalking** | Marking locations of open WiFi |
| **MITM on WiFi** | ARP spoofing on WiFi network |
| **Deauthentication Attack** | Forcing clients to disconnect (prep for WPA capture) |
| **KARMA Attack** | Responding to any WiFi probe request |

### 1.2 WiFi Security Protocols (Exam Critical!)

| Protocol | Year | Encryption | Key Issues |
|----------|------|-----------|------------|
| **WEP** | 1997 | RC4 (40/104-bit) | Broken — IV reuse, cracked in minutes |
| **WPA** | 2003 | TKIP (RC4) | TKIP vulnerable, transitional |
| **WPA2** | 2004 | AES-CCMP | KRACK attack (2017), still used |
| **WPA3** | 2018 | AES-GCMP-256 | SAE replaces PSK handshake, most secure |

> **Tricky Q:** Why was WEP broken?
> **A:** WEP reuses Initialization Vectors (IVs) which are only 24-bit, making statistical analysis feasible. RC4 with repeated IVs reveals the keystream, allowing key recovery.

> **Tricky Q:** What is KRACK attack?
> **A:** Key Reinstallation Attack (2017) — targets WPA2's 4-way handshake by replaying handshake messages to reinstall an already-used key, allowing decryption and replay of traffic.

### 1.3 Credit Card Frauds in Mobile/Wireless Computing

| Fraud Type | Method |
|-----------|--------|
| **Card Skimming** | Physical device on ATM/POS captures card data |
| **NFC Sniffing** | Reading contactless card data wirelessly |
| **Replay Attack** | Reusing intercepted transaction data |
| **SIM Swapping** | Social engineering carrier to transfer number (bypass OTP) |
| **Man-in-the-Browser** | Malware intercepts transactions in browser |
| **Formjacking** | Injecting malicious JS into payment forms |

> **Tricky Q:** What is SIM swapping and how does it affect 2FA?
> **A:** Attacker convinces carrier to transfer victim's phone number to attacker's SIM. This allows attacker to receive SMS OTPs, bypassing SMS-based two-factor authentication.

### 1.4 Information Security Management (ISM)

**ISO 27001** — International standard for Information Security Management Systems (ISMS)

Key components:
- Risk assessment and treatment
- Security policies
- Asset management
- Access control
- Cryptography
- Physical security
- Incident management
- Business continuity

**ISMS = People + Processes + Technology**

---

---

# SESSION 8: Cyber Crimes & Ethical Hacking Introduction

## 1. Cyber Crimes

### 1.1 Classification of Cyber Crimes

| Category | Examples |
|----------|---------|
| **Against individuals** | Cyberstalking, identity theft, phishing |
| **Against organizations** | Corporate espionage, DoS, data breach |
| **Against government** | Cyberterrorism, espionage, defacement |
| **Against property** | Unauthorized access, vandalism |

### 1.2 Types of Cyber Crimes

| Crime | Description |
|-------|-------------|
| **Hacking** | Unauthorized access to systems |
| **Phishing** | Stealing credentials via fake websites |
| **Identity Theft** | Stealing personal information for fraud |
| **Ransomware** | Encrypting data and demanding payment |
| **Cyberstalking** | Harassment via internet |
| **Child Pornography** | Distribution of CSAM (illegal globally) |
| **Cyber Terrorism** | Using cyberattacks for political/social goals |
| **IPR violation** | Software piracy, copyright infringement |
| **Fraud** | Online financial scams |

---

## 2. Indian Legal Framework for Cyber Crime

### 2.1 Information Technology Act, 2000 (IT Act)
India's primary cyber law:

| Section | Offence | Punishment |
|---------|---------|-----------|
| **Section 43** | Unauthorized access, damage to computer | Civil — compensation |
| **Section 66** | Computer related offences (hacking) | 3 years + fine |
| **Section 66A** | Sending offensive messages (STRUCK DOWN by SC) | — |
| **Section 66B** | Receiving stolen computer resource | 3 years + ₹1 lakh fine |
| **Section 66C** | Identity theft | 3 years + ₹1 lakh fine |
| **Section 66D** | Cheating by personation using computer | 3 years + ₹1 lakh fine |
| **Section 66E** | Violation of privacy (publishing images) | 3 years + ₹2 lakh fine |
| **Section 66F** | Cyber terrorism | Life imprisonment |
| **Section 67** | Publishing obscene material electronically | 3 years + ₹5 lakh fine |
| **Section 69** | Government power to intercept communications | — |
| **Section 72** | Breach of confidentiality | 2 years + fine |

> **Tricky Q:** Which Section of IT Act deals with identity theft?
> **A:** Section 66C — Identity theft carries 3 years imprisonment + ₹1 lakh fine

> **Tricky Q:** Section 66A was struck down. By which case?
> **A:** Shreya Singhal vs. Union of India (2015) — Supreme Court struck down Section 66A as unconstitutional, violating freedom of speech.

### 2.2 Indian Penal Code (IPC) Sections Relevant to Cyber Crime

| IPC Section | Offence |
|------------|---------|
| **Section 420** | Cheating and dishonestly inducing delivery of property |
| **Section 463-465** | Forgery |
| **Section 499** | Defamation |
| **Section 500** | Punishment for defamation |
| **Section 503** | Criminal intimidation |
| **Section 506** | Punishment for criminal intimidation |

### 2.3 International Laws
- **Budapest Convention** (2001) — First international treaty on cybercrime; India is NOT a signatory
- **GDPR** (EU) — Data protection regulation; fines up to 4% of global revenue or €20M
- **CFAA** (US) — Computer Fraud and Abuse Act
- **CMA** (UK) — Computer Misuse Act

> **Tricky Q:** Is India a signatory to the Budapest Convention?
> **A:** No, India is not a signatory to the Budapest Convention on Cybercrime.

---

## 3. Introduction to Ethical Hacking

### 3.1 What is Ethical Hacking?
Testing a system for security vulnerabilities **with permission** of the owner, using the same techniques as malicious hackers, to find and fix weaknesses before attackers do.

Also called: **Penetration Testing, Pen Testing, White-Hat Hacking**

### 3.2 Key Terminology

| Term | Definition |
|------|-----------|
| **Threat** | Potential danger to a system |
| **Vulnerability** | Weakness that can be exploited |
| **Exploit** | Code/technique that takes advantage of a vulnerability |
| **Risk** | Likelihood × Impact of a threat |
| **Attack** | Actual attempt to exploit a vulnerability |
| **Payload** | Code that runs on victim after exploitation |
| **Zero-day** | Vulnerability unknown to vendor, no patch exists |
| **Proof of Concept (PoC)** | Code demonstrating a vulnerability exists |

### 3.3 Phases of Ethical Hacking (EXAM CRITICAL)

```
Phase 1: RECONNAISSANCE (Footprinting)
  → Passive: WHOIS, DNS, Google Dorks (no direct contact)
  → Active: Port scanning, banner grabbing (direct contact)

Phase 2: SCANNING & ENUMERATION
  → Port scanning (NMAP)
  → Service version detection
  → OS fingerprinting
  → Vulnerability scanning (Nessus)

Phase 3: GAINING ACCESS (Exploitation)
  → Exploit vulnerabilities
  → Metasploit, manual exploits
  → Password cracking

Phase 4: MAINTAINING ACCESS (Persistence)
  → Install backdoors, rootkits
  → Create admin accounts
  → Scheduled tasks

Phase 5: CLEARING TRACKS (Covering)
  → Delete logs
  → Modify timestamps
  → Remove malware artifacts
```

> **Tricky Q:** What is the correct order of ethical hacking phases?
> **A:** Reconnaissance → Scanning/Enumeration → Gaining Access → Maintaining Access → Clearing Tracks

---

---

# SESSION 9: Hacker Types, Teams & Skills

## 1. Types of Hackers

### 1.1 By Ethics/Intent

| Type | Hat Color | Description |
|------|-----------|-------------|
| **White Hat** | White | Ethical hackers, authorized testing |
| **Black Hat** | Black | Malicious hackers, illegal activities |
| **Grey Hat** | Grey | No permission but no malicious intent, may report after |
| **Script Kiddie** | — | Uses existing tools without understanding them |
| **Hacktivist** | — | Hacking for political/social cause (Anonymous) |
| **Cyber Criminal** | — | Financially motivated |
| **State-sponsored** | — | Nation-state backed APT groups |
| **Insider** | — | Employee/contractor with internal access |

> **Tricky Q:** What is a Script Kiddie?
> **A:** Someone who uses pre-made hacking tools without understanding the underlying techniques. Low skill, opportunistic.

> **Tricky Q:** What is a Hacktivist? Give an example.
> **A:** A hacker motivated by political/social causes. Example: Anonymous group defacing government websites in protest.

### 1.2 Red Team, Blue Team, Purple Team

| Team | Color | Role |
|------|-------|------|
| **Red Team** | 🔴 Red | Offensive — simulates attackers, finds vulnerabilities |
| **Blue Team** | 🔵 Blue | Defensive — monitors, detects, responds to attacks |
| **Purple Team** | 🟣 Purple | Collaboration of Red + Blue for better communication |
| **White Team** | ⚪ White | Neutral — sets rules of engagement, acts as referee |
| **Yellow Team** | 🟡 Yellow | Builders — developers who integrate security into code |

> **Tricky Q:** What is a Purple Team exercise?
> **A:** Red and Blue teams work together simultaneously — Red shares attack techniques with Blue to improve detection capabilities, creating feedback loops.

### 1.3 Ethical Hackers vs Crackers

| Ethical Hacker | Cracker |
|---------------|---------|
| Has written permission | No permission |
| Reports all findings | Exploits findings |
| Works within defined scope | No boundaries |
| Goal: improve security | Goal: damage/profit |
| Legal activity | Criminal activity |

---

## 2. Security, Functionality, Ease of Use Triangle

```
        SECURITY
           /\
          /  \
         /    \
        /      \
       /________\
 FUNCTIONALITY  EASE OF USE

Relationship: Increasing one dimension REDUCES the others

Examples:
• Strong password (Security ↑) → Harder to use (Ease ↓)
• No authentication (Ease ↑) → Less secure (Security ↓)
• Feature-rich app (Functionality ↑) → More attack surface (Security ↓)
```

> **Tricky Q:** Why can't we maximize all three of Security, Functionality, and Ease of Use simultaneously?
> **A:** They are in tension. More security controls add complexity (reduces ease). More features add attack surface (reduces security). Tradeoffs must be made based on risk tolerance.

---

## 3. Goals of Attackers

| Goal | Description | CIA Impacted |
|------|-------------|-------------|
| **Reconnaissance** | Gather information | — |
| **Data theft** | Steal sensitive data | Confidentiality |
| **System disruption** | DoS, destroy data | Availability, Integrity |
| **Financial gain** | Ransomware, fraud | All three |
| **Espionage** | Corporate/government secrets | Confidentiality |
| **Reputation damage** | Defacement, leaks | — |
| **Establishing foothold** | Persistence for future attacks | — |

---

## 4. Skills Required for Ethical Hacker

### 4.1 Technical Skills
- Networking (TCP/IP, routing, protocols)
- Operating Systems (Linux, Windows, macOS)
- Programming (Python, Bash, PowerShell, C/C++)
- Web technologies (HTTP, HTML, JS, SQL)
- Cryptography
- Penetration testing methodologies

### 4.2 Certifications (Important to Know)

| Certification | Issuer | Focus |
|--------------|--------|-------|
| **CEH** (Certified Ethical Hacker) | EC-Council | Broad ethical hacking |
| **OSCP** (Offensive Security Certified Professional) | Offensive Security | Hands-on pentest |
| **CISSP** | (ISC)² | Security management |
| **CompTIA Security+** | CompTIA | Security fundamentals |
| **CISM** | ISACA | Security management |
| **CISA** | ISACA | Audit and assurance |
| **eJPT** | eLearnSecurity | Entry-level pentest |

> **Tricky Q:** Which certification requires passing a 24-hour hands-on penetration test exam?
> **A:** OSCP (Offensive Security Certified Professional)

---

## 5. Exam Questions Bank (Sessions 6–9)

**Q1:** What does the security triangle describe?
**A:** The trade-off between Security, Functionality, and Ease of Use — increasing one typically decreases the others

**Q2:** What is an APT?
**A:** Advanced Persistent Threat — long-term, stealthy attack usually by nation-state actors targeting espionage/data theft

**Q3:** Which IT Act section deals with cyber terrorism?
**A:** Section 66F — punishable with life imprisonment

**Q4:** What is the difference between a threat and a vulnerability?
**A:** Threat = potential danger. Vulnerability = weakness. Both must be present for risk to exist.

**Q5:** What color hat does an ethical hacker wear?
**A:** White hat

**Q6:** What is the principle of "separation of duties"?
**A:** No single person should control an entire critical process — prevents fraud and errors (e.g., person who approves payments ≠ person who makes them)

**Q7:** What is ISO 27001?
**A:** International standard for Information Security Management Systems (ISMS)

**Q8:** What does GDPR stand for and what are its maximum fines?
**A:** General Data Protection Regulation. Maximum fines: €20 million or 4% of global annual revenue (whichever is higher)

**Q9:** What phase of hacking involves clearing logs?
**A:** Phase 5 — Clearing Tracks / Covering Tracks

**Q10:** What is a zero-day vulnerability?
**A:** A vulnerability unknown to the software vendor for which no patch exists. Named because there are zero days to fix it before it can be exploited.

---

*Next: Session 10 → Footprinting, Reconnaissance, Scanning*
