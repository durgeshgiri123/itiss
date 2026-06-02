# Session 7: Health Insurance Portability and Accountability Act (HIPAA)

---

## 1. What is HIPAA?

**HIPAA** = Health Insurance Portability and Accountability Act  
- **Enacted:** 1996 by the US Congress  
- **Enforced by:** **HHS (Department of Health and Human Services)** via its **Office for Civil Rights (OCR)**  
- **Purpose:** Protect the privacy and security of individuals' **health information** while allowing the flow of information needed for quality healthcare

> 🔑 **Tricky Exam Tip:** HIPAA is a **US federal law** — it applies to US healthcare entities. It does NOT apply globally like GDPR. However, non-US companies handling US patient data may need to comply contractually.

---

## 2. Key HIPAA Definitions

| Term | Definition |
|------|-----------|
| **PHI** (Protected Health Information) | Individually identifiable health information held/transmitted by covered entities |
| **ePHI** | PHI stored or transmitted in **electronic** form |
| **Covered Entity (CE)** | Healthcare providers, health plans, healthcare clearinghouses |
| **Business Associate (BA)** | Third-party that creates, receives, maintains, or transmits PHI on behalf of CE |
| **Minimum Necessary** | Only access/use/disclose the minimum PHI needed for the purpose |
| **De-identification** | Removing identifying info so PHI can be used without HIPAA restrictions |

> 🔑 **Tricky Exam Tip:**  
> - A **hospital** = Covered Entity  
> - A **cloud provider storing patient records** = Business Associate  
> - A **billing company** used by the hospital = Business Associate  
> - Business Associates must sign a **BAA (Business Associate Agreement)**

---

## 3. HIPAA Rules Overview

HIPAA has **5 Rules** but 3 are most critical for cybersecurity exams:

| Rule | Focus |
|------|-------|
| **Privacy Rule** | Rights of individuals over their PHI; when PHI can be used/disclosed |
| **Security Rule** | Safeguards for **ePHI** specifically |
| **Breach Notification Rule** | Notifying individuals, HHS, and media after a breach |
| **Enforcement Rule** | Penalties and enforcement procedures |
| **Omnibus Rule (2013)** | Extended HIPAA to Business Associates; updated penalties |

---

## 4. HIPAA Privacy Rule

- Applies to **all PHI** (paper, oral, electronic)
- Gives patients rights over their health information
- Defines when PHI can be used/disclosed

### Patient Rights Under Privacy Rule:
- **Right to access** their PHI
- **Right to amend** inaccurate records
- **Right to accounting of disclosures**
- **Right to restrict** certain disclosures
- **Right to receive confidential communications**
- **Right to file a complaint**

### Permitted Disclosures (without authorization):
- **Treatment, Payment, Healthcare Operations (TPO)** — The three big permitted uses
- Public health activities
- Law enforcement (limited)
- Serious threat to health/safety

> 🔑 **Tricky Exam Tip:** **TPO** = Treatment, Payment, Operations — These are the primary permitted uses that do NOT require explicit patient authorization. Exams love this acronym.

---

## 5. HIPAA Security Rule

Applies **only to ePHI**. Requires covered entities and business associates to implement safeguards in 3 categories:

### 5.1 Administrative Safeguards
- **Security Management Process** – Risk analysis + risk management
- **Assigned Security Responsibility** – Designate a security official
- **Workforce Training and Management**
- **Information Access Management**
- **Contingency Plan** – Backup, disaster recovery, emergency access

### 5.2 Physical Safeguards
- **Facility Access Controls**
- **Workstation Use and Security**
- **Device and Media Controls** – Disposal, re-use, accountability

### 5.3 Technical Safeguards
- **Access Control** – Unique user IDs, emergency access, automatic logoff, encryption
- **Audit Controls** – Hardware/software/procedural mechanisms to record activity
- **Integrity Controls** – Ensure ePHI is not improperly altered or destroyed
- **Transmission Security** – Protect ePHI during transmission (encryption)

> 🔑 **Tricky Exam Tip:** The Security Rule classifies requirements as:
> - **Required** – Must be implemented
> - **Addressable** – Must be implemented IF reasonable and appropriate; if not, must document why and implement equivalent
> **"Addressable" does NOT mean optional!**

---

## 6. HIPAA Breach Notification Rule

### What is a Breach?
An impermissible use/disclosure of PHI that **compromises security or privacy** unless there is a **low probability** the PHI was compromised (based on 4-factor risk assessment).

### Breach Notification Timelines:

| Notification To | Timeline |
|----------------|---------|
| **Affected Individuals** | Within **60 days** of discovering the breach |
| **HHS (Secretary)** | Within **60 days** if ≥500 individuals; **annually** if <500 individuals |
| **Media (prominent news)** | Within **60 days** if ≥500 individuals in a state/jurisdiction |

> 🔑 **Tricky Exam Tip:** HIPAA breach notification = **60 days** (unlike GDPR's 72 hours). Know this distinction — it's a very common exam trap!

### 4-Factor Risk Assessment (to determine if notification is required):
1. Nature and extent of PHI involved
2. Who used/disclosed or to whom it was disclosed
3. Whether PHI was actually acquired or viewed
4. Extent to which risk has been mitigated

---

## 7. HIPAA De-identification Methods

PHI can be de-identified using **two methods**:

### Method 1: Expert Determination
A qualified statistical expert certifies that the risk of identifying the individual is very small.

### Method 2: Safe Harbor
Remove **18 specific identifiers**:
1. Names
2. Geographic data (smaller than state)
3. Dates (except year) for individuals < 90 years
4. Phone numbers
5. Fax numbers
6. Email addresses
7. Social security numbers
8. Medical record numbers
9. Health plan beneficiary numbers
10. Account numbers
11. Certificate/license numbers
12. Vehicle identifiers
13. Device identifiers
14. Web URLs
15. IP addresses
16. Biometric identifiers
17. Full-face photographs
18. Any other unique identifying number or code

> 🔑 **Tricky Exam Tip:** Under Safe Harbor, **ALL 18 identifiers** must be removed. Removing only some does not qualify as de-identification. Also, the entity must have **no actual knowledge** the remaining data could identify someone.

---

## 8. HIPAA Penalties

### Civil Penalties (4 Tiers based on culpability):

| Tier | Description | Per Violation | Annual Cap |
|------|-------------|--------------|-----------|
| **Tier 1** | Did not know | $100–$50,000 | $25,000 |
| **Tier 2** | Reasonable cause | $1,000–$50,000 | $100,000 |
| **Tier 3** | Willful neglect (corrected) | $10,000–$50,000 | $250,000 |
| **Tier 4** | Willful neglect (not corrected) | $50,000 | **$1.9 million** |

### Criminal Penalties:
- **Tier 1** – Knowingly obtaining PHI: up to **1 year** in prison
- **Tier 2** – False pretenses: up to **5 years** in prison
- **Tier 3** – Selling PHI for personal gain: up to **10 years** in prison

> 🔑 **Tricky Exam Tip:** Maximum annual civil penalty = **$1.9 million** per violation category. Criminal penalties can include **imprisonment**.

---

## 9. Business Associate Agreement (BAA)

- **Required** before a Business Associate can access PHI
- Defines permissible uses and disclosures of PHI
- Requires BA to implement appropriate safeguards
- Requires BA to report breaches to CE
- Requires BA to ensure sub-contractors (subprocessors) also comply

> 🔑 **Tricky Exam Tip:** If a covered entity doesn't have a BAA in place with a vendor who accesses PHI, **both the CE and BA can be held liable**. No BAA = HIPAA violation by itself.

---

## 10. HITECH Act (2009)

**HITECH** = Health Information Technology for Economic and Clinical Health Act  
- Strengthened HIPAA's enforcement
- Extended HIPAA obligations **directly to Business Associates**
- Increased penalties
- Introduced **Meaningful Use** program for EHR adoption
- Created breach notification requirements (later codified in Omnibus Rule)

> 🔑 **Tricky Exam Tip:** Before HITECH, Business Associates were only indirectly liable through CEs. **HITECH made BAs directly liable** for HIPAA compliance.

---

## 11. HIPAA vs GDPR – Key Differences

| Feature | HIPAA | GDPR |
|---------|-------|------|
| Jurisdiction | USA | EU (+ global reach) |
| Data covered | PHI (health data) | All personal data |
| Breach notification | 60 days | 72 hours |
| Applies to | Covered entities + BAs | Controllers + Processors |
| Individual rights | Limited | Extensive |
| Enforcement | HHS OCR | Supervisory Authorities |

---

## 12. Quick Tricky MCQ Summary

| Question | Answer |
|---------|--------|
| HIPAA enforced by | HHS Office for Civil Rights (OCR) |
| HIPAA applies to | Covered entities and Business Associates |
| Security Rule applies to | ePHI only |
| Privacy Rule applies to | All PHI (paper, oral, electronic) |
| Breach notification to individuals | Within 60 days |
| Breach notification to media | ≥500 individuals in a state |
| "Addressable" = optional? | NO – must implement or document why not |
| Safe Harbor identifiers to remove | 18 identifiers |
| HITECH made BAs directly liable? | YES |
| Maximum annual civil penalty | $1.9 million |
| TPO permitted without authorization | Treatment, Payment, Operations |
| Criminal penalty for selling PHI | Up to 10 years imprisonment |

---

*End of Session 7 — Proceed to Session 8: PCI DSS*
