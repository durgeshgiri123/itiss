# Session 1: Information Security & Security Attacks/Threats

## 1. What is Information Security?

Information Security (InfoSec) is the practice of protecting information and information systems from unauthorized access, use, disclosure, disruption, modification, or destruction. It is not just about computers — it covers people, processes, and technology.

### The CIA Triad (the foundation of everything in this course)

| Goal | Meaning | Broken by |
|---|---|---|
| **Confidentiality** | Information is accessible only to authorized parties | Eavesdropping, shoulder surfing, data theft |
| **Integrity** | Information is not altered in unauthorized ways | Tampering, man-in-the-middle modification |
| **Availability** | Information/systems are accessible when needed | DoS/DDoS attacks, ransomware |

Extended models add:
- **Authentication** — proving identity (you are who you claim to be)
- **Non-repudiation** — a sender cannot deny having sent a message (this is why digital signatures matter later in the course)
- **Access Control** — only authorized entities can use a resource

> **Exam tip:** "AAA" framework = Authentication, Authorization, Accounting — often confused with CIA. CIA is about the *data*; AAA is about *controlling access* to systems.

## 2. Security Attacks — Classification

Security attacks are broadly divided into two categories based on Stallings' model:

### A. Passive Attacks
The attacker only **observes/monitors** data; nothing is altered. Hard to detect, so the focus is on **prevention** (encryption) rather than detection.
- **Release of message contents** — reading confidential data in transit
- **Traffic analysis** — observing patterns (frequency, length, source/destination of messages) even if content is encrypted

### B. Active Attacks
The attacker **modifies or fabricates** data/streams. These are detectable but hard to prevent absolutely, so the focus is on **detection and recovery**.
- **Masquerade** — pretending to be another entity (identity spoofing)
- **Replay** — capturing data and retransmitting it later to produce an unauthorized effect
- **Modification of messages** — altering part of a legitimate message
- **Denial of Service (DoS)** — preventing normal use of a service/resource

> **Tricky point:** Students often think "active = more dangerous, passive = harmless." Passive attacks (like traffic analysis) can leak metadata that is just as damaging in military/strategic contexts, and is the precursor to active attacks (reconnaissance).

## 3. Threats vs Attacks vs Vulnerability vs Risk (frequently confused)

| Term | Definition | Example |
|---|---|---|
| **Vulnerability** | A weakness in a system | Unpatched software, weak password policy |
| **Threat** | A potential danger that could exploit a vulnerability | A hacker, malware, natural disaster |
| **Attack** | The actual act of exploiting a vulnerability | SQL injection executed against a login form |
| **Risk** | Likelihood × Impact of a threat exploiting a vulnerability | Probability of data breach × cost of breach |

> **Memory trick:** Vulnerability = open door. Threat = a burglar in the area. Attack = the burglar breaking in. Risk = how likely + how costly if it happens.

## 4. Common Categories of Threats

1. **Malware** — viruses, worms, trojans, ransomware, spyware, rootkits
2. **Social Engineering** — phishing, pretexting, baiting, tailgating
3. **Network-based attacks** — sniffing, spoofing, MITM, session hijacking
4. **Application-level attacks** — SQL injection, XSS, buffer overflow
5. **Insider threats** — malicious or negligent employees
6. **Physical threats** — theft of devices, hardware tampering
7. **Advanced Persistent Threats (APT)** — long-term targeted, often state-sponsored

## 5. Security Services (X.800 model — frequently asked in exams)

X.800 (ITU-T) defines five major categories of security services:
1. **Authentication** (peer-entity and data-origin)
2. **Access Control**
3. **Data Confidentiality**
4. **Data Integrity**
5. **Non-repudiation**

## 6. Security Mechanisms

Mechanisms are tools used to implement the services above:
- Encipherment (encryption)
- Digital signature
- Access control mechanisms
- Data integrity mechanisms
- Authentication exchange
- Traffic padding (defends against traffic analysis)
- Routing control
- Notarization (trusted third party)

---

## Tricky / Conceptual Questions

**Q1. Is encryption alone sufficient for information security?**
No. Encryption protects confidentiality but does **not** guarantee integrity (data could still be altered, e.g., bit-flipping attacks on stream ciphers) or availability (a DoS attack on an encrypted channel still works). You need a combination of mechanisms (hashing for integrity, redundancy/firewalls for availability).

**Q2. Why is traffic analysis considered an attack even though the attacker can't read the content?**
Because metadata (who is talking to whom, how often, message size, timing) can reveal sensitive intelligence — e.g., a sudden spike in encrypted traffic between a company and a law firm might reveal an impending acquisition, without anyone reading a single message.

**Q3. Why can active attacks be detected but not always prevented?**
Because prevention would require blocking all possible malicious inputs in advance (extremely hard, e.g., zero-day exploits), whereas detection mechanisms (IDS, checksums, sequence numbers) can spot anomalies *after* an attempt — though by then some damage may already be done. Hence the strategy shifts to detection + recovery.

**Q4. Difference between masquerade and replay attack?**
Masquerade = claiming a false identity (the attacker actively impersonates). Replay = reusing **valid, previously captured** data (the attacker doesn't need to fake identity — they just resend old legitimate traffic, e.g., an old "transfer ₹500" banking transaction).

**Q5. A company's data is encrypted, but a disgruntled employee deletes the encryption keys before resigning. Which leg of the CIA triad is this?**
Availability — even though confidentiality/integrity are technically intact, the data is unusable. This trips up students who instinctively jump to "confidentiality" whenever encryption is mentioned.

---

## Interview Questions & Model Answers

**Q: What is the difference between Information Security and Cybersecurity?**
> Cybersecurity is a subset of Information Security focused specifically on protecting digital systems, networks, and data from cyber threats. Information Security is broader — it includes physical security, paper records, and organizational policy, not just digital assets.

**Q: How would you explain the CIA triad to a non-technical stakeholder?**
> Confidentiality is like a locked diary — only you can read it. Integrity is like a sealed envelope — if it's been opened/tampered with, you'll know. Availability is like a 24/7 ATM — the service must be there when you need it.

**Q: Give a real-world example of a passive attack and an active attack.**
> Passive: A hacker on public Wi-Fi quietly sniffing packets to read someone's unencrypted login credentials. Active: An attacker performing a SYN flood DoS attack on a web server to take it offline.

**Q: What's the difference between threat, vulnerability, and risk in your own words, as if explaining to a manager?**
> Vulnerability is the crack in the wall. Threat is the rain that could come through it. Risk is how much damage you'd take if it rained heavily before you fixed the crack — that's why we patch (fix vulnerabilities) before "rain" (attacks) happens.

**Q: Name two non-technical (administrative) controls used in InfoSec.**
> Security policies/awareness training, and background checks/segregation of duties for employees — both reduce insider threat risk without touching a single line of code.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| CIA Triad | Confidentiality, Integrity, Availability |
| Passive Attack | Observes only — eavesdropping, traffic analysis |
| Active Attack | Modifies/fabricates — masquerade, replay, modification, DoS |
| X.800 Services | Authentication, Access Control, Confidentiality, Integrity, Non-repudiation |
| Vulnerability → Threat → Attack → Risk | Weakness → Danger → Exploitation → Likelihood×Impact |
| Hardest to detect | Passive attacks |
| Hardest to prevent | Active attacks |
