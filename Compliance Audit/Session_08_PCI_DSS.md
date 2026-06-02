# Session 8: Payment Card Industry Data Security Standard (PCI DSS)

---

## 1. What is PCI DSS?

**PCI DSS** = Payment Card Industry Data Security Standard  
- **Established by:** **PCI SSC** (Payment Card Industry Security Standards Council), founded in **2006** by Visa, MasterCard, American Express, Discover, and JCB  
- **Purpose:** Protect cardholder data and reduce payment card fraud  
- **Current Version:** **PCI DSS v4.0** (released March 2022; v3.2.1 retired March 2024)  
- **Type:** Industry standard (NOT a law, but contractually required by card brands)

> 🔑 **Tricky Exam Tip:** PCI DSS is **not a law** — it's a **contractual requirement** imposed by card brands (Visa, Mastercard, etc.) on merchants and processors. Non-compliance can result in **fines from card brands** and loss of ability to process card payments.

---

## 2. Who Must Comply?

**Any organization that:**
- Stores, processes, or transmits **cardholder data (CHD)**
- Provides services that control or could impact the security of CHD

This includes: merchants, payment processors, banks, service providers, and any third party involved in card payment transactions.

---

## 3. Key PCI DSS Definitions

| Term | Definition |
|------|-----------|
| **Cardholder Data (CHD)** | At minimum: PAN (Primary Account Number) |
| **PAN** | The 16-digit card number |
| **SAD** | Sensitive Authentication Data (CVV, PIN, full magnetic stripe) |
| **CDE** | Cardholder Data Environment — systems that store/process/transmit CHD |
| **Scope** | All systems in CDE + connected systems |
| **Tokenization** | Replacing PAN with a non-sensitive token |
| **Point-to-Point Encryption (P2PE)** | Encrypting card data from point of interaction to processor |
| **Network Segmentation** | Isolating CDE from rest of network to reduce scope |

> 🔑 **Tricky Exam Tip:**  
> - **PAN** = Must always be protected  
> - **SAD** (CVV, PIN, full magnetic stripe) = **CANNOT be stored after authorization**, even if encrypted  
> - **Tokenization** and **network segmentation** can significantly **reduce PCI DSS scope**

---

## 4. PCI DSS Cardholder Data – What Can Be Stored?

| Data Element | Can Store? | Must Protect? |
|-------------|-----------|--------------|
| PAN (card number) | YES | YES (mask when displaying) |
| Cardholder Name | YES | YES |
| Service Code | YES | YES |
| Expiration Date | YES | YES |
| Full Magnetic Stripe | **NO** | N/A |
| CAV2/CVC2/CVV2 (3-digit code) | **NO** | N/A |
| PIN/PIN Block | **NO** | N/A |

> 🔑 **Tricky Exam Tip:** The **CVV2/CVC2/CID** (the 3-digit code on the back of a card) **must never be stored** — not even in encrypted form — after authorization. This is one of the most tested PCI DSS facts.

---

## 5. PCI DSS v4.0 – 12 Requirements

Organized under **6 Goals:**

### Goal 1: Build and Maintain a Secure Network and Systems
**Req 1:** Install and maintain network security controls (firewalls)  
**Req 2:** Apply secure configurations to all system components

### Goal 2: Protect Account Data
**Req 3:** Protect stored account data  
**Req 4:** Protect cardholder data with strong cryptography during transmission

### Goal 3: Maintain a Vulnerability Management Program
**Req 5:** Protect all systems and networks from malicious software (anti-malware)  
**Req 6:** Develop and maintain secure systems and software

### Goal 4: Implement Strong Access Control Measures
**Req 7:** Restrict access to system components and cardholder data by business need-to-know  
**Req 8:** Identify users and authenticate access to system components  
**Req 9:** Restrict physical access to cardholder data

### Goal 5: Regularly Monitor and Test Networks
**Req 10:** Log and monitor all access to system components and cardholder data  
**Req 11:** Test security of systems and networks regularly

### Goal 6: Maintain an Information Security Policy
**Req 12:** Support information security with organizational policies and programs

> 🔑 **Tricky Exam Tip:** Memorize the **6 Goals and 12 Requirements** structure. Exams frequently ask "Which requirement covers X?" Match the topic to the correct requirement.

---

## 6. PCI DSS Compliance Validation Levels

### Merchant Levels (based on annual transaction volume):

| Level | Criteria | Validation Method |
|-------|----------|------------------|
| **Level 1** | >6 million Visa/Mastercard transactions/year | Annual **ROC** by QSA |
| **Level 2** | 1–6 million transactions/year | Annual **SAQ** + ASV scans |
| **Level 3** | 20,000–1 million e-commerce transactions/year | Annual SAQ + ASV scans |
| **Level 4** | <20,000 e-commerce or up to 1M other | Annual SAQ + ASV scans |

### Service Provider Levels:
| Level | Criteria | Validation |
|-------|----------|-----------|
| **Level 1** | >300,000 transactions/year | Annual ROC by QSA |
| **Level 2** | ≤300,000 transactions/year | Annual SAQ |

---

## 7. Key Compliance Validation Tools

| Tool | Full Name | Purpose |
|------|-----------|---------|
| **QSA** | Qualified Security Assessor | Certified auditor who performs ROC |
| **ROC** | Report on Compliance | Full audit by QSA (Level 1 merchants) |
| **SAQ** | Self-Assessment Questionnaire | Self-assessment for lower-risk merchants |
| **ASV** | Approved Scanning Vendor | Performs quarterly external vulnerability scans |
| **AOC** | Attestation of Compliance | Summary document signed by QSA/merchant |
| **ISA** | Internal Security Assessor | Internal employee certified by PCI SSC |

> 🔑 **Tricky Exam Tip:** 
> - **Level 1 merchants MUST have a ROC** conducted by a **QSA** — they cannot self-assess
> - **ASV scans** are required **quarterly** for external network vulnerabilities
> - **Penetration testing** is required **annually** and after significant changes

---

## 8. PCI DSS Key Technical Requirements (Deep Dive)

### Requirement 3 – Protect Stored Data
- Render PAN unreadable using: **hashing, truncation, tokenization, or strong encryption**
- **Truncation**: Show only first 6 and last 4 digits of PAN (e.g., 4111 XXXXXXXX 1111)
- **Encryption keys** must be protected and managed properly

### Requirement 4 – Encryption in Transit
- Use **strong cryptography** (TLS 1.2 or higher) for transmitting CHD over open/public networks
- **SSL and early TLS** are considered insecure and must not be used

> 🔑 **Tricky Exam Tip:** PCI DSS **prohibits SSL and TLS 1.0/1.1** for cardholder data transmission. Only **TLS 1.2 or TLS 1.3** is acceptable.

### Requirement 8 – Authentication
- Unique user IDs for all users
- Passwords minimum **12 characters** (in v4.0, increased from 7)
- MFA required for **all access into CDE** (v4.0 extended this requirement)
- Account lockout after **maximum 10 failed attempts**
- Session idle timeout of at least **15 minutes**

### Requirement 10 – Logging
- Logs must be retained for **at least 12 months** with **3 months immediately available**
- Logs must include: user ID, type of event, date/time, success/failure, origination

### Requirement 11 – Testing
- **Internal vulnerability scans**: After significant changes
- **External vulnerability scans by ASV**: Quarterly
- **Penetration testing**: Annually + after significant infrastructure/application changes

---

## 9. PCI DSS v4.0 – Key Changes from v3.2.1

| Change | Details |
|--------|---------|
| **Customized Approach** | New option: organizations can design their own controls to meet objectives |
| **MFA expanded** | Required for ALL access to CDE (not just remote access) |
| **Password length** | Increased from 7 to **12 characters minimum** |
| **Targeted Risk Analysis** | Organizations must conduct risk analysis to justify some control frequencies |
| **Phishing prevention** | New requirement to implement anti-phishing controls |
| **Ecommerce/DAST** | Script integrity requirements for payment pages |

---

## 10. SAQ Types (Self-Assessment Questionnaires)

| SAQ | Who uses it |
|-----|------------|
| **SAQ A** | Card-not-present merchants; all CHD functions outsourced |
| **SAQ A-EP** | E-commerce merchants with partially outsourced CHD |
| **SAQ B** | Merchants using imprint machines or standalone dial-out terminals |
| **SAQ B-IP** | Merchants with standalone PTS-approved terminals via IP |
| **SAQ C** | Merchants with payment application systems connected to internet |
| **SAQ C-VT** | Merchants using web-based virtual payment terminals |
| **SAQ D** | All service providers and merchants not fitting above categories |
| **SAQ P2PE** | Merchants using validated P2PE solutions |

---

## 11. Shared Responsibility in Cloud

PCI DSS v4.0 emphasizes **shared responsibility** in cloud environments:
- Cloud provider responsible for infrastructure security
- Merchant/organization responsible for their application and data security
- **Shared Responsibility Matrix** must be documented

---

## 12. Quick Tricky MCQ Summary

| Question | Answer |
|---------|--------|
| PCI DSS established by | PCI Security Standards Council (PCI SSC) |
| PCI SSC founded by | Visa, Mastercard, Amex, Discover, JCB |
| Current version | PCI DSS v4.0 (2022) |
| CVV/CVC can be stored? | NO |
| Full magnetic stripe stored? | NO |
| PAN can be stored? | YES (but must be protected) |
| Level 1 merchant validation | ROC by QSA |
| ASV scans frequency | Quarterly |
| Penetration testing frequency | Annually |
| Minimum password length in v4.0 | 12 characters |
| Minimum TLS version | TLS 1.2 |
| Log retention period | 12 months (3 months immediately available) |
| Session idle timeout | 15 minutes |
| Account lockout after | 10 failed attempts |
| Network segmentation purpose | Reduce PCI DSS scope |
| Customized approach introduced in | v4.0 |

---

*End of Session 8 — Proceed to Session 9: COBIT Framework*
