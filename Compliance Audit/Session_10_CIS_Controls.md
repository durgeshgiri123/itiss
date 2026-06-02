# Session 10: Center for Internet Security (CIS) Critical Security Controls

---

## 1. What is CIS?

**CIS** = Center for Internet Security  
- A **non-profit organization** that develops best practices, tools, and guidelines to help organizations improve cybersecurity  
- Known for: **CIS Controls** (formerly "SANS Top 20") and **CIS Benchmarks** (hardening guides)

---

## 2. CIS Critical Security Controls – Overview

**CIS Controls** = A prioritized set of actions organizations should take to defend against the most common cyberattacks.

| Feature | Details |
|---------|---------|
| **Current Version** | **CIS Controls v8** (released May 2021) |
| **Previous version** | CIS Controls v7.1 (2019) |
| **Number of Controls** | **18 Controls** in v8 (was 20 in v7) |
| **Created by** | Originally SANS Institute; now maintained by CIS |
| **Original name** | SANS Top 20 / CAG (Consensus Audit Guidelines) |

> 🔑 **Tricky Exam Tip:** CIS Controls v8 reduced controls from **20 to 18** by merging some and reorganizing. Many exams still reference v7's 20 controls. Know the current version (v8 = 18 controls).

---

## 3. CIS Controls v8 – All 18 Controls

| # | Control Name | Category |
|---|-------------|---------|
| 1 | Inventory and Control of Enterprise Assets | Basic |
| 2 | Inventory and Control of Software Assets | Basic |
| 3 | Data Protection | Basic |
| 4 | Secure Configuration of Enterprise Assets and Software | Basic |
| 5 | Account Management | Basic |
| 6 | Access Control Management | Basic |
| 7 | Continuous Vulnerability Management | Foundational |
| 8 | Audit Log Management | Foundational |
| 9 | Email and Web Browser Protections | Foundational |
| 10 | Malware Defenses | Foundational |
| 11 | Data Recovery | Foundational |
| 12 | Network Infrastructure Management | Foundational |
| 13 | Network Monitoring and Defense | Foundational |
| 14 | Security Awareness and Skills Training | Foundational |
| 15 | Service Provider Management | Foundational |
| 16 | Application Software Security | Organizational |
| 17 | Incident Response Management | Organizational |
| 18 | Penetration Testing | Organizational |

---

## 4. CIS Implementation Groups (IGs)

CIS Controls v8 introduced **3 Implementation Groups** to help organizations prioritize controls based on their size and risk profile:

| Group | Target Organization | Description |
|-------|-------------------|-------------|
| **IG1** | Small/basic enterprises | **"Basic Cyber Hygiene"** — Essential safeguards for organizations with limited IT/security staff |
| **IG2** | Mid-size enterprises | Organizations with dedicated IT staff; handle sensitive data |
| **IG3** | Large/complex enterprises | Organizations with sophisticated security teams; high regulatory requirements |

> 🔑 **Tricky Exam Tip:** **IG1 is considered the minimum standard** for all organizations. IG1 safeguards represent "essential cyber hygiene." IG2 includes all IG1 controls. IG3 includes all IG1 + IG2 controls. They are **cumulative**.

---

## 5. CIS Controls v7 vs v8 – Key Differences

| Feature | v7.1 | v8 |
|---------|------|----|
| Total Controls | 20 | **18** |
| Implementation Groups | 3 (same) | 3 |
| Sub-controls | 171 | **153 Safeguards** |
| Terminology | Sub-controls | **Safeguards** |
| New in v8 | – | Added cloud/mobile focus |
| Merged controls | – | Controls 19 & 20 merged into existing |

---

## 6. Top CIS Controls Deep Dive

### Control 1: Inventory and Control of Enterprise Assets
- Know every device on your network (BYOD, IoT, cloud assets)
- **You can't protect what you don't know you have**

### Control 2: Inventory and Control of Software Assets
- Know all authorized software; block unauthorized software
- Related concept: **Allowlisting (whitelisting)** — only approved software runs

### Control 3: Data Protection
- Classify data, use encryption, manage data lifecycle
- Related: DLP (Data Loss Prevention) tools

### Control 4: Secure Configuration
- Configure systems securely; disable unnecessary services
- Related to **CIS Benchmarks** — hardening guides for OS, applications

### Control 5: Account Management
- Manage all account credentials; disable dormant accounts
- Different types: user accounts, admin accounts, service accounts

### Control 6: Access Control Management
- Enforce least privilege, MFA, role-based access control (RBAC)

### Control 7: Continuous Vulnerability Management
- Regularly scan for vulnerabilities; patch promptly
- **CVSS** (Common Vulnerability Scoring System) used to prioritize

### Control 8: Audit Log Management
- Collect and review logs; set retention policies
- Centralized logging via **SIEM** (Security Information and Event Management)

### Control 17: Incident Response Management
- Establish and maintain an IR plan; run tabletop exercises

### Control 18: Penetration Testing
- Conduct regular pen tests; test from both outside and inside

---

## 7. CIS Benchmarks

**CIS Benchmarks** = Configuration best practices (hardening guides) for over 100 technologies:
- Operating Systems (Windows, Linux, macOS)
- Cloud platforms (AWS, Azure, GCP)
- Databases (MySQL, Oracle, SQL Server)
- Web browsers (Chrome, Firefox)
- Mobile devices

**Two levels:**
- **Level 1** – Basic security; minimal performance impact
- **Level 2** – Defense-in-depth; higher security; may impact usability

> 🔑 **Tricky Exam Tip:** CIS Benchmarks are **separate from CIS Controls**. Controls = strategic framework; Benchmarks = technical hardening guides for specific technologies.

---

## 8. CIS Controls vs Other Frameworks

| Feature | CIS Controls v8 | NIST CSF | ISO 27001 |
|---------|----------------|----------|-----------|
| Focus | Specific technical actions | Risk management | ISMS management |
| Prescriptiveness | **Highly prescriptive** | Flexible | Flexible |
| # of Controls/Requirements | 18 Controls, 153 Safeguards | 6 Functions | 93 Controls |
| Certifiable? | No | No | YES |
| Best for | Tactical security teams | Strategic risk programs | Formal certification |

> 🔑 **Tricky Exam Tip:** CIS Controls are **the most prescriptive** and **action-oriented** of the major frameworks. Organizations wanting concrete "what to do next" guidance often start with CIS Controls.

---

## 9. Mapping CIS Controls to Attack Types

CIS Controls directly address the most common attack techniques seen in real-world breaches:

| Attack Type | Key CIS Controls That Defend |
|------------|----------------------------|
| Phishing | Control 9 (Email Protections), Control 14 (Security Training) |
| Unpatched vulnerabilities | Control 7 (Vulnerability Mgmt) |
| Stolen credentials | Control 5 (Account Mgmt), Control 6 (Access Control) |
| Malware/Ransomware | Control 10 (Malware Defenses), Control 11 (Data Recovery) |
| Supply chain attacks | Control 15 (Service Provider Mgmt) |
| Web application attacks | Control 16 (App Security) |
| Insider threats | Control 6 (Access Control), Control 8 (Audit Logs) |

---

## 10. CVSS – Common Vulnerability Scoring System

Closely used with Control 7 (Vulnerability Management):

| Score Range | Severity |
|------------|---------|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | **Critical** |

> 🔑 **Tricky Exam Tip:** CVSS is maintained by **FIRST (Forum of Incident Response and Security Teams)**. Current version is **CVSS v3.1** (v4.0 is being adopted). Organizations use CVSS scores to **prioritize patching**.

---

## 11. Quick Tricky MCQ Summary

| Question | Answer |
|---------|--------|
| CIS Controls current version | v8 (2021) |
| Number of controls in v8 | 18 |
| Number of controls in v7 | 20 |
| Number of safeguards in v8 | 153 |
| Implementation groups | 3 (IG1, IG2, IG3) |
| IG1 = | Essential cyber hygiene (all orgs) |
| IGs are cumulative? | YES (IG3 includes IG1+IG2) |
| CIS Benchmarks purpose | Technical hardening guides |
| CIS Benchmarks Level 1 | Basic security |
| CIS Benchmarks Level 2 | Defense-in-depth |
| CVSS critical score range | 9.0–10.0 |
| CVSS maintained by | FIRST |
| Most prescriptive framework | CIS Controls |
| Original name of CIS Controls | SANS Top 20 |

---

*End of Session 10 — Proceed to Session 11: IT Act 2000 (ITAA 2008)*
