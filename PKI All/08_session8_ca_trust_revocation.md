# Session 8: Certificate Authority (CA), Trust Model, Certificate Issuance, Revocation, Types & Classes of Certificates

## 1. Certificate Authority (CA)

A **Certificate Authority** is a trusted entity that issues digital certificates, verifying the identity of the requester before binding their public key to that verified identity via a digitally signed certificate.

### Core Functions of a CA
- Verifying the identity of certificate requesters (directly, or via a Registration Authority)
- Issuing certificates (signing them with the CA's private key)
- Maintaining and publishing **certificate revocation information** (CRL/OCSP)
- Renewing certificates before expiry
- Protecting its own private key (the **root of trust** — if compromised, every certificate it ever issued becomes untrustworthy)

### Registration Authority (RA) — often confused with CA
The **RA** handles the verification/vetting process (identity checks, document verification) on behalf of the CA, but does **not** itself sign/issue certificates — it forwards verified requests to the CA for actual issuance. This separation allows a single CA to delegate identity-verification workload across many RAs (e.g., regional offices) while keeping the signing key centralized and tightly protected.

> **Exam trap**: "Does the RA have access to the CA's private key?" — **No.** The RA's job is purely verification/registration; only the CA itself performs the actual signing using its private key.

## 2. Trust Models in PKI

### A. Hierarchical Trust Model (most common, used in the web/browsers)
```
          Root CA
         /        \
     Sub-CA 1    Sub-CA 2
      /    \         \
   Cert   Cert       Cert
```
- A single **Root CA** sits at the top, self-signed (it signs its own certificate, since there's no higher authority above it).
- The Root CA's private key is extremely sensitive — often kept **offline** ("air-gapped") to minimize compromise risk.
- The Root CA issues certificates to **Intermediate/Subordinate CAs**, which in turn issue certificates to end entities (websites, users, devices).
- This creates a **Chain of Trust** (also called a **certificate chain**): End-entity cert → signed by Sub-CA → Sub-CA cert signed by Root CA → Root CA is in the trust store (browser/OS).

> **Why use Intermediate/Sub-CAs instead of having the Root CA sign everything directly?** (Frequently asked)
> 1. **Security isolation**: The root key stays offline/protected; if a subordinate CA is compromised, only certificates under that specific sub-CA need to be revoked — the root remains safe and doesn't need to be re-trusted from scratch across the entire internet.
> 2. **Operational flexibility**: Different sub-CAs can have different policies (e.g., one for SSL website certs, another for code-signing certs) without touching the root.
> 3. This is exactly mirrored in the **Session 13 OpenSSL lab** — Root CA (rtca.pgditiss.local) issues to Sub CA (sbca.pgditiss.local), and the Sub-CA issues the actual website certificate.

### B. Web of Trust Model (decentralized — used by PGP, see Session 13)
- No central CA. Instead, **users sign each other's public keys** based on personal verification/trust ("I personally verified this is really Bob's key, so I'll sign it").
- Trust is **transitive but subjective** — if you trust Alice, and Alice has signed Bob's key, you might extend some trust to Bob's key, depending on configurable trust thresholds.
- Used in PGP/GPG email encryption.

### C. Bridge/Mesh Trust Model
- Multiple independent CAs cross-certify each other (or connect through a "bridge CA"), useful for connecting separate organizational PKIs (e.g., government-to-government, or merging two corporate PKI hierarchies after an acquisition) without forcing one to become subordinate to the other.

### Hierarchical vs Web of Trust (classic comparison table)

| Feature | Hierarchical (CA-based) | Web of Trust |
|---|---|---|
| **Central authority?** | Yes (Root CA) | No |
| **Trust basis** | Institutional/audited trust in the CA | Personal verification between individuals |
| **Scalability** | High (works for the entire internet) | Lower (works well for smaller communities) |
| **Single point of failure** | Yes (Root CA compromise is catastrophic) | No single point, but trust can be patchy/inconsistent |
| **Example use** | SSL/TLS website certificates | PGP email encryption |

## 3. Certificate Issuance Process (step-by-step — frequently asked to list)

1. **Key pair generation** — the applicant generates their own public/private key pair (the private key NEVER leaves the applicant's possession).
2. **CSR (Certificate Signing Request) creation** — the applicant creates a CSR containing their public key and identity information (domain name, organization, etc.), and signs the CSR with their own private key (proving they hold the corresponding private key).
3. **Submission to RA/CA** — the CSR is submitted; the RA verifies the requester's identity (domain ownership validation, organization documents, or extended vetting depending on certificate class — see below).
4. **Validation** — depending on certificate type: Domain Validation (DV), Organization Validation (OV), or Extended Validation (EV) — increasing levels of identity scrutiny.
5. **Certificate issuance** — once validated, the CA signs the certificate (containing the applicant's public key + verified identity) using the CA's private key.
6. **Certificate delivery and installation** — the signed certificate is delivered to the applicant, who installs it on their server/application.

> **Critical exam point**: The CA **never sees or handles the applicant's private key** — the entire process is designed so the private key never needs to leave the owner's system. Only the public key (inside the CSR) travels to the CA.

## 4. Certificate Revocation

Certificates sometimes need to be invalidated **before** their natural expiry — e.g., if the private key is compromised, the organization changes its domain, or the certificate was issued in error.

### A. CRL (Certificate Revocation List)
- A **list of serial numbers** of revoked certificates, digitally signed by the CA, published periodically.
- Clients must **download the entire CRL** and check if a certificate's serial number appears in it.

**Drawbacks of CRL:**
- Can become very **large** over time (lists grow as more certificates are revoked).
- **Not real-time** — there's a delay between revocation and the next CRL publication/update cycle, creating a window of vulnerability.
- Bandwidth-heavy for clients to repeatedly download large lists.

### B. OCSP (Online Certificate Status Protocol)
- A **real-time query protocol** — the client asks an **OCSP responder**, "Is this specific certificate (by serial number) still valid?" and gets an immediate response: **good, revoked, or unknown**.

**Advantages over CRL:**
- Real-time/near-real-time status, smaller bandwidth (queries one cert at a time, not a whole list).

**Drawbacks of OCSP:**
- Requires the client to contact the OCSP responder for **every** certificate check, which can leak privacy information (the OCSP responder learns which sites you're visiting) and adds latency/dependency on the responder's availability.

### C. OCSP Stapling (the modern fix — great interview talking point)
- The **web server itself** periodically queries the OCSP responder and "staples" (attaches) the signed OCSP response directly to the TLS handshake when serving its certificate to clients.
- This eliminates the client's need to separately contact the OCSP responder — improving privacy (no third party learns what site you're visiting) and performance (no extra round-trip), while still providing fresh revocation status.

### CRL vs OCSP (heavily tested comparison)

| Feature | CRL | OCSP |
|---|---|---|
| **Mechanism** | Downloaded list of revoked serial numbers | Real-time query for a specific certificate |
| **Real-time?** | No (periodic updates) | Yes (near real-time) |
| **Bandwidth** | Higher (entire list) | Lower (single query/response) |
| **Privacy** | Better (no per-site query sent to a third party) | Worse (responder learns which certs/sites you check) — mitigated by OCSP stapling |
| **Availability dependency** | Less (works offline with cached/recent CRL) | More (requires responder to be reachable) |

> **Tricky question commonly asked**: "If OCSP is real-time and CRL isn't, why does CRL still exist / why not always use OCSP?" — Because OCSP introduces a hard dependency on the responder's availability (if the OCSP server is down, clients may either fail-open, dangerously trusting unverifiable certs, or fail-closed, breaking access entirely) and raises privacy concerns. CRL works even when offline (cached), making both mechanisms relevant depending on context — and OCSP stapling combines the best of both.

## 5. Types and Classes of Certificates

### By Validation Level (most commonly tested)

| Type | Validation Level | What's Verified | Typical Use |
|---|---|---|---|
| **DV (Domain Validated)** | Lowest | Only domain ownership/control | Personal blogs, basic websites |
| **OV (Organization Validated)** | Medium | Domain + verified organization details (legal existence, etc.) | Business websites |
| **EV (Extended Validated)** | Highest | Extensive legal/operational verification of the organization | Banks, e-commerce, high-trust sites (historically showed green address bar — largely deprecated in modern browser UI, but the validation rigor remains relevant) |

### By Usage/Purpose

- **SSL/TLS Certificates** — secure websites (further divided into single-domain, wildcard `*.example.com`, and multi-domain/SAN certificates)
- **Code Signing Certificates** — verify software publisher identity and that code hasn't been tampered with since signing
- **Email Certificates (S/MIME)** — sign/encrypt email (Session 13/14)
- **Client Certificates** — authenticate a user/device to a server (mutual TLS)
- **Root/Intermediate CA Certificates** — used by CAs themselves within the trust hierarchy

### By "Class" (a legacy/traditional classification, still referenced in many Indian/ITISS-style courses, including CCA-India licensed CA classes)
- **Class 1** — basic email address validation only.
- **Class 2** — identity verified against a trusted, pre-verified database (commonly used for individual digital signatures in India, e.g., for income tax e-filing, historically).
- **Class 3** — highest assurance; requires the applicant to personally appear (or use video verification) before a Registration Authority — used for high-value transactions, e-tendering, e-procurement in India.

---

## Tricky / Conceptual Questions

**Q1. If the Root CA's certificate is self-signed, how can anyone trust it — isn't a self-signed certificate supposed to be untrustworthy?**
This is the foundational "trust anchor" paradox, and it's resolved **outside** of cryptography: Root CA certificates aren't trusted because of their signature (which is indeed self-signed and cryptographically "unverifiable" in the chain sense) — they're trusted because they are **pre-installed by operating system/browser vendors after rigorous, audited vetting** of the CA's practices (e.g., via programs like the CA/Browser Forum). Trust in the root is ultimately a matter of **policy and audit**, not pure cryptography — cryptography only secures everything *below* the root in the chain.

**Q2. Why is the compromise of an Intermediate (Sub) CA considered serious but recoverable, while compromise of the Root CA is catastrophic?**
If a Sub-CA's key is compromised, the Root CA can simply **revoke the Sub-CA's certificate**, instantly invalidating everything that Sub-CA issued, while the Root itself and other unrelated Sub-CAs remain trusted and unaffected. If the **Root CA** itself is compromised, there is no higher authority to revoke it — the only fix is removing/replacing the root certificate from every OS/browser trust store worldwide, a massive, slow, and disruptive global operation (this is precisely why root keys are kept offline/air-gapped in real deployments, and why the Session 13 lab keeps Root CA and Sub CA on separate roles).

**Q3. Does OCSP stapling eliminate the need for the OCSP responder entirely?**
No — the web server itself must still periodically query the OCSP responder (just less frequently, and the client doesn't have to). Stapling shifts *who* makes the query (server, not client) and *how often* (periodically cached, not per-client-visit), but the underlying real-time revocation infrastructure (the OCSP responder) is still required.

**Q4. Can a DV certificate be used for an e-commerce/banking site, and is that secure?**
Technically yes (browsers will show the padlock for any valid cert type), but it's **not equally trustworthy** from a verification standpoint — a DV cert only proves "whoever requested this cert controls the domain," not "this is a legitimate, verified business." This is why phishing sites can easily obtain free DV certificates (e.g., via Let's Encrypt) and still show a padlock — the padlock indicates encrypted transport, **not** business legitimacy. Banks/e-commerce typically use OV/EV for the additional identity assurance, even though browsers no longer visually distinguish EV as prominently as they once did.

**Q5. Why is the Registration Authority a separate role from the Certificate Authority, rather than the CA just doing everything itself?**
Separation of duties improves both **security** (the CA's signing key/infrastructure stays centralized and tightly controlled, reducing attack surface) and **scalability** (RAs can be distributed regionally/organizationally to handle identity verification workload without each one needing access to sensitive CA signing infrastructure).

---

## Interview Questions & Model Answers

**Q: Walk me through the full chain of trust when you visit an HTTPS website.**
> The browser receives the website's certificate, which is signed by an Intermediate CA. The browser checks if it has that Intermediate CA's certificate (often sent along in the handshake) and verifies the Intermediate CA's signature using the **Root CA's public key** (which the browser already trusts, pre-installed in its trust store). If that signature checks out, and the Root CA is genuinely in the trusted store, and none of the certificates in the chain are expired or revoked, the browser trusts the website's certificate and proceeds with the secure connection.

**Q: Why would a company choose to run its own internal CA rather than buying certificates from a public CA like DigiCert or Let's Encrypt?**
> For internal-only systems (intranet sites, internal APIs, employee devices), a private internal CA avoids per-certificate costs from public CAs, allows full control over issuance/revocation policy, and avoids unnecessarily exposing internal hostnames to public Certificate Transparency logs (which public CAs must publish to). However, internal CA certificates won't be trusted by external/public clients unless explicitly installed — this is exactly why the Session 10 and Session 13 labs require manually importing the self-signed/internal CA certificate into client browsers/machines.

**Q: What would you do, as a security engineer, if you discovered your organization's TLS private key had been leaked?**
> Immediately revoke the affected certificate with the issuing CA (requesting it be added to the CRL and OCSP responder), generate a new key pair, issue a new CSR, and obtain/install a new certificate — all as fast as possible, since anyone holding the leaked private key could impersonate the server or decrypt previously captured traffic (unless perfect forward secrecy was in use, in which case past sessions remain safe, but future impersonation risk remains until revocation propagates and the cert is replaced).

**Q: What's the practical difference between CRL and OCSP from an end-user's browsing experience?**
> With CRL, the browser/OS periodically downloads a (potentially large) list of revoked certificates and checks locally — fast per-check, but the list might be slightly outdated. With OCSP, the browser can ask a live server in real time whether a specific certificate is still valid — more current, but adds a network round-trip and a privacy consideration, since the OCSP responder technically learns which certificate (and thus often which site) is being checked, unless OCSP stapling is used to avoid this.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| CA | Issues and signs certificates, binding public key to identity |
| RA | Verifies identity; does NOT sign certificates itself |
| Hierarchical trust | Root CA → Sub-CA → End entity; used in the web |
| Web of Trust | Decentralized, peer-signed; used in PGP |
| Root CA risk | Catastrophic if compromised — no higher authority to revoke it |
| CSR | Contains public key + identity, signed by applicant's own private key |
| Private key in issuance | NEVER leaves the applicant — only the public key (in CSR) goes to CA |
| CRL | Downloaded list of revoked serials; not real-time |
| OCSP | Real-time query per certificate; privacy/availability tradeoffs |
| OCSP Stapling | Server queries OCSP and attaches response to handshake — best of both worlds |
| DV / OV / EV | Increasing levels of identity validation rigor |
| Class 1/2/3 (India) | Email-only / DB-verified identity / in-person high-assurance |
