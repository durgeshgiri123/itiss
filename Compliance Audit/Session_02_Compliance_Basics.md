# Session 2: Compliance and Regulations for Cybersecurity – Basics

---

## 1. What is Compliance?

**Compliance** means adhering to laws, regulations, standards, and policies that govern how an organization protects data and operates its information systems.

> 🔑 **Tricky Exam Tip:** **Compliance ≠ Security**. An organization can be compliant but not secure, or secure but not compliant. Compliance is the **minimum baseline**; security goes beyond it.

---

## 2. Why Cybersecurity Compliance Matters

- **Legal obligations** – Failure leads to fines, penalties, lawsuits
- **Customer trust** – Demonstrates commitment to protecting personal data
- **Risk reduction** – Structured controls reduce likelihood and impact of breaches
- **Competitive advantage** – Certifications signal trustworthiness (e.g., ISO 27001)
- **Incident prevention** – Frameworks guide proactive security measures

---

## 3. Core Compliance Terminology

| Term | Definition |
|------|-----------|
| **Regulation** | Legally binding rules enforced by government (GDPR, HIPAA) |
| **Standard** | Voluntary or industry-adopted best practices (ISO 27001, PCI DSS) |
| **Framework** | A structured set of guidelines (NIST CSF, COBIT) |
| **Policy** | Internal rules set by an organization |
| **Control** | A measure taken to mitigate risk |
| **Audit** | Formal review to assess compliance with standards |
| **Gap Analysis** | Comparing current state to desired state |
| **Attestation** | Formal declaration that requirements are met |
| **Remediation** | Actions taken to fix identified compliance gaps |

> 🔑 **Tricky Exam Tip:** Know the difference:
> - **Regulation** = Mandatory (law)  
> - **Standard** = Often voluntary (but may be required by contracts)  
> - **Framework** = Advisory guidance  

---

## 4. Types of Compliance

### 4.1 Regulatory Compliance
Compliance with laws and government regulations. Non-compliance leads to **legal penalties**.
- Examples: GDPR (EU), HIPAA (USA), DPDP Act 2023 (India), IT Act 2000 (India)

### 4.2 Industry Standards Compliance
Compliance with standards set by industry bodies.
- Examples: PCI DSS (payment industry), ISO 27001 (information security)

### 4.3 Contractual Compliance
Compliance required by contracts with customers or partners.
- Examples: A client may require SOC 2 compliance before signing a contract

### 4.4 Internal Compliance
Adherence to internally defined policies and procedures.

---

## 5. Compliance vs Security vs Privacy

```
+------------------+---------------------------+---------------------------+
| Dimension        | Security                  | Compliance/Privacy        |
+------------------+---------------------------+---------------------------+
| Focus            | Protecting systems/data   | Meeting rules/regulations |
| Driver           | Risk management           | Legal/regulatory mandate  |
| Outcome          | Reduced breaches          | Avoid fines, legal issues |
| Approach         | Continuous improvement    | Point-in-time assessment  |
+------------------+---------------------------+---------------------------+
```

> 🔑 **Tricky Exam Tip:** **Privacy** focuses on the rights of individuals over their personal data. **Security** focuses on protecting data from unauthorized access. **Compliance** ensures both are done in accordance with rules.

---

## 6. Key Compliance Principles

### 6.1 Principle of Least Privilege (PoLP)
Users should have **only the minimum access** needed to perform their job functions.

### 6.2 Need-to-Know
Information should only be shared with those who **need it to do their job**.

### 6.3 Separation of Duties (SoD)
No single person should have control over all aspects of a critical process (prevents fraud).

> 🔑 **Tricky Exam Tip:** **Least Privilege** limits access rights; **Separation of Duties** divides tasks across people. Both prevent insider abuse.

### 6.4 Due Care vs Due Diligence
- **Due Care** – Doing what a reasonable person would do to protect assets (action taken)
- **Due Diligence** – Investigating and understanding risks before making decisions (research/assessment)

### 6.5 Data Minimization
Collect only the minimum data necessary for the stated purpose (a GDPR core principle).

---

## 7. Compliance Lifecycle

```
1. IDENTIFY → What regulations/standards apply to my organization?
2. ASSESS   → What is our current state? (Gap Analysis)
3. PLAN     → What controls need to be implemented?
4. IMPLEMENT→ Deploy controls, policies, procedures
5. MONITOR  → Continuously track compliance status
6. AUDIT    → Internal/External audit verification
7. REPORT   → Document and report compliance status
8. REMEDIATE→ Fix gaps identified in audits
```

---

## 8. Roles in Compliance

| Role | Responsibility |
|------|---------------|
| **CISO** (Chief Information Security Officer) | Owns overall security strategy |
| **DPO** (Data Protection Officer) | Required under GDPR; oversees data protection |
| **Compliance Officer** | Monitors regulatory adherence |
| **Internal Auditor** | Reviews compliance processes internally |
| **External Auditor** | Independent third-party reviewer |
| **Risk Manager** | Identifies and manages organizational risks |

> 🔑 **Tricky Exam Tip:** Under GDPR, appointing a **DPO is mandatory** for public authorities, or organizations that carry out large-scale processing of special categories of data.

---

## 9. Data Classification

Organizations must classify data to apply appropriate controls:

| Classification Level | Description | Example |
|---------------------|-------------|---------|
| **Public** | No restriction | Press releases, marketing material |
| **Internal** | For internal use only | Internal memos, org charts |
| **Confidential** | Sensitive business information | Financial reports, contracts |
| **Restricted/Secret** | Highly sensitive | PII, health records, passwords |

> 🔑 **Tricky Exam Tip:** Most compliance frameworks require data classification as a **foundational step** before applying security controls.

---

## 10. Personally Identifiable Information (PII)

**PII** = Any information that can be used to identify an individual.

**Examples:** Name, email address, phone number, SSN, IP address, biometric data, medical records.

**Sensitive PII** (requires extra protection): SSN, financial account numbers, medical data, biometric data.

> 🔑 **Tricky Exam Tip:** Under GDPR, **IP addresses** are considered personal data (PII). This surprises many — an IP address can be used to identify an individual.

---

## 11. Common Compliance Challenges

- **Regulatory fragmentation** – Different rules in different countries
- **Evolving regulations** – Laws updated faster than organizations can adapt
- **Resource constraints** – Small organizations lack compliance budgets
- **Audit fatigue** – Multiple audits (SOC 2, ISO 27001, PCI DSS) simultaneously
- **Third-party risk** – Vendors may not be compliant even if you are
- **Data localization** – Some countries require data to be stored locally

---

## 12. Penalties for Non-Compliance

| Regulation | Maximum Penalty |
|-----------|----------------|
| **GDPR** | €20 million or 4% of global annual turnover (whichever is higher) |
| **HIPAA** | Up to $1.9 million per violation category per year |
| **PCI DSS** | $5,000–$100,000 per month until compliant |
| **IT Act 2000 (India)** | Up to ₹5 crore and imprisonment |
| **DPDP Act 2023 (India)** | Up to ₹250 crore per breach |

---

## 13. Key Relationships Between Frameworks

```
NIST CSF ←──────── maps to ──────────→ ISO 27001
    ↑                                        ↑
    └──── supports compliance with ─────────┘
              GDPR | HIPAA | PCI DSS
```

> 🔑 **Tricky Exam Tip:** **Frameworks (NIST, ISO) guide HOW to achieve security**. **Regulations (GDPR, HIPAA) define WHAT must be protected**. Organizations often use frameworks to demonstrate compliance with regulations.

---

## 14. Quick Revision MCQ Summary

| Concept | Key Fact |
|---------|---------|
| Compliance vs Security | Compliance = minimum; Security = ongoing |
| DPO | Required by GDPR for qualifying organizations |
| PII under GDPR | Includes IP addresses |
| Data Minimization | GDPR core principle |
| PoLP | Minimum access necessary |
| SoD | Divides critical tasks to prevent fraud |
| Due Care | Action taken to protect |
| Due Diligence | Research/Assessment before acting |
| Gap Analysis | Compare current state vs required state |
| Attestation | Formal declaration of compliance |

---

*End of Session 2 — Proceed to Session 3: Compliance Frameworks and Industry Standards*
