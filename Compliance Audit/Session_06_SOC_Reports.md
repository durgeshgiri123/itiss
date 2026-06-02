# Session 6: SOC Reports & Auditor Process Overview

---

## 1. What are SOC Reports?

**SOC** = System and Organization Controls (formerly "Service Organization Controls")  
SOC reports are **independent third-party audit reports** that evaluate the controls of a service organization. They are issued by **CPAs (Certified Public Accountants)** under the **AICPA (American Institute of Certified Public Accountants)** framework.

> 🔑 **Tricky Exam Tip:** SOC reports are issued by **auditors/CPAs**, NOT by the organization itself. The standard governing SOC audits is **SSAE 18** (Statement on Standards for Attestation Engagements No. 18) — the successor to SSAE 16 and SAS 70.

---

## 2. Types of SOC Reports

### SOC 1 – Financial Reporting Controls

| Feature | Details |
|---------|---------|
| **Purpose** | Controls relevant to **user entities' financial reporting** |
| **Who uses it** | Financial auditors, CFOs |
| **Standard** | AT-C Section 320 |
| **Audience** | **Restricted** – Only management and auditors of the service org and user entities |

### SOC 2 – Security, Availability, Processing Integrity, Confidentiality, and Privacy

| Feature | Details |
|---------|---------|
| **Purpose** | Controls related to the **5 Trust Service Criteria (TSC)** |
| **Who uses it** | Customers, prospects, business partners evaluating security posture |
| **Standard** | AT-C Section 205 |
| **Audience** | **Restricted** – Only specified parties with NDA |

### SOC 3 – General Use Report

| Feature | Details |
|---------|---------|
| **Purpose** | Summary of SOC 2 findings for **public disclosure** |
| **Who uses it** | General public, marketing |
| **Standard** | AT-C Section 205 |
| **Audience** | **Unrestricted** – Can be publicly distributed |

> 🔑 **Tricky Exam Tip:**
> - **SOC 1** = Financial controls (ICFR – Internal Controls over Financial Reporting)
> - **SOC 2** = Security/trust controls (most common in IT/cloud)
> - **SOC 3** = Public-facing summary of SOC 2
> - SOC 3 is essentially a **seal/certificate** version of SOC 2 for marketing use

---

## 3. SOC 2 – Trust Service Criteria (TSC)

Five criteria (previously called "Trust Service Principles"):

| Criteria | Description |
|----------|-------------|
| **Security** (CC) | Protection against unauthorized access (ALWAYS required) |
| **Availability** | System available as committed/agreed |
| **Processing Integrity** | Processing is complete, valid, accurate, timely, authorized |
| **Confidentiality** | Information designated as confidential is protected |
| **Privacy** | Collection, use, retention, disclosure of personal information |

> 🔑 **Tricky Exam Tip:** **Security is the only mandatory TSC** for SOC 2. The other four are optional, chosen based on customer requirements and the nature of the service.

---

## 4. SOC Report Types – Type I vs Type II

| | Type I | Type II |
|-|--------|---------|
| **What it tests** | Design/existence of controls at a **specific point in time** | Operating effectiveness over a **period of time** |
| **Minimum period** | N/A (single date) | **Minimum 6 months** |
| **More valuable** | No | **YES** |
| **Typical duration** | 1 day audit | 6–12 months |

> 🔑 **Tricky Exam Tip:** A **Type I** report says "controls are designed properly." A **Type II** report says "controls are operating effectively over time." **Customers prefer Type II** because it provides stronger assurance.

---

## 5. SOC for Cybersecurity

A newer report type:
- Focuses on the organization's **enterprise-wide cybersecurity risk management program**
- Not limited to service organizations
- Any organization can get this report
- Based on the **AICPA Description Criteria and Control Criteria**

---

## 6. SOC Report Structure

A typical SOC 2 Type II report contains:

1. **Independent Service Auditor's Report** – Auditor's opinion
2. **Management's Assertion** – Management statement on system description
3. **System Description** – Details of the service organization's system
4. **Description of Tests and Results** – What was tested, when, and results
5. **Other Information** (optional)

### Auditor's Opinion Types:
- **Unqualified (Clean) Opinion** – No exceptions found; controls are effective
- **Qualified Opinion** – Exceptions found but limited in scope
- **Adverse Opinion** – Material weaknesses found; controls ineffective
- **Disclaimer of Opinion** – Auditor unable to form an opinion (scope limitation)

> 🔑 **Tricky Exam Tip:** An **Unqualified Opinion** = GOOD (clean bill of health). An **Adverse Opinion** = BAD. Many organizations misread "Qualified" as good — it actually means exceptions were found.

---

## 7. Auditor Process for SOC Reports

### Step-by-Step SOC 2 Audit Process:

```
Phase 1: PLANNING
├── Define scope (which TSCs apply)
├── Understand the system
├── Identify risks
└── Develop audit plan

Phase 2: READINESS ASSESSMENT (optional but recommended)
├── Gap analysis
└── Identify remediation needed

Phase 3: FIELDWORK / TESTING
├── Request evidence (logs, screenshots, config files)
├── Interview personnel
├── Observe controls
└── Test control effectiveness

Phase 4: REPORTING
├── Compile findings
├── Management reviews draft
├── Resolve exceptions (if any)
└── Issue final SOC report

Phase 5: FOLLOW-UP
└── Address exceptions; remediation plan
```

### Types of Testing Performed:
- **Inquiry** – Asking questions (lowest assurance)
- **Observation** – Watching a process being performed
- **Inspection (Examination)** – Reviewing documents, logs, records
- **Re-performance** – Auditor independently performs the control

> 🔑 **Tricky Exam Tip:** **Re-performance provides the highest level of assurance**. Inquiry alone is the weakest form of evidence. Auditors use a combination of all four.

---

## 8. Complementary User Entity Controls (CUECs)

CUECs are controls that **the customer (user entity)** must implement for the service organization's controls to be effective.

Example: A cloud provider may state that customers are responsible for managing their own user access credentials.

> 🔑 **Tricky Exam Tip:** CUECs are **very important** in SOC 2 reports. If a breach occurs due to a CUEC not being followed, the **customer** (user entity) bears responsibility, not the service organization.

---

## 9. SOC 1 vs SOC 2 vs SOC 3 – Comparison Table

| Feature | SOC 1 | SOC 2 | SOC 3 |
|---------|-------|-------|-------|
| Focus | Financial reporting controls | Security/trust criteria | Same as SOC 2 |
| Standard | SSAE 18 AT-C 320 | SSAE 18 AT-C 205 | SSAE 18 AT-C 205 |
| Distribution | Restricted | Restricted | Public |
| Commonly requested by | Financial auditors | IT customers/prospects | General public |
| Type I & Type II | Yes | Yes | No (no Type I/II) |

---

## 10. SSAE 18 – The Governing Standard

- **SSAE 18** (Statements on Standards for Attestation Engagements No. 18) governs SOC audits
- Replaced **SSAE 16** in 2017, which replaced **SAS 70**

> 🔑 **Tricky Exam Tip:** **SAS 70 → SSAE 16 → SSAE 18**. Know the evolution. Many older documents still reference SAS 70 or SSAE 16 for historical context.

---

## 11. Quick Tricky MCQ Summary

| Question | Answer |
|---------|--------|
| SOC reports issued by whom? | CPA / Auditor |
| SOC 2 mandatory criteria | Security (only) |
| SOC 3 audience | Unrestricted / Public |
| Type I vs Type II | Type I = design; Type II = effectiveness over time |
| Type II minimum period | 6 months |
| Clean opinion = ? | Unqualified Opinion |
| Bad opinion = ? | Adverse Opinion |
| Governing standard for SOC | SSAE 18 |
| Predecessor to SSAE 18 | SSAE 16 (and before that, SAS 70) |
| Highest assurance test method | Re-performance |
| What is CUEC? | Customer controls required to complement service org controls |
| SOC for Cybersecurity | For enterprise-wide cybersecurity programs; any organization |

---

*End of Session 6 — Proceed to Session 7: HIPAA*
