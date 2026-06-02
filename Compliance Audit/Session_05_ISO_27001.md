# Session 5: ISO/IEC 2700x Series – Information Security Standards

---

## 1. What is ISO?

**ISO** = International Organization for Standardization  
**IEC** = International Electrotechnical Commission  
Together, they publish the **ISO/IEC 27000 series** — a family of standards for **Information Security Management Systems (ISMS)**.

> 🔑 **Tricky Exam Tip:** ISO standards are **voluntary** — no law requires them globally. However, ISO 27001 certification is widely required by clients, governments, and as part of tenders and contracts.

---

## 2. The ISO 2700x Family — Overview

| Standard | Title | Key Purpose |
|----------|-------|------------|
| **ISO 27000** | Overview and vocabulary | Definitions and terminology |
| **ISO 27001** | ISMS Requirements | Certifiable ISMS standard (SHALL requirements) |
| **ISO 27002** | Code of Practice | Best practice guidance for implementing controls |
| **ISO 27003** | ISMS Implementation Guidance | How to implement 27001 |
| **ISO 27004** | Monitoring, measurement, analysis | Measuring ISMS performance |
| **ISO 27005** | Information Security Risk Management | Risk assessment/treatment |
| **ISO 27006** | Accreditation for Audit Bodies | Requirements for certification bodies |
| **ISO 27007** | ISMS Auditing Guidelines | How to audit an ISMS |
| **ISO 27017** | Cloud Security | Controls for cloud services |
| **ISO 27018** | PII in Public Clouds | Protecting PII in cloud environments |
| **ISO 27031** | ICT Readiness for Business Continuity | BC/DR |
| **ISO 27035** | Information Security Incident Management | Incident handling |
| **ISO 27701** | Privacy Information Management (PIMS) | Extension for GDPR/privacy |

> 🔑 **Tricky Exam Tip:**  
> - **ISO 27001** = **Requirements** (uses "SHALL") → Can be certified  
> - **ISO 27002** = **Guidance** (uses "SHOULD") → Cannot be certified alone  
> - **ISO 27701** = Privacy extension to 27001; helps demonstrate GDPR compliance

---

## 3. ISO 27001 – The Certifiable Standard

### 3.1 Purpose
Specifies the requirements for **establishing, implementing, maintaining, and continually improving** an ISMS. Organizations can be **certified** by an accredited third party.

### 3.2 Structure (High-Level Structure – Annex SL / Harmonized Structure)

ISO 27001:2022 follows the **Annex SL** / **ISO/IEC HS** (Harmonized Structure) format, common across ISO management standards (ISO 9001, ISO 14001, etc.):

```
Clause 1:  Scope
Clause 2:  Normative References
Clause 3:  Terms and Definitions
Clause 4:  Context of the Organization
Clause 5:  Leadership
Clause 6:  Planning
Clause 7:  Support
Clause 8:  Operation
Clause 9:  Performance Evaluation
Clause 10: Improvement
```

> 🔑 **Tricky Exam Tip:** Clauses **4–10** are the **normative/mandatory** clauses. Clauses 1–3 are introductory. Annex A contains the **controls** (but implementing them is guided by risk assessment, not mandatory to implement all of them).

---

## 4. ISO 27001 Annex A (Controls)

### ISO 27001:2013 vs 2022

| Version | Number of Controls | Number of Domains |
|---------|-------------------|------------------|
| ISO 27001:**2013** | **114 controls** | **14 domains** |
| ISO 27001:**2022** | **93 controls** | **4 themes** |

> 🔑 **Tricky Exam Tip:** The **2022 update** reorganized controls into **4 themes** (not 14 domains). Many exams may still reference the 2013 version. Know both!

### ISO 27001:2022 – 4 Themes (Annex A)

| Theme | Control Count |
|-------|--------------|
| **5 – Organizational** | 37 controls |
| **6 – People** | 8 controls |
| **7 – Physical** | 14 controls |
| **8 – Technological** | 34 controls |

### ISO 27001:2013 – 14 Domains (Annex A)

| Domain | ID |
|--------|-----|
| Information Security Policies | A.5 |
| Organization of Information Security | A.6 |
| Human Resource Security | A.7 |
| Asset Management | A.8 |
| Access Control | A.9 |
| Cryptography | A.10 |
| Physical and Environmental Security | A.11 |
| Operations Security | A.12 |
| Communications Security | A.13 |
| System Acquisition, Development, Maintenance | A.14 |
| Supplier Relationships | A.15 |
| Information Security Incident Management | A.16 |
| Business Continuity Management | A.17 |
| Compliance | A.18 |

---

## 5. ISO 27001 Certification Process

```
1. Gap Analysis         → Compare current state to ISO 27001 requirements
2. Risk Assessment      → Identify and evaluate information security risks (per ISO 27005)
3. Statement of Applicability (SoA) → Document which Annex A controls apply (and why)
4. Implement Controls   → Apply chosen controls + policies/procedures
5. Internal Audit       → Check readiness before external audit
6. Management Review    → Top management reviews ISMS effectiveness
7. Stage 1 Audit (Documentation) → External auditor reviews documentation
8. Stage 2 Audit (Certification) → External auditor verifies implementation
9. Certification Issued → Valid for 3 years
10. Surveillance Audits → Annual checks (Years 1 and 2)
11. Recertification     → Full audit every 3 years
```

> 🔑 **Tricky Exam Tip:** The **Statement of Applicability (SoA)** is one of the most important documents in ISO 27001 — it justifies which controls from Annex A are included or excluded and why.

---

## 6. ISMS – Information Security Management System

An **ISMS** is a systematic approach to managing sensitive company information through:
- Policies
- Procedures
- Controls
- Risk management processes

**PDCA Cycle (Plan-Do-Check-Act)** — used in ISMS:

```
PLAN  → Establish ISMS scope, policy, objectives, risk assessment
DO    → Implement controls and processes
CHECK → Monitor, measure, audit performance
ACT   → Take corrective/improvement actions
```

---

## 7. ISO 27002:2022 – Updated Control Attributes

ISO 27002:2022 introduced **5 attributes** to categorize controls:

| Attribute | Options |
|-----------|---------|
| **Control type** | Preventive, Detective, Corrective |
| **Information security properties** | Confidentiality, Integrity, Availability |
| **Cybersecurity concepts** | Identify, Protect, Detect, Respond, Recover (aligns with NIST CSF!) |
| **Operational capabilities** | Governance, Asset management, etc. |
| **Security domains** | Governance & ecosystem, Protection, Defence, Resilience |

---

## 8. ISO 27005 – Risk Management

**Risk management process:**

```
1. Context Establishment
2. Risk Identification
3. Risk Analysis (Qualitative / Quantitative)
4. Risk Evaluation
5. Risk Treatment
6. Risk Acceptance
7. Risk Communication & Consultation
8. Risk Monitoring & Review
```

### Risk Treatment Options (4 T's):
- **Tolerate (Accept)** – Risk is within acceptable levels
- **Treat (Mitigate)** – Apply controls to reduce risk
- **Transfer (Share)** – Cyber insurance, outsource
- **Terminate (Avoid)** – Stop the activity causing the risk

> 🔑 **Tricky Exam Tip:** ISO 27005 uses **"Terminate"** where others may say "Avoid". Risk **Transfer** includes cyber insurance. Risk **Tolerance** (Accept) is chosen when cost of control > potential loss.

---

## 9. ISO 27017 and 27018 – Cloud Security

**ISO 27017** – Security controls for **cloud service providers and cloud service customers**  
**ISO 27018** – Protection of **PII in public cloud** environments; aligns with GDPR

> 🔑 **Tricky Exam Tip:** ISO 27017 extends ISO 27002 with **7 additional cloud-specific controls**. ISO 27018 is specifically about **personal data in the cloud**.

---

## 10. ISO 27701 – Privacy Information Management

- Extension to ISO 27001/27002 for **privacy**
- Creates a **PIMS (Privacy Information Management System)**
- Helps organizations demonstrate compliance with **GDPR** and other privacy laws
- Applies to both **controllers and processors**

---

## 11. Quick Tricky MCQ Summary

| Question | Answer |
|---------|--------|
| ISO 27001 can be certified? | YES |
| ISO 27002 can be certified? | NO |
| Number of controls in ISO 27001:2013 | 114 |
| Number of controls in ISO 27001:2022 | 93 |
| Number of themes in ISO 27001:2022 | 4 |
| Number of domains in ISO 27001:2013 | 14 |
| Certification validity period | 3 years |
| Most important document in ISO 27001 | Statement of Applicability (SoA) |
| ISO standard for risk management | ISO 27005 |
| ISO standard for cloud PII | ISO 27018 |
| ISO standard for privacy management | ISO 27701 |
| PDCA stands for? | Plan-Do-Check-Act |
| Risk treatment option = Insurance | Transfer |
| Risk treatment option = Stop activity | Terminate/Avoid |
| ISO 27001 uses "SHALL" or "SHOULD"? | SHALL (mandatory) |

---

*End of Session 5 — Proceed to Session 6: SOC Reports*
