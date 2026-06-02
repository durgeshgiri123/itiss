# Session 1: Cybersecurity Challenges in Organizations

---

## 1. What is Cybersecurity?

Cybersecurity is the practice of **protecting systems, networks, programs, and data** from digital attacks, unauthorized access, damage, or theft. It encompasses technologies, processes, and practices designed to defend computers, networks, and data from attack, damage, or unauthorized access.

**CIA Triad** – The foundation of cybersecurity:
- **Confidentiality** – Ensuring information is accessible only to authorized individuals
- **Integrity** – Ensuring data is accurate and has not been tampered with
- **Availability** – Ensuring systems and data are accessible when needed

> 🔑 **Tricky Exam Tip:** The CIA Triad is not the same as the US Intelligence Agency. In cybersecurity, it stands for Confidentiality, Integrity, and Availability. Some frameworks add **Non-repudiation** as a fourth pillar.

---

## 2. Key Cybersecurity Concepts

| Term | Definition |
|------|-----------|
| **Threat** | Any potential danger to information or systems |
| **Vulnerability** | A weakness that can be exploited |
| **Risk** | Probability of a threat exploiting a vulnerability |
| **Exploit** | A method/tool used to take advantage of a vulnerability |
| **Attack Vector** | The pathway used by an attacker to access a system |
| **Attack Surface** | Total number of attack vectors available |
| **Zero-Day** | A vulnerability unknown to the software vendor |
| **Patch** | A fix released by vendor to close a vulnerability |

---

## 3. Types of Cybersecurity Threats

### 3.1 Malware
- **Virus** – Attaches to legitimate programs; spreads when executed
- **Worm** – Self-replicating; spreads without user action
- **Trojan** – Disguises itself as legitimate software
- **Ransomware** – Encrypts data; demands ransom for decryption key
- **Spyware** – Secretly monitors and collects user data
- **Adware** – Displays unwanted advertisements; may include spyware
- **Rootkit** – Hides malicious activity; gains root/admin access
- **Keylogger** – Records keystrokes to capture passwords

> 🔑 **Tricky Exam Tip:** A **worm** does NOT need a host file unlike a **virus**. Worms are self-replicating and self-propagating.

### 3.2 Social Engineering
- **Phishing** – Mass fraudulent emails impersonating legitimate sources
- **Spear Phishing** – Targeted phishing at specific individuals/organizations
- **Whaling** – Spear phishing targeting senior executives (CEOs, CFOs)
- **Vishing** – Voice phishing via phone calls
- **Smishing** – SMS-based phishing
- **Pretexting** – Creating fabricated scenarios to extract information
- **Baiting** – Luring victims with false promises (e.g., free USB drives)
- **Tailgating/Piggybacking** – Physically following authorized personnel into restricted areas

### 3.3 Network Attacks
- **DDoS (Distributed Denial of Service)** – Flooding systems with traffic to make them unavailable
- **Man-in-the-Middle (MitM)** – Intercepting communication between two parties
- **SQL Injection** – Inserting malicious SQL code into input fields
- **Cross-Site Scripting (XSS)** – Injecting malicious scripts into web pages
- **DNS Spoofing/Poisoning** – Redirecting DNS queries to malicious sites
- **ARP Spoofing** – Sending fake ARP messages to link attacker's MAC with a legitimate IP
- **Session Hijacking** – Stealing a user's active session token

### 3.4 Insider Threats
- **Malicious Insider** – Employee intentionally causing harm
- **Negligent Insider** – Employee accidentally causing harm (e.g., clicking phishing links)
- **Compromised Insider** – Employee whose credentials have been stolen

> 🔑 **Tricky Exam Tip:** Insider threats are considered the **most difficult to detect** because insiders have legitimate access rights. Up to **60% of cyber incidents** involve insiders.

---

## 4. Organizational Cybersecurity Challenges

### 4.1 People Challenges
- **Lack of security awareness** – Employees unaware of phishing, social engineering
- **Insider threats** – Disgruntled employees or negligent staff
- **Shadow IT** – Employees using unauthorized apps/services
- **Skill shortage** – Global shortage of trained cybersecurity professionals
- **Resistance to security policies** – Users bypassing security controls for convenience

### 4.2 Process Challenges
- **Poorly defined security policies** – Vague or outdated security guidelines
- **Inadequate incident response plans** – No clear procedure when breaches occur
- **Lack of patch management** – Delayed application of security patches
- **Weak password policies** – No enforcement of strong passwords/MFA
- **Third-party risk management** – Vendors/suppliers with weaker security postures
- **Insufficient security testing** – No regular penetration testing or vulnerability assessments

### 4.3 Technology Challenges
- **Legacy systems** – Old systems that no longer receive security updates
- **BYOD (Bring Your Own Device)** – Personal devices accessing corporate networks
- **Cloud security** – Misconfigured cloud environments
- **IoT vulnerabilities** – Internet of Things devices with weak security
- **Data sprawl** – Data stored across multiple unmanaged locations
- **Encryption gaps** – Sensitive data transmitted or stored in plain text

### 4.4 Regulatory/Compliance Challenges
- **Multiple overlapping regulations** – GDPR, HIPAA, PCI DSS all applying simultaneously
- **Constantly evolving standards** – Frameworks updated frequently
- **Cross-border data issues** – Different countries have different data protection laws
- **Audit fatigue** – Organizations overwhelmed by continuous compliance checks

---

## 5. Cost of Cybersecurity Breaches

| Impact Category | Examples |
|----------------|---------|
| **Financial** | Fines, remediation costs, ransom payments |
| **Reputational** | Loss of customer trust, brand damage |
| **Operational** | Business downtime, disrupted services |
| **Legal** | Lawsuits, regulatory penalties |
| **Strategic** | Loss of competitive advantage (IP theft) |

> 🔑 **Tricky Exam Tip:** IBM's Cost of a Data Breach Report regularly shows the **average cost of a data breach exceeds $4 million USD**. Financial services and healthcare are the most expensive sectors.

---

## 6. Cybersecurity Domains (Based on CISSP)

1. Security and Risk Management
2. Asset Security
3. Security Architecture and Engineering
4. Communication and Network Security
5. Identity and Access Management (IAM)
6. Security Assessment and Testing
7. Security Operations
8. Software Development Security

---

## 7. Defense in Depth

A layered security approach — multiple controls so that if one fails, others still provide protection.

**Layers (outer to inner):**
```
Physical Security → Network Security → Host Security → 
Application Security → Data Security → Policies & Procedures
```

> 🔑 **Tricky Exam Tip:** Defense in Depth is also called **"Layered Security"** or **"Castle Approach"** (like a castle with moat, walls, guards). No single control is relied upon.

---

## 8. Security Controls Classification

### By Type:
- **Preventive** – Stop an attack from happening (firewalls, access control)
- **Detective** – Identify when an attack has occurred (IDS, audit logs)
- **Corrective** – Minimize damage and restore systems (backups, incident response)
- **Deterrent** – Discourage attackers (warning banners, security cameras)
- **Compensating** – Alternative controls when primary cannot be implemented

### By Category:
- **Administrative/Managerial** – Policies, procedures, training
- **Technical/Logical** – Software-based (firewalls, encryption, MFA)
- **Physical** – Tangible barriers (locks, guards, CCTV)

> 🔑 **Tricky Exam Tip:** A **firewall** is a **Preventive + Technical** control. A **security camera** is a **Deterrent + Physical** control.

---

## 9. Emerging Cybersecurity Challenges

- **AI-powered attacks** – Attackers using ML to create adaptive malware
- **Deepfakes** – AI-generated fake audio/video used for fraud
- **Supply chain attacks** – Compromising software/hardware vendors (e.g., SolarWinds)
- **Quantum computing threats** – Potential to break current encryption algorithms
- **5G security** – Expanded attack surface with 5G rollout
- **Geopolitical cyber warfare** – Nation-state sponsored attacks

---

## 10. Quick Revision – Tricky MCQ Areas

| Question Type | Key Distinction |
|--------------|----------------|
| Virus vs Worm | Virus needs host file; Worm self-replicates independently |
| Phishing vs Spear Phishing | Phishing = mass; Spear = targeted |
| Threat vs Vulnerability | Threat = potential danger; Vulnerability = weakness |
| Risk vs Threat | Risk = probability × impact; Threat = possibility |
| Preventive vs Detective | Preventive stops; Detective identifies after |
| MFA vs 2FA | MFA = 2+ factors; 2FA = specifically 2 factors |

---

## 11. Common Exam Scenarios

**Scenario 1:** An employee receives an email claiming to be from IT asking for their password reset — This is **Phishing** (social engineering).

**Scenario 2:** A company's website slows to a crawl due to massive traffic from multiple IPs — This is a **DDoS Attack**.

**Scenario 3:** An attacker uses a USB drive labeled "Salary_2024" in a parking lot — This is **Baiting**.

**Scenario 4:** A disgruntled employee copies client data before resignation — This is a **Malicious Insider Threat**.

**Scenario 5:** Attacker intercepts communication between a client and bank — This is **Man-in-the-Middle (MitM)**.

---

*End of Session 1 — Proceed to Session 2: Compliance and Regulations Basics*
