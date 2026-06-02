# MASTER QUICK REVISION CHEAT SHEET
## Cybersecurity Compliance & Regulations — All Sessions

---

## 🔵 SESSION 1 — Cybersecurity Challenges

| Key Concept | Fact |
|------------|------|
| CIA Triad | Confidentiality, Integrity, Availability |
| Virus vs Worm | Virus needs host; Worm self-replicates independently |
| Phishing vs Spear Phishing | Mass vs Targeted |
| Whaling | Spear phishing at C-level executives |
| Ransomware | Encrypts data; demands payment |
| Rootkit | Hides malware; gains admin access |
| MitM | Intercepts communication between two parties |
| DDoS | Floods system with traffic |
| Insider threats | Hardest to detect; legitimate access |
| Defense in Depth | Layered security — multiple controls |
| Preventive Control | Stops attacks (firewall) |
| Detective Control | Identifies attacks (IDS, logs) |
| Corrective Control | Restores systems (backup) |
| Administrative Control | Policies, training |
| Technical Control | Software-based (encryption, MFA) |

---

## 🔵 SESSION 2 — Compliance Basics

| Key Concept | Fact |
|------------|------|
| Regulation vs Standard | Regulation = mandatory law; Standard = voluntary |
| Compliance ≠ Security | Compliance is minimum baseline |
| DPO | Required by GDPR for qualifying orgs |
| PII | Any info identifying an individual; includes IP addresses (GDPR) |
| Data Minimization | GDPR core principle — collect only what's necessary |
| Least Privilege | Minimum access needed |
| Separation of Duties | Divide critical tasks to prevent fraud |
| Due Care | Action taken to protect |
| Due Diligence | Assessment/research before acting |
| Gap Analysis | Current state vs required state |

---

## 🔵 SESSION 3 — NIST

| Key Concept | Fact |
|------------|------|
| NIST is | Non-regulatory US federal agency (Dept of Commerce) |
| CSF 1.x functions | IDENTIFY, PROTECT, DETECT, RESPOND, RECOVER (5) |
| CSF 2.0 new function | **GOVERN** (total 6 functions) |
| CSF Components | Core, Tiers, Profiles |
| CSF Tiers | 1=Partial, 2=Risk Informed, 3=Repeatable, 4=Adaptive |
| CSF Tiers measure | Maturity of risk management (not control maturity) |
| RMF Steps | PREPARE→CATEGORIZE→SELECT→IMPLEMENT→ASSESS→AUTHORIZE→MONITOR |
| ATO | Authorization to Operate (granted by Authorizing Official) |
| FIPS 197 | AES (Advanced Encryption Standard) |
| FIPS 140-2/3 | Cryptographic module security |
| SP 800-53 | Control catalog (20 families) |
| SP 800-53 Rev 5 new families | Supply Chain Risk (SR) + PII Processing (PT) |
| SP 800-37 | Risk Management Framework |
| SP 800-61 | Incident Response |
| SP 800-63B | Digital Identity / Password Guidelines |
| Password periodic reset | NIST no longer recommends it |
| SP 800-171 | CUI protection for non-federal orgs |

---

## 🔵 SESSION 4 — GDPR

| Key Concept | Fact |
|------------|------|
| GDPR effective | 25 May 2018 |
| GDPR principles | **7** (LPDASIA) |
| Data Principal term | Data Subject |
| Controller | Decides purpose/means of processing |
| Processor | Processes on behalf of controller |
| Consent validity | Free, specific, informed, unambiguous; NO pre-ticked boxes |
| Lawful bases | 6 (Consent, Contract, Legal Obligation, Vital Interests, Public Task, Legitimate Interests) |
| Special categories | 9 (racial origin, health, biometrics, genetics, etc.) |
| Genetic/biometric data | NEW special categories added by GDPR |
| Breach notification to authority | **72 hours** |
| Right to be Forgotten | = Right to Erasure (Article 17); NOT absolute |
| Data portability | Only when basis = consent or contract |
| DPO mandatory when | Public authority / large-scale monitoring / large-scale special category data |
| DPO personal liability? | NO — Controller is liable |
| DPIA done | BEFORE processing begins |
| Pseudonymized data = PD? | YES |
| Anonymized data = PD? | NO |
| Lower tier fine | €10M or 2% global turnover |
| Upper tier fine | **€20M or 4% global turnover** (whichever higher) |
| EU-US Privacy Shield | Invalidated 2020 (Schrems II); replaced by EU-US DPF 2023 |
| Privacy by Design | Embed privacy from the start |
| Privacy by Default | Most privacy-protective settings = default |
| UK GDPR enforced by | ICO |

---

## 🔵 SESSION 5 — ISO 27001

| Key Concept | Fact |
|------------|------|
| ISO 27001 | ISMS Requirements — certifiable (uses SHALL) |
| ISO 27002 | Best practice guidance — NOT certifiable (uses SHOULD) |
| ISO 27701 | Privacy extension to 27001 (PIMS) |
| ISO 27001:2013 controls | **114 controls, 14 domains** |
| ISO 27001:2022 controls | **93 controls, 4 themes** |
| 4 themes (2022) | Organizational, People, Physical, Technological |
| Certification validity | **3 years** |
| SoA | Statement of Applicability — most important document |
| PDCA | Plan-Do-Check-Act (ISMS cycle) |
| ISO 27005 | Information security risk management |
| Risk treatment (4Ts) | Tolerate, Treat, Transfer, Terminate |
| Risk Transfer | = Insurance / outsourcing |
| ISO 27017 | Cloud security controls |
| ISO 27018 | PII in public cloud |
| Annex SL | Common structure shared by ISO management standards |
| Surveillance audits | Annual (Years 1 and 2 after certification) |

---

## 🔵 SESSION 6 — SOC Reports

| Key Concept | Fact |
|------------|------|
| SOC issued by | CPA / External Auditor |
| Governing standard | **SSAE 18** (replaced SSAE 16, which replaced SAS 70) |
| SOC 1 | Financial reporting controls (ICFR) |
| SOC 2 | Security/trust criteria |
| SOC 3 | Public summary of SOC 2 (no Type I/II) |
| SOC 2 mandatory criteria | **Security only** |
| SOC 2 optional criteria | Availability, Processing Integrity, Confidentiality, Privacy |
| Type I | Design of controls at point in time |
| Type II | Effectiveness over time (min 6 months) |
| Better report | Type II |
| Unqualified Opinion | Clean — controls effective |
| Adverse Opinion | Bad — material weaknesses |
| Qualified Opinion | Exceptions found (limited scope) |
| Highest assurance test | Re-performance |
| Lowest assurance test | Inquiry |
| CUEC | Customer controls complementing service org controls |

---

## 🔵 SESSION 7 — HIPAA

| Key Concept | Fact |
|------------|------|
| HIPAA enforced by | HHS Office for Civil Rights (OCR) |
| PHI | Protected Health Information |
| ePHI | Electronic PHI |
| Covered Entity | Healthcare providers, health plans, clearinghouses |
| Business Associate | Third-party handling PHI for CE |
| BAA | Business Associate Agreement — mandatory |
| TPO | Treatment, Payment, Operations — permitted without consent |
| Security Rule applies to | **ePHI only** |
| Privacy Rule applies to | All PHI (paper, oral, electronic) |
| Addressable ≠ Optional | Must implement or document equivalent |
| Breach notification to individuals | **60 days** |
| Report to HHS if ≥500 | Within 60 days |
| Report to HHS if <500 | Annually |
| Media notification | ≥500 in a state; within 60 days |
| HIPAA vs GDPR breach timing | HIPAA = 60 days; GDPR = 72 hours |
| Safe Harbor identifiers | Remove all **18** identifiers |
| HITECH (2009) | Made BAs directly liable for HIPAA |
| Max annual civil penalty | **$1.9 million** |
| Willful neglect + not corrected | $50,000/violation, $1.9M cap |
| Criminal — selling PHI | Up to **10 years** imprisonment |

---

## 🔵 SESSION 8 — PCI DSS

| Key Concept | Fact |
|------------|------|
| PCI DSS established by | PCI SSC (Visa, MC, Amex, Discover, JCB) |
| PCI DSS is | Contractual requirement, NOT a law |
| Current version | **v4.0** (March 2022) |
| Goals | 6 |
| Requirements | **12** |
| CHD | At minimum = PAN (card number) |
| CVV/CVC can be stored? | **NEVER** |
| Full mag stripe stored? | **NEVER** |
| SAD | Sensitive Auth Data — NEVER store after authorization |
| PAN display | Mask — show first 6 and last 4 only |
| Level 1 merchant validation | ROC by QSA |
| QSA | Qualified Security Assessor |
| ASV scans | Quarterly (external network) |
| Penetration testing | Annually + after significant changes |
| SSL/TLS 1.0 | Prohibited for CHD transmission |
| Minimum TLS | **TLS 1.2** |
| Password minimum (v4.0) | **12 characters** |
| Log retention | 12 months (3 months immediately available) |
| Session idle timeout | 15 minutes |
| Account lockout | After 10 failed attempts |
| Network segmentation benefit | Reduces PCI DSS scope |
| Customized approach | New in v4.0 |
| MFA in v4.0 | Required for ALL access into CDE |

---

## 🔵 SESSION 9 — COBIT

| Key Concept | Fact |
|------------|------|
| COBIT by | ISACA |
| COBIT focus | IT Governance and Management |
| COBIT 5 principles | **5** |
| COBIT 2019 principles | **6** |
| COBIT 5 enablers | **7** |
| COBIT 2019 objectives | **40** (5 EDM + 35 management) |
| Governance domain | **EDM** (Evaluate, Direct, Monitor) |
| Management domains | APO, BAI, DSS, MEA |
| Governance = | Board/executive level (WHAT/WHY) |
| Management = | Operational level (HOW) |
| Capability Level 0 | Incomplete |
| Capability Level 5 | Optimizing |
| COBIT + ITIL | Complementary (governance + service operations) |
| Design Factors | 11 (make COBIT 2019 flexible and tailored) |
| ISACA certifications | CISA, CISM, CRISC |
| COBIT certifiable? | No (COBIT itself is not certifiable) |

---

## 🔵 SESSION 10 — CIS Controls

| Key Concept | Fact |
|------------|------|
| CIS Controls current version | **v8** (May 2021) |
| v8 controls | **18** |
| v7 controls | **20** |
| v8 safeguards | **153** |
| v7 sub-controls | 171 |
| Implementation Groups | 3 (IG1, IG2, IG3) — cumulative |
| IG1 | Essential cyber hygiene (all organizations) |
| IG3 | All IG1+IG2+IG3 controls |
| Original name | SANS Top 20 |
| CIS Benchmarks | Technical hardening guides (separate from controls) |
| CIS Benchmark Level 1 | Basic security |
| CIS Benchmark Level 2 | Defense in depth |
| Most prescriptive framework | CIS Controls |
| CVSS critical score | 9.0–10.0 |
| CVSS maintained by | FIRST |

---

## 🔵 SESSION 11 — IT Act 2000 / ITAA 2008

| Key Concept | Fact |
|------------|------|
| IT Act enacted | 9 June 2000 |
| ITAA 2008 in force | 27 October 2009 |
| Based on | UNCITRAL Model Law on E-Commerce |
| Administered by | MeitY |
| Section 43 | Unauthorized access — up to ₹1 crore civil |
| Section 43A | SPDI breach by body corporate — court-determined compensation |
| Section 65 | Source code tampering — 3 years |
| Section 66 | Hacking — 3 years / ₹5 lakh |
| Section 66A | Offensive messages — **STRUCK DOWN** (Shreya Singhal, 2015) |
| Section 66C | Identity theft — 3 years / ₹1 lakh |
| Section 66F | Cyber terrorism — **LIFE IMPRISONMENT** |
| Section 67B | Child pornography — 5 years / ₹10 lakh |
| Section 70 | Attacking protected systems — 10 years |
| Section 72A | Breach of data contract — 3 years / ₹5 lakh |
| Section 79 | Intermediary safe harbor |
| Section 70B | CERT-In designated as national nodal agency |
| CERT-In incident reporting | **Within 6 hours** |
| CERT-In log retention | **180 days** |
| Adjudicating Officer jurisdiction | Up to ₹5 crore |
| Above ₹5 crore | High Court |
| CCA | Regulates Certifying Authorities |
| Electronic Signature (ITAA 2008) | Replaced "Digital Signature" — broader term |

---

## 🔵 SESSION 12 — DPDP Act 2023

| Key Concept | Fact |
|------------|------|
| DPDP Act enacted | **11 August 2023** |
| Administered by | Data Protection Board of India (DPBI) |
| Appeals against DPBI | TDSAT |
| Data Principal | Individual (= GDPR's Data Subject) |
| Data Fiduciary | Organization (= GDPR's Data Controller) |
| Covers | Only **digital** personal data |
| Does NOT cover | Offline/paper data; personal/domestic use |
| Lawful bases | **2**: Consent + Legitimate Uses |
| Age of child | **18 years** |
| GDPR child age | 16 years |
| Max penalty | **₹250 crore** (security safeguard failure) |
| Children's data breach penalty | ₹200 crore |
| Data Principal duties | YES — unique feature (not in GDPR) |
| Data Principal duty violation | Up to ₹10,000 |
| Right to Nominate | Unique to DPDP Act |
| Data portability | NOT explicitly provided |
| DPO required for | Significant Data Fiduciaries only |
| DPO must be based in | India |
| Cross-border transfers | Whitelist of trusted countries |
| Consent Manager | New concept — manages consent across fiduciaries |
| Puttaswamy judgment | 2017 — Privacy as fundamental right (Article 21) |
| DPDP replaced | IT (SPDI) Rules 2011 |

---

## 🔴 SUPER TRICKY COMPARISON TABLE

| Feature | GDPR | HIPAA | PCI DSS | IT Act | DPDP 2023 |
|---------|------|-------|---------|--------|-----------|
| Jurisdiction | EU (global) | USA | Global (contractual) | India | India |
| Breach notification | **72 hours** | **60 days** | No fixed timeline | Not specified | Prescribed by DPBI |
| Max penalty | **€20M / 4% turnover** | $1.9M/year | $100K/month | ₹1 crore (civil) | **₹250 crore** |
| Child age | 16 | N/A | N/A | N/A | **18** |
| Enforcer | SA (each member state) | HHS OCR | Card brands | CCA / Courts | DPBI |
| DPO required | Yes (qualifying orgs) | No | No | No | Yes (for SDFs) |
| Individual duties | No | No | No | No | **YES** |
| Data portability | YES | No | No | No | NO |

---

## 🔴 MUST-KNOW ACRONYMS

| Acronym | Full Form |
|---------|----------|
| CIA | Confidentiality, Integrity, Availability |
| PII | Personally Identifiable Information |
| PHI | Protected Health Information |
| ePHI | Electronic Protected Health Information |
| GDPR | General Data Protection Regulation |
| HIPAA | Health Insurance Portability and Accountability Act |
| PCI DSS | Payment Card Industry Data Security Standard |
| NIST | National Institute of Standards and Technology |
| CSF | Cybersecurity Framework |
| RMF | Risk Management Framework |
| ATO | Authorization to Operate |
| ISMS | Information Security Management System |
| SoA | Statement of Applicability |
| DPIA | Data Protection Impact Assessment |
| DPO | Data Protection Officer |
| BAA | Business Associate Agreement |
| CDE | Cardholder Data Environment |
| QSA | Qualified Security Assessor |
| ROC | Report on Compliance |
| SAQ | Self-Assessment Questionnaire |
| ASV | Approved Scanning Vendor |
| ISACA | Information Systems Audit and Control Association |
| COBIT | Control Objectives for Information and Related Technologies |
| CIS | Center for Internet Security |
| CVSS | Common Vulnerability Scoring System |
| CERT-In | Computer Emergency Response Team – India |
| MeitY | Ministry of Electronics and Information Technology |
| DPDP | Digital Personal Data Protection |
| DPBI | Data Protection Board of India |
| TDSAT | Telecom Disputes Settlement and Appellate Tribunal |
| CCA | Controller of Certifying Authorities |
| SPDI | Sensitive Personal Data or Information |
| SSAE | Statements on Standards for Attestation Engagements |
| SOC | System and Organization Controls |
| TSC | Trust Service Criteria |
| CUEC | Complementary User Entity Controls |
| FIPS | Federal Information Processing Standards |
| AES | Advanced Encryption Standard |
| TLS | Transport Layer Security |
| MFA | Multi-Factor Authentication |
| RBAC | Role-Based Access Control |
| PoLP | Principle of Least Privilege |
| SoD | Separation of Duties |

---

*All 12 Sessions Covered — Best of luck for your competition exam!* 🎯
