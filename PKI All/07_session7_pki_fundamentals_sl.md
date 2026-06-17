# Session 7: PKI Fundamentals — Digital Signature & Digital Certificate (+ Self-Learning Topics, Sessions 1–7)

## 1. What is PKI? (The big picture before diving into details)

**Public Key Infrastructure (PKI)** is the complete framework of hardware, software, policies, procedures, and roles needed to **create, manage, distribute, use, store, and revoke digital certificates** and manage public-key encryption. PKI binds public keys to verified identities, solving the core problem: "How do I know this public key really belongs to who it claims to belong to?"

PKI's main components (previewed here, detailed in Session 8 onward):
- Certificate Authority (CA)
- Registration Authority (RA)
- Digital Certificates
- Certificate Revocation mechanisms (CRL/OCSP)
- A Trust Model connecting all of the above

## 2. Digital Signatures

A **digital signature** is a cryptographic mechanism that provides **authentication, integrity, and non-repudiation** for a digital message or document, using **asymmetric cryptography**.

### How a Digital Signature is Created and Verified (Critical Process — frequently drawn as a diagram in exams)

**Signing (by sender, using their PRIVATE key):**
1. Take the original message/document.
2. Compute its hash: `H = Hash(Message)` (e.g., using SHA-256).
3. Encrypt the hash using the sender's **private key**: `Signature = Encrypt_private(H)`.
4. Send the original message + the signature together.

**Verification (by receiver, using sender's PUBLIC key):**
1. Receiver independently computes the hash of the received message: `H' = Hash(received message)`.
2. Receiver decrypts the received signature using the sender's **public key**: `H = Decrypt_public(Signature)`.
3. Compare H and H'. **If they match → signature is valid** (message is authentic and unaltered). If they don't match → message was tampered with, or signature is invalid/not from the claimed sender.

> **The single most common point of confusion in this entire course**: In digital signatures, the **sender encrypts with their PRIVATE key** (so anyone with the public key can verify) — this is the OPPOSITE of confidentiality-focused encryption, where the sender encrypts with the **receiver's PUBLIC key** (so only the receiver's private key can decrypt). Memorize this contrast carefully:
> - **Confidentiality (encryption)**: Encrypt with receiver's **public** key → Decrypt with receiver's **private** key.
> - **Authentication (digital signature)**: "Encrypt"/sign with sender's **private** key → Verify with sender's **public** key.

### Why We Sign the HASH, Not the Whole Message
As covered in Session 6 — asymmetric operations are slow and size-limited; hashing first compresses the message into a small fixed-size digest, making the signature operation efficient regardless of document size, while the hash's collision resistance ensures the signature effectively still "covers" the entire original content.

### Properties Provided by Digital Signatures

| Property | How it's achieved |
|---|---|
| **Authentication** | Only the holder of the private key could have created a valid signature verifiable with the matching public key |
| **Integrity** | Any change to the message changes its hash, causing verification to fail |
| **Non-repudiation** | Sender cannot deny signing, since only they possess the private key (assuming proper key custody) |

> **Digital Signature vs Digital Certificate vs Handwritten Signature vs Electronic Signature** (a classic confusing terminology cluster):
> - **Handwritten signature**: physical, easily forged, no cryptographic guarantee.
> - **Electronic signature**: a broad legal term — ANY electronic indication of intent to sign (could be a scanned image of a signature, a typed name, a clicked "I agree" button). May or may not be cryptographically secure.
> - **Digital signature**: a *specific cryptographic technique* (as described above) that is one strong way to implement an electronic signature with verifiable security guarantees.
> - **Digital certificate**: NOT a signature itself — it's a document that binds a public key to an identity, *issued and signed by a CA* (covered next, and in depth in Session 8).

## 3. Digital Certificates

A **digital certificate** is an electronic document that uses a digital signature to bind a **public key** to an **identity** (a person, organization, server, or device), issued by a trusted third party called a **Certificate Authority (CA)**.

### Why Are Digital Certificates Needed? (The core problem PKI solves)
If Bob simply publishes his public key online, how does Alice know it's *really* Bob's key and not an attacker's? An attacker could publish a fake public key claiming to be Bob's, intercept messages meant for Bob, and decrypt them. A digital certificate solves this by having a **mutually trusted CA vouch for the binding** between Bob's identity and his public key, by signing that binding with the CA's own private key.

### Structure of a Digital Certificate (X.509 standard — heavily tested)

A standard **X.509 certificate** typically contains:
1. **Version** (X.509 v1, v2, or v3 — v3 is current/most common, supports extensions)
2. **Serial Number** — unique identifier assigned by the issuing CA
3. **Signature Algorithm** — the algorithm the CA used to sign this certificate (e.g., SHA256withRSA)
4. **Issuer** — the CA's distinguished name (who issued/signed this certificate)
5. **Validity Period** — "Not Before" and "Not After" dates
6. **Subject** — the entity's distinguished name (who this certificate belongs to — person, organization, website domain)
7. **Subject Public Key Info** — the actual public key being certified, and its algorithm
8. **Extensions** (v3 only) — e.g., Key Usage, Extended Key Usage, Subject Alternative Name (SAN), CRL Distribution Points, Authority Key Identifier
9. **CA's Digital Signature** — over all the above fields, created using the CA's private key

### How Certificate Verification Works (conceptually, detailed more in Session 8)
1. Receiver gets the certificate (containing sender's public key + identity claims).
2. Receiver checks the CA's signature on the certificate using the **CA's own public key** (which the receiver already trusts, typically pre-installed as a "root certificate" in the OS/browser).
3. If the signature is valid, the receiver trusts that the public key inside truly belongs to the claimed identity.
4. Receiver also checks validity period and revocation status (Session 8: CRL/OCSP).

---

## SELF-LEARNING TOPICS (Sessions 1–7) — Detailed Coverage

### A. Information Security Goals (CIA Triad) and Threat Mapping
Already covered in depth in Session 1 notes. **Threat mapping** means associating specific threats with which CIA goal they violate, e.g.:
- Eavesdropping → violates Confidentiality
- Data tampering in transit → violates Integrity
- DDoS attack → violates Availability
- Phishing leading to credential theft → violates Confidentiality (and can cascade into Integrity/Availability violations)

### B. Difference Between Encryption, Hashing, and Digital Signatures (a favorite 5-mark exam question)

| Feature | Encryption | Hashing | Digital Signature |
|---|---|---|---|
| **Reversible?** | Yes (with correct key) | No (one-way) | N/A — verifies, doesn't reveal content |
| **Goal** | Confidentiality | Integrity (fingerprint) | Authentication + Integrity + Non-repudiation |
| **Uses a key?** | Yes (symmetric or asymmetric) | No (typically) | Yes (asymmetric key pair) |
| **Output size** | Same order as input (roughly) | Fixed size always | Fixed size (size of the encrypted hash) |
| **Example** | AES, RSA | SHA-256 | RSA/ECDSA signing of a hash |

### C. Symmetric vs Asymmetric Encryption Use Cases
- **Symmetric**: Bulk file/disk encryption, VPN tunnel data encryption, database encryption at rest — anywhere speed matters and keys can be securely pre-shared or exchanged via a hybrid method.
- **Asymmetric**: Key exchange (establishing the symmetric session key), digital signatures, certificate issuance, secure email (initial key wrap), SSH authentication.

### D. Why Diffie-Hellman is Required for Secure Key Exchange
Covered in depth in Session 5 — DH solves the problem of establishing a shared secret over a public channel without prior shared secrets, eliminating the need for risky physical/manual key distribution.

### E. Secure Hashing vs Encryption (SHA vs AES)
SHA (hashing) provides a one-way fingerprint for integrity verification — cannot be reversed, no key needed (for plain SHA). AES (encryption) provides reversible confidentiality — requires a key for both encryption and decryption. **They solve fundamentally different problems** and are often used together (e.g., encrypt-then-MAC schemes).

### F. Role of HMAC in Message Integrity
Covered in depth in Session 6 — HMAC adds a secret key to hashing, enabling verification that a message wasn't tampered with AND that it came from someone who knows the shared key (unlike plain hashing, which only catches accidental corruption).

### G. Common Cryptographic Implementation Mistakes
Covered in depth in Session 5 — weak RNGs, key/IV/nonce reuse, hardcoded keys, downgrade attacks, padding oracle vulnerabilities, insufficient key lengths.

### H. Case Study: Password Database Breach

**Why lack of salt leads to rainbow table attacks:**
A **rainbow table** is a precomputed table mapping common passwords (and variations) to their hash values. If passwords are stored as `hash(password)` with no salt, an attacker who steals the database can instantly look up each hash in the precomputed table to reveal the original password — and since many users reuse passwords, identical hashes across different users instantly reveal they share the same password too. A **salt** (a unique random value per user, stored alongside the hash, combined with the password before hashing) defeats this because the attacker would need a separate precomputed table for every possible salt value — making precomputation infeasible at scale.

**Role of SHA and HMAC in secure password storage:**
- Plain SHA (even with salt) is still **too fast** — modern GPUs can compute billions of SHA-256 hashes per second, making brute-force/dictionary attacks on stolen salted-SHA hashes still feasible for weak passwords.
- HMAC alone isn't typically used for password storage either, because it's also fast.
- The actual best practice is **purpose-built slow hashing algorithms**: **bcrypt, scrypt, PBKDF2 (which internally often uses HMAC, e.g., HMAC-SHA256, repeated thousands of times), or Argon2** — these are deliberately slow and/or memory-intensive, making brute-force attacks computationally expensive even with powerful hardware.
- **Key exam point**: PBKDF2 is literally built using HMAC as its core building block, applying it iteratively (e.g., 100,000+ rounds) — this is the direct, concrete link between HMAC (Session 6) and real-world secure password storage.

---

## Tricky / Conceptual Questions

**Q1. If a digital signature uses the sender's private key, and private keys are supposed to be secret, how can anyone verify the signature?**
Because verification uses the **public key** (the matching half of the key pair), which is meant to be shared openly. The private key is only used to *create* the signature; verification never requires the private key — only the public key, which doesn't need to be secret. This asymmetry is exactly what makes the scheme work for non-repudiation.

**Q2. Does encrypting a message with someone's private key provide confidentiality?**
No — and this is a major trap question. If you encrypt with a private key, **anyone with the corresponding public key** (which is freely available) can decrypt it — so there's no confidentiality at all. This operation only makes sense for **authentication/signing** (proving origin), not for hiding content from unauthorized viewers.

**Q3. Can a digital certificate exist without a digital signature?**
No — by definition, a digital certificate's trustworthiness comes entirely from the CA's digital signature over its contents. A certificate without a valid signature (or with a broken/invalid signature) is meaningless — it's just an unverified claim, no different from a sticky note saying "trust me, this is my key."

**Q4. If I create my own self-signed certificate claiming to be "google.com," will browsers trust it?**
No — browsers maintain a list of trusted **root CA public keys**. A certificate is only trusted if it can be verified through a **chain of trust** back to one of these pre-installed root CAs. A self-signed certificate (signed by its own private key, not a trusted CA) will trigger a browser warning, because there's no trusted third party vouching for the identity claim — this is exactly the scenario referenced in the Session 10 lab (importing a self-signed cert to remove browser warnings).

**Q5. Why is "lack of salt" specifically the vulnerability, rather than "weak hash algorithm," in many password breach case studies?**
Even a strong, modern hash algorithm (like SHA-256) is vulnerable to rainbow table attacks if unsalted, because the vulnerability isn't about the hash being "crackable" mathematically — it's about **precomputation feasibility**. A salt doesn't make the hash function itself stronger; it makes precomputed attacks economically/computationally infeasible by forcing a unique computation per user. This is why "use SHA-256 instead of MD5" alone does NOT fix an unsalted password storage scheme.

---

## Interview Questions & Model Answers

**Q: Explain, step by step, what happens when you digitally sign a PDF document.**
> First, a hash (digest) of the PDF's content is computed using an algorithm like SHA-256. That hash is then encrypted using my private key, producing the digital signature, which gets embedded into the PDF along with my certificate (containing my public key, signed by a CA). When someone opens the PDF, their PDF reader recomputes the hash of the document content, decrypts my embedded signature using my public key (extracted from my certificate) to get the original hash, and compares the two hashes. If they match, the reader confirms the document hasn't been altered since I signed it, and that I — the certificate holder — am genuinely the signer.

**Q: What's the difference between a digital signature and an electronic signature, and why does this distinction matter legally?**
> An electronic signature is a broad legal category covering any electronic mark of intent to sign — even a typed name or a scanned image — and its evidentiary strength varies. A digital signature is a specific cryptographic technique providing mathematically verifiable proof of authenticity, integrity, and non-repudiation. This distinction matters legally because laws like India's IT Act (covered in Session 14) and global frameworks like eIDAS treat them differently — many jurisdictions specifically require certificate-based digital signatures (not just any electronic signature) for high-stakes legal/financial documents to have the strongest evidentiary and non-repudiation value.

**Q: Why can't you just trust any public key you find online without a certificate?**
> Because there's no inherent binding between a public key and an identity — anyone can generate a key pair and claim any name/identity they want. Without a trusted third party (CA) vouching for that binding through a verifiable digital signature, you have no cryptographic basis to believe the key actually belongs to who it claims to belong to — you'd be vulnerable to impersonation/MITM attacks (this connects directly back to Diffie-Hellman's authentication gap in Session 5).

**Q: How would you explain rainbow table attacks and salting to someone non-technical?**
> Imagine a thief has a giant pre-made dictionary mapping common passwords to what they'd look like "locked" (hashed). If your password is stored without anything extra, the thief just looks it up in their dictionary and instantly finds your real password. Salting is like adding a unique, random ingredient to your password before locking it — now the thief's dictionary doesn't match anymore, and they'd have to build a whole new dictionary for every single user, which is far too expensive to do at scale.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| Digital signature creation | Hash message → Encrypt hash with SENDER's private key |
| Digital signature verification | Decrypt signature with sender's PUBLIC key → compare with recomputed hash |
| Confidentiality encryption | Encrypt with RECEIVER's public key → decrypt with receiver's private key |
| Digital certificate | Binds public key to identity; signed by a CA |
| X.509 v3 | Current certificate standard; supports extensions (SAN, Key Usage, etc.) |
| Self-signed cert | No trusted CA signature → browser warning |
| Rainbow table | Precomputed hash lookup table → defeated by salting |
| Salting | Unique random value per user added before hashing |
| PBKDF2 | Password hashing built on repeated HMAC iterations |
| Best password hashing | bcrypt / scrypt / Argon2 / PBKDF2 — NOT plain SHA-256 |
