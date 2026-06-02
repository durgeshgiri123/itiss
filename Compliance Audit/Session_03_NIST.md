# Session 3: National Institute of Standards and Technology (NIST)

---

## 1. What is NIST?

**NIST** = National Institute of Standards and Technology — a non-regulatory federal agency under the **US Department of Commerce**. It develops standards, guidelines, and best practices for cybersecurity.

> 🔑 **Tricky Exam Tip:** NIST is **not a regulatory body** — it does not enforce laws. However, US federal agencies are **required** to follow NIST guidelines. Private sector adoption is voluntary but widely recognized.

---

## 2. Key NIST Publications

| Document | Full Name | Purpose |
|----------|-----------|---------|
| **NIST SP 800-53** | Security and Privacy Controls for Federal Information Systems | Comprehensive control catalog |
| **NIST SP 800-37** | Risk Management Framework (RMF) | Risk-based approach to system authorization |
| **NIST SP 800-171** | Protecting Controlled Unclassified Information (CUI) | For non-federal organizations handling gov data |
| **NIST SP 800-30** | Guide for Conducting Risk Assessments | Risk assessment methodology |
| **NIST CSF** | Cybersecurity Framework | Voluntary framework for all sectors |
| **NIST SP 800-61** | Computer Security Incident Handling Guide | Incident response |
| **NIST SP 800-115** | Technical Guide to Information Security Testing | Penetration testing guide |
| **FIPS 140-2/140-3** | Security Requirements for Cryptographic Modules | Encryption standards |
| **FIPS 197** | Advanced Encryption Standard (AES) | AES specification |

> 🔑 **Tricky Exam Tip:** **FIPS** = Federal Information Processing Standards. **FIPS 197** established **AES**. **FIPS 140-2** is the most referenced standard for **cryptographic modules**. FIPS 140-3 is the newer version.

---

## 3. NIST Cybersecurity Framework (CSF)

Released in **2014** (CSF 1.0), updated to **CSF 1.1 in 2018**, and **CSF 2.0 in 2024**.

### 3.1 CSF Core Functions (Original 5 + 1 new in CSF 2.0)

```
GOVERN → IDENTIFY → PROTECT → DETECT → RESPOND → RECOVER
```

> 🔑 **Tricky Exam Tip:** CSF 1.x had **5 functions**: Identify, Protect, Detect, Respond, Recover. **CSF 2.0 added GOVERN** as the first/overarching function.

| Function | Key Focus | Exam Keyword |
|----------|-----------|-------------|
| **Govern** (NEW 2.0) | Organizational context, risk strategy, policies | Oversight, accountability |
| **Identify** | Asset management, risk assessment, governance | Know what you have |
| **Protect** | Access control, data security, training | Safeguards |
| **Detect** | Anomalies, monitoring, detection processes | Find threats |
| **Respond** | Response planning, communications, analysis | React to incidents |
| **Recover** | Recovery planning, improvements, communications | Restore services |

### 3.2 CSF Three Components

1. **Core** – The 6 functions + categories + subcategories
2. **Tiers** – How mature the organization's risk management is (Tier 1–4)
3. **Profiles** – Alignment of Core with business requirements (Current vs Target Profile)

### 3.3 NIST CSF Implementation Tiers

| Tier | Name | Description |
|------|------|-------------|
| Tier 1 | **Partial** | Ad-hoc, no formalized risk management |
| Tier 2 | **Risk Informed** | Risk practices exist but not organization-wide |
| Tier 3 | **Repeatable** | Formally approved, regularly updated processes |
| Tier 4 | **Adaptive** | Continuous improvement, agile risk management |

> 🔑 **Tricky Exam Tip:** Tiers describe the **MATURITY of risk management practices**, NOT the maturity of security controls. A Tier 4 organization adapts based on current threats.

---

## 4. NIST Risk Management Framework (RMF)

Defined in **NIST SP 800-37**. Provides a structured process for managing cybersecurity risk in federal systems.

### RMF 7 Steps (Memorize in Order):

```
1. PREPARE   → Establish context, resources, and roles
2. CATEGORIZE→ Classify system based on impact (FIPS 199)
3. SELECT    → Choose appropriate security controls (SP 800-53)
4. IMPLEMENT → Deploy selected controls
5. ASSESS    → Verify controls are implemented correctly
6. AUTHORIZE → AO grants Authorization to Operate (ATO)
7. MONITOR   → Continuously track controls and risks
```

> 🔑 **Tricky Exam Tip:** 
> - The **Authorizing Official (AO)** grants the **Authorization to Operate (ATO)**
> - **Prepare** was added in RMF Rev 2 (2018) — older versions had only 6 steps
> - After **MONITOR**, issues loop back to earlier steps

### FIPS 199 Impact Levels (Used in Categorize Step):
- **Low** – Limited adverse effect
- **Moderate** – Serious adverse effect  
- **High** – Severe or catastrophic adverse effect

---

## 5. NIST SP 800-53 Control Families

Major control families (20 families):

| ID | Family |
|----|--------|
| AC | Access Control |
| AT | Awareness and Training |
| AU | Audit and Accountability |
| CA | Assessment, Authorization, Monitoring |
| CM | Configuration Management |
| CP | Contingency Planning |
| IA | Identification and Authentication |
| IR | Incident Response |
| MA | Maintenance |
| MP | Media Protection |
| PE | Physical and Environmental Protection |
| PL | Planning |
| PM | Program Management |
| PS | Personnel Security |
| PT | PII Processing and Transparency |
| RA | Risk Assessment |
| SA | System and Services Acquisition |
| SC | System and Communications Protection |
| SI | System and Information Integrity |
| SR | Supply Chain Risk Management |

> 🔑 **Tricky Exam Tip:** SP 800-53 Rev 5 (2020) added **Supply Chain Risk Management (SR)** and **PII Processing (PT)** as new families.

---

## 6. NIST Password Guidelines (SP 800-63B)

Key recommendations (updated in 2020):
- **Minimum 8 characters** for user-chosen passwords; **minimum 6 for machine-generated**
- **Do NOT force periodic password resets** unless compromise is suspected
- **Do NOT require complexity rules** (uppercase, numbers, symbols) — they reduce security by leading to predictable patterns
- **Check against lists of commonly used/compromised passwords**
- Allow **copy-paste** in password fields
- Support **long passwords** (64+ characters)

> 🔑 **Tricky Exam Tip:** NIST **no longer recommends** mandatory periodic password changes and complex composition rules. This contradicts older conventional wisdom. Many exam questions test this modern guidance.

---

## 7. NIST Privacy Framework (2020)

A **voluntary framework** to help organizations manage privacy risk. Complements the Cybersecurity Framework.

**Core Functions:**
1. **Identify-P** – Inventory and manage data with privacy implications
2. **Govern-P** – Establish policies and risk strategy for privacy
3. **Control-P** – Manage data with individuals' privacy interests
4. **Communicate-P** – Engage with individuals about privacy practices
5. **Protect-P** – Implement safeguards for data with privacy implications

---

## 8. NIST vs Other Frameworks

| Framework | Creator | Mandatory? | Sector | Focus |
|-----------|---------|-----------|--------|-------|
| NIST CSF | NIST (US) | Voluntary (federal agencies required) | All sectors | Risk management |
| NIST RMF | NIST (US) | Required for US federal systems | Government | System authorization |
| ISO 27001 | ISO/IEC | Voluntary (certifiable) | All sectors | ISMS |
| COBIT | ISACA | Voluntary | IT governance | Governance & management |
| CIS Controls | CIS | Voluntary | All sectors | Practical security actions |

---

## 9. Tricky NIST Exam Questions

**Q: Which NIST document covers incident response?**  
A: **NIST SP 800-61** (Computer Security Incident Handling Guide)

**Q: Which NIST document covers penetration testing?**  
A: **NIST SP 800-115**

**Q: NIST CSF 2.0 added which new function?**  
A: **GOVERN**

**Q: What are the 3 components of NIST CSF?**  
A: **Core, Tiers, Profiles**

**Q: What does ATO stand for in NIST RMF?**  
A: **Authorization to Operate**

**Q: Which FIPS covers AES?**  
A: **FIPS 197**

**Q: Which FIPS covers cryptographic modules?**  
A: **FIPS 140-2 / 140-3**

**Q: NIST SP 800-53 Rev 5 added which new control families?**  
A: **Supply Chain Risk Management (SR)** and **PII Processing (PT)**

**Q: NIST no longer recommends which password practice?**  
A: **Mandatory periodic password changes**

**Q: What is the difference between Tiers and Profiles in NIST CSF?**  
A: **Tiers = maturity of risk management; Profiles = alignment of framework to business needs**

---

## 10. Quick Recall Summary

```
NIST CSF 2.0 Functions:  GOVERN → IDENTIFY → PROTECT → DETECT → RESPOND → RECOVER
NIST RMF Steps:          PREPARE → CATEGORIZE → SELECT → IMPLEMENT → ASSESS → AUTHORIZE → MONITOR
CSF Tiers:               Partial (1) → Risk Informed (2) → Repeatable (3) → Adaptive (4)
FIPS 197 = AES
FIPS 140-2/3 = Cryptographic Module Security
SP 800-53 = Control Catalog (20 families)
SP 800-37 = Risk Management Framework
SP 800-61 = Incident Handling
SP 800-171 = CUI protection for non-federal
SP 800-63B = Digital Identity / Password Guidelines
```

---

*End of Session 3 — Proceed to Session 4: GDPR*
