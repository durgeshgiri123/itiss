# Session 14 & 15: IT Act, LDAP/Active Directory, Blockchain, Email Security Lab, Self-Learning (Sessions 8–15)

---

## PART A: IT Act & Legal Framework

### 1. Information Technology Act, 2000 (India)

The **IT Act 2000** is the primary law governing cybercrimes and electronic commerce in India. Amended significantly in **2008 (IT Amendment Act)**.

#### Key Objectives
- Legal recognition of electronic records and digital signatures
- Facilitate e-governance and e-commerce
- Define cybercrimes and penalties
- Establish regulatory bodies (CERT-In, Cyber Appellate Tribunal)

---

### 2. Key Sections of IT Act 2000 (Amended 2008)

| Section | Subject | Offence / Provision |
|---------|---------|---------------------|
| **Sec 43** | Unauthorized access/damage | Civil liability — compensation up to ₹1 crore |
| **Sec 43A** | Body corporate data protection | Negligent handling of sensitive personal data |
| **Sec 65** | Tampering with source code | Up to 3 years / ₹2 lakh fine |
| **Sec 66** | Computer-related offences | Up to 3 years / ₹5 lakh fine |
| **Sec 66A** | Offensive online messages | **Struck down by Supreme Court (Shreya Singhal 2015)** |
| **Sec 66B** | Receiving stolen computer resource | Up to 3 years / ₹1 lakh |
| **Sec 66C** | Identity theft | Up to 3 years / ₹1 lakh |
| **Sec 66D** | Cheating by impersonation online | Up to 3 years / ₹1 lakh |
| **Sec 66E** | Violation of privacy | Up to 3 years / ₹2 lakh |
| **Sec 66F** | Cyber terrorism | Up to life imprisonment |
| **Sec 67** | Publishing obscene material | Up to 3 years / ₹5 lakh |
| **Sec 67A** | Explicit sexual content | Up to 5 years / ₹10 lakh |
| **Sec 67B** | Child pornography | Up to 7 years / ₹10 lakh |
| **Sec 69** | Govt power to intercept/monitor | With proper authorization |
| **Sec 69A** | Blocking of websites | Power to block public access to info |
| **Sec 70** | Protected system | Unauthorized access to critical infra |
| **Sec 72** | Breach of confidentiality | Up to 2 years / ₹1 lakh |
| **Sec 79** | Intermediary liability | Safe harbor for intermediaries if due diligence followed |

> **Tricky Q:** Section 66A was struck down in 2015 (Shreya Singhal v. Union of India) for violating freedom of speech under Article 19(1)(a).

---

### 3. Digital Signature under IT Act

- **Section 3** of IT Act: Digital signatures are legally valid
- Based on **asymmetric cryptography** + hash
- Certifying Authority (CA) issues Digital Signature Certificates (DSC)
- **Controller of Certifying Authorities (CCA)** — apex body for CAs in India
- Licensed CAs in India: eMudhra, Sify, NSDL, IDRBT, NIC

#### Classes of DSC (India)
| Class | Usage |
|-------|-------|
| Class 1 | Email identity verification |
| Class 2 | IT returns, ROC filings (discontinued) |
| Class 3 | E-tendering, e-auctions, MCA filings |

---

### 4. CERT-In — Computer Emergency Response Team India

- Under **Ministry of Electronics and Information Technology (MeitY)**
- Functions: Incident response, cybersecurity advisories, coordination
- **CERT-In Directions 2022** — Organizations must report cyber incidents within **6 hours**
- Mandates: accurate system clocks (IST), maintain logs for 180 days

---

### 5. Other Relevant Laws

| Law | Relevance |
|-----|-----------|
| **IPC Section 420** | Cheating (applies to online fraud) |
| **IPC Section 463-477A** | Forgery (digital documents) |
| **PDPB / DPDP Act 2023** | Digital Personal Data Protection Act — India's GDPR equivalent |
| **GDPR** | EU data protection — applies to Indian companies handling EU data |
| **Aadhaar Act 2016** | Governs Aadhaar data and authentication |
| **PCI-DSS** | Payment card security standard |
| **RBI Guidelines** | Cybersecurity framework for banks |

---

## PART B: LDAP & Active Directory

### 6. LDAP — Lightweight Directory Access Protocol

#### What is LDAP?
A protocol for **accessing and maintaining distributed directory information services** over an IP network. Based on X.500 standard but simplified.

- **Port 389** — LDAP (plain)
- **Port 636** — LDAPS (LDAP over TLS)
- **Port 3268** — Global Catalog
- **Port 3269** — Global Catalog over TLS

#### LDAP Directory Structure (DIT — Directory Information Tree)
```
dc=example,dc=com          ← Root (Domain Component)
├── ou=Users               ← Organizational Unit
│   ├── cn=Alice Smith     ← Common Name (user entry)
│   └── cn=Bob Jones
├── ou=Groups
│   └── cn=Admins
└── ou=Computers
    └── cn=WebServer01
```

#### LDAP Distinguished Name (DN)
- Unique identifier for each entry
- Example: `cn=Alice Smith,ou=Users,dc=example,dc=com`
- Components: `cn` (Common Name), `ou` (Org Unit), `dc` (Domain Component), `o` (Organization), `l` (Locality), `c` (Country)

#### LDAP Operations
| Operation | Description |
|-----------|-------------|
| **Bind** | Authenticate to the directory |
| **Search** | Query entries matching a filter |
| **Add** | Add a new entry |
| **Delete** | Remove an entry |
| **Modify** | Change entry attributes |
| **ModifyDN** | Rename or move entry |
| **Unbind** | Close connection |
| **Compare** | Test if attribute has a value |

#### LDAP Search Filter Examples
```
(cn=Alice*)              # Wildcard search
(objectClass=user)       # All user objects
(&(objectClass=user)(department=IT))  # AND filter
(|(cn=Alice)(cn=Bob))   # OR filter
(!(disabled=TRUE))      # NOT filter
```

---

### 7. Active Directory (AD)

#### What is Active Directory?
Microsoft's **directory service** that uses LDAP, Kerberos, DNS, and Group Policy to manage users, computers, and resources in a Windows domain.

#### AD Key Components
| Component | Description |
|-----------|-------------|
| **Domain** | Logical group of AD objects sharing the same namespace |
| **Domain Controller (DC)** | Server running AD DS; handles auth |
| **Forest** | Collection of one or more domains; highest trust boundary |
| **Tree** | Domains sharing a contiguous DNS namespace |
| **OU (Organizational Unit)** | Container for grouping objects; GPOs applied here |
| **GPO (Group Policy Object)** | Policy settings applied to users/computers |
| **Trust** | Relationship allowing access across domains |
| **Schema** | Defines object types and attributes |
| **Global Catalog** | Partial replica of all objects in the forest |

#### AD Trust Types
| Trust | Direction | Description |
|-------|-----------|-------------|
| **Parent-Child** | Two-way transitive | Auto-created within a tree |
| **Tree-Root** | Two-way transitive | Between tree root domains |
| **External** | One-way or two-way, non-transitive | Across forests or NT domains |
| **Forest** | One-way or two-way transitive | Between forests |
| **Shortcut** | One-way or two-way | Optimize auth in large forests |

#### SYSVOL & NETLOGON
- **SYSVOL** — Shared folder replicated across all DCs; contains GPOs and scripts
- **NETLOGON** — Shared folder for logon scripts

#### AD Replication
- Uses **FRS** (legacy) or **DFSR** (modern) for SYSVOL replication
- AD uses **multi-master replication** — changes on any DC replicate to all others
- **FSMO Roles** (Flexible Single Master Operations) — 5 roles: Schema Master, Domain Naming Master, PDC Emulator, RID Master, Infrastructure Master

---

### 8. LDAP Injection

- Attack where malicious LDAP filters are injected via user input
- Example: Input `*)(uid=*))(|(uid=*` can manipulate query logic
- **Defense:** Input validation, parameterized LDAP queries, principle of least privilege

---

## PART C: Blockchain & Distributed Ledger Technology

### 9. What is Blockchain?

A **distributed, decentralized, immutable ledger** that records transactions across multiple nodes. No single point of control.

> Invented conceptually by **Stuart Haber & W. Scott Stornetta (1991)**; popularized by **Satoshi Nakamoto (Bitcoin, 2008)**.

---

### 10. Blockchain Structure

#### Block Components
```
┌─────────────────────────────┐
│  Block Header               │
│  ├── Previous Block Hash    │  ← Links blocks (chain)
│  ├── Merkle Root            │  ← Root hash of all transactions
│  ├── Timestamp              │
│  ├── Nonce                  │  ← Proof of Work value
│  └── Difficulty Target      │
├─────────────────────────────┤
│  Transaction Data           │
│  ├── Tx1, Tx2, Tx3 ...      │
└─────────────────────────────┘
```

#### Merkle Tree
- Binary tree of **transaction hashes**
- Root hash = single hash representing ALL transactions
- Enables **SPV (Simplified Payment Verification)** — verify tx without full blockchain
- Any change to a transaction changes its hash → changes Merkle root → changes block hash → invalidates all subsequent blocks

---

### 11. Blockchain Types

| Type | Description | Example |
|------|-------------|---------|
| **Public** | Open to all; permissionless | Bitcoin, Ethereum |
| **Private** | Restricted access; permissioned | Hyperledger Fabric |
| **Consortium** | Semi-decentralized; controlled by group | R3 Corda, Quorum |
| **Hybrid** | Mix of public + private | Dragonchain |

---

### 12. Consensus Mechanisms

| Mechanism | Description | Used By |
|-----------|-------------|---------|
| **PoW** (Proof of Work) | Solve computational puzzle | Bitcoin |
| **PoS** (Proof of Stake) | Validators stake cryptocurrency | Ethereum 2.0 |
| **DPoS** (Delegated PoS) | Token holders vote for delegates | EOS, TRON |
| **PBFT** (Practical Byzantine Fault Tolerance) | Vote-based; tolerates f < n/3 faulty nodes | Hyperledger |
| **PoA** (Proof of Authority) | Identity-based validator approval | Private chains |
| **PoET** (Proof of Elapsed Time) | Random wait timer (Intel SGX) | Hyperledger Sawtooth |

> **Tricky Q:** Bitcoin uses PoW. Ethereum switched from PoW to PoS in September 2022 ("The Merge").

---

### 13. Blockchain Cryptography

| Component | Cryptographic Mechanism |
|-----------|------------------------|
| **Transaction signing** | ECDSA (secp256k1 curve) |
| **Block hashing** | SHA-256 (Bitcoin) / Keccak-256 (Ethereum) |
| **Address generation** | Hash of public key (RIPEMD-160(SHA-256(pubkey))) |
| **Merkle tree** | SHA-256 double hashing |
| **Smart contracts** | Keccak-256 for function selectors |

### Bitcoin Address Generation
```
Private Key (256-bit random) 
    → ECDSA → Public Key (512-bit)
    → SHA-256 → 
    → RIPEMD-160 → 
    → Add version byte → 
    → Double SHA-256 checksum → 
    → Base58Check encoding → Bitcoin Address
```

---

### 14. Smart Contracts

- **Self-executing contracts** with terms written in code on blockchain
- Automatically execute when conditions are met
- **Ethereum** is the leading platform (Solidity language)
- **Hyperledger Fabric** uses chaincode (Go, Java, Node.js)

#### Smart Contract Use Cases
- Decentralized Finance (DeFi)
- Supply chain management
- Healthcare records
- Digital identity (Self-Sovereign Identity)
- E-voting systems
- NFTs (Non-Fungible Tokens)

---

### 15. Blockchain Security Concerns

| Attack | Description |
|--------|-------------|
| **51% Attack** | Attacker controls >50% mining power; can double-spend |
| **Sybil Attack** | One entity creates many fake identities |
| **Selfish Mining** | Pool withholds blocks to gain disproportionate rewards |
| **Double Spending** | Spend same coin twice before first tx confirms |
| **Smart Contract Bugs** | Logic flaws exploited (DAO hack 2016 — $60M) |
| **Eclipse Attack** | Isolate a node from honest peers |
| **Replay Attack** | Replay tx from one chain on another (post hard fork) |

---

### 16. Blockchain in Security/PKI

- **Certificate Transparency** using blockchain
- **Decentralized PKI (DPKI)** — Namecoin, Sovrin
- **Self-Sovereign Identity (SSI)** — Users control their own identity
- **Timestamp services** — Immutable proof of existence at a time
- **Supply chain integrity** — Track document/product authenticity

---

## PART D: Email Security

### 17. Email Security Protocols

#### SPF — Sender Policy Framework
- **DNS TXT record** listing authorized mail servers for a domain
- Receiving server checks if sending IP is in SPF record
- Prevents **email spoofing/forgery**
- Example record: `v=spf1 ip4:192.0.2.0/24 include:mailprovider.com -all`
- `-all` = fail (strict); `~all` = softfail; `+all` = pass all (dangerous)

#### DKIM — DomainKeys Identified Mail
- Sender's mail server **signs email with private key**
- Public key published in **DNS TXT record** (`_domainkey.example.com`)
- Receiver verifies signature → ensures email not tampered in transit
- Signs specific headers + body
- **Does NOT prevent domain spoofing** in From header

#### DMARC — Domain-based Message Authentication, Reporting & Conformance
- Builds on SPF + DKIM
- Specifies **policy** for handling failures: `none`, `quarantine`, `reject`
- Provides **reporting** (forensic + aggregate)
- Aligns SPF/DKIM results with the **From: header domain**
- Example: `v=DMARC1; p=reject; rua=mailto:dmarc@example.com`

#### Email Auth Flow
```
1. Sender sends email
2. Receiver checks SPF → sending IP authorized?
3. Receiver checks DKIM → signature valid?
4. Receiver checks DMARC → alignment + policy action
5. If all pass → delivered; if fail → quarantine/reject
```

> **Tricky Q:** SPF checks sending IP; DKIM checks message integrity; DMARC enforces alignment of both with the visible From domain.

---

### 18. Email Encryption

| Method | Description |
|--------|-------------|
| **S/MIME** | Certificate-based, integrated into email clients |
| **PGP/GPG** | Key-based, web of trust |
| **TLS (STARTTLS)** | Encrypts transport — not end-to-end |
| **MTA-STS** | Forces TLS for mail delivery to a domain |

---

### 19. Email Header Analysis

Important headers for forensics:
- `Received:` — Trace of servers that handled email (read bottom to top)
- `X-Originating-IP:` — Original sender IP
- `DKIM-Signature:` — DKIM signature block
- `Authentication-Results:` — SPF/DKIM/DMARC results
- `Message-ID:` — Unique identifier per message
- `X-Spam-Status:` — Spam score

---

## PART E: Self-Learning Review (Sessions 8–15)

### 20. Key Concepts Summary Table

| Session | Key Topics | Critical Points |
|---------|-----------|----------------|
| **8** | CA, Trust Models, CRL/OCSP | OCSP stapling reduces CA load |
| **9** | Aadhaar/e-Sign, Timestamping, XCA | e-Sign uses Aadhaar OTP as auth |
| **10** | PKCS, FIPS 140-2, XCA Lab | PKCS#12 = PFX (cert + key bundle) |
| **11** | MFA, SSO, OAuth, Graphical Passwords | TOTP uses HMAC-SHA1 + time |
| **12** | Kerberos, FIDO2, Zero Trust | Golden Ticket = stolen KRBTGT hash |
| **13** | TLS 1.3, PGP, S/MIME, OpenSSL | TLS 1.3 = 1-RTT, mandatory PFS |
| **14** | IT Act, LDAP, Blockchain | Sec 66F = cyber terrorism = life imprisonment |

---

### 21. PKCS Standards Quick Reference

| Standard | Name | Description |
|----------|------|-------------|
| PKCS#1 | RSA Cryptography | RSA encryption/signing format |
| PKCS#3 | DH Key Agreement | Diffie-Hellman standard |
| PKCS#5 | Password-Based Crypto | PBKDF2, key derivation from password |
| PKCS#7 | Cryptographic Message Syntax | CMS — signed/encrypted data (S/MIME) |
| PKCS#8 | Private Key Info | Private key storage format |
| PKCS#10 | CSR | Certificate Signing Request format |
| PKCS#11 | Cryptoki | API for hardware tokens/HSMs |
| PKCS#12 | PFX | Bundle private key + certificate (`.p12`, `.pfx`) |

---

### 22. FIPS 140-2 — Federal Information Processing Standard

- US NIST standard for **cryptographic module validation**
- **4 security levels:**

| Level | Description |
|-------|-------------|
| **Level 1** | Basic crypto requirements; no physical security |
| **Level 2** | Tamper-evident coatings/seals; role-based auth |
| **Level 3** | Tamper-resistant; identity-based auth; key zeroing |
| **Level 4** | Complete physical protection; environmental attacks |

- HSMs (Hardware Security Modules) are typically **FIPS 140-2 Level 3**
- **FIPS 140-3** is the current version (2019), aligned with ISO 19790

---

### 23. Aadhaar & e-Sign

#### Aadhaar Authentication
- **OTP-based** — Aadhaar OTP sent to registered mobile
- **Biometric** — Fingerprint or iris scan
- **Demographic** — Name/DOB verification
- Managed by **UIDAI** (Unique Identification Authority of India)
- API: **AUA/KUA** (Authentication/KYC User Agency) connects to UIDAI CIDR

#### e-Sign Service
- Allows citizens to digitally sign documents using **Aadhaar authentication**
- **ASP** (Application Service Provider) integrates e-Sign
- **ESP** (e-Sign Service Provider) — licensed by CCA
- Flow: Document hash → Aadhaar OTP/biometric auth → Certificate issued → Document signed → Certificate revoked post-use (one-time use cert)

---

### 24. Timestamping

- **RFC 3161** — Time-Stamp Protocol (TSP)
- **TSA** (Time Stamp Authority) signs the hash of a document with its private key + current time
- Proves document existed at a specific time
- Used in: legal documents, code signing, audit trails
- Token contains: hash of data, timestamp, TSA signature, policy OID

---

### 25. XCA (X Certificate and Key Manager) — Lab Reference

XCA is a GUI tool for managing X.509 certificates, CAs, and keys.

#### XCA Key Operations
- Create Root CA template → Generate Root CA certificate
- Create Intermediate CA → Sign with Root CA
- Create End-Entity certificate → Sign with Intermediate CA
- Export: PEM, DER, PKCS#12 formats
- Create CRL (Certificate Revocation List)
- View certificate chain and extensions

---

### 26. Graphical Passwords

| Type | Description | Example |
|------|-------------|---------|
| **Recognition-based** | Identify images from set | Passfaces |
| **Recall-based** | Draw pattern/sketch | Android pattern lock |
| **Cued-recall** | Click points on image | PassPoints |
| **Hybrid** | Combination | CAPTCHA + image selection |

**Advantages over text passwords:** No shoulder surfing of text, memorable, no dictionary attack
**Disadvantages:** Smudge attacks (pattern lock), larger attack space needed, slower to enter

---

## 27. Master Interview Questions (Sessions 8–15 Cross-Topic)

**Q1: What is OCSP stapling and why is it better than CRL?**
> OCSP stapling has the server cache and attach the OCSP response to the TLS handshake. This eliminates client-to-CA roundtrips, improves performance, and preserves user privacy (CA doesn't see which certs are being checked).

**Q2: Explain the difference between PKCS#7 and PKCS#12.**
> PKCS#7 (CMS) is used to sign or encrypt data (used in S/MIME). PKCS#12 bundles a private key with its certificate chain into a single encrypted file (.p12/.pfx) for transport/backup.

**Q3: What is the Aadhaar e-Sign flow?**
> User authenticates via Aadhaar OTP/biometric → ESP issues a one-time DSC → document hash is signed → certificate is immediately revoked post-signing. This ensures non-repudiation without giving users a permanent key.

**Q4: What is a 51% attack on blockchain?**
> An attacker controlling more than 50% of the network's mining/staking power can reverse transactions, double-spend, and prevent new transactions from confirming. They cannot alter old blocks or steal others' funds directly.

**Q5: How does SPF differ from DKIM?**
> SPF validates the **sending server's IP** against a DNS-published whitelist. DKIM validates **message integrity** using a cryptographic signature. SPF can be bypassed by legitimate mail forwarding; DKIM survives forwarding but not content modification.

**Q6: What is DMARC and what problem does it solve?**
> DMARC enforces alignment between SPF/DKIM results and the visible From domain, and specifies what to do with failures (none/quarantine/reject). It solves the problem of email spoofing where attackers use a different domain in headers vs envelope.

**Q7: What is FIPS 140-2 Level 3?**
> A hardware security module standard requiring tamper-resistant physical security, identity-based operator authentication, and automatic zeroing of cryptographic keys upon tamper detection.

**Q8: Explain Zero Trust in one sentence and name its 3 core principles.**
> Zero Trust = "Never Trust, Always Verify." Core principles: (1) Verify explicitly, (2) Use least privilege, (3) Assume breach.

**Q9: What is the RFC 3161 Timestamping Protocol?**
> A standard where a Time Stamp Authority (TSA) signs a hash of a document along with the current time, providing cryptographic proof that the document existed in a specific form at that time.

**Q10: How does Kerberos protect against replay attacks?**
> Kerberos uses timestamps in authenticators. The KDC and service servers reject messages with timestamps outside a 5-minute clock skew window, and they cache recently used authenticators to detect replays within that window.

**Q11: What is a Merkle tree in blockchain?**
> A binary tree of hashes where each leaf is a transaction hash, and each parent node is the hash of its two children. The root (Merkle root) is a single hash representing all transactions in a block. Any change to any transaction changes the Merkle root.

**Q12: What is LDAP injection?**
> Injecting malicious characters into LDAP queries via unsanitized input, manipulating the filter logic to bypass authentication or extract data. Defense: input validation and parameterized queries.

**Q13: What is the difference between a public and private blockchain?**
> Public blockchains (Bitcoin, Ethereum) are permissionless — anyone can participate. Private blockchains restrict participation to authorized entities and use identity-based consensus mechanisms like PBFT or PoA.

**Q14: What is a smart contract vulnerability? Give an example.**
> The DAO hack (2016) exploited a **reentrancy vulnerability** in an Ethereum smart contract — the attacker recursively called the withdraw function before balance was updated, draining ~$60M in ETH.

---

## Quick Revision Summary: All Sessions 8–15

```
Session 8:  CA hierarchy → Root CA signs Intermediate CA → Intermediate signs user certs
            CRL (batch revocation) vs OCSP (real-time) vs OCSP Stapling (server-cached)

Session 9:  Aadhaar e-Sign = one-time DSC via Aadhaar auth
            RFC 3161 = Timestamping Protocol (TSA signs hash + time)

Session 10: PKCS#12 = cert+key bundle | PKCS#10 = CSR | PKCS#7 = signed/encrypted data
            FIPS 140-2: Level 1 (basic) → Level 4 (full physical protection)

Session 11: MFA types: something you know/have/are/where
            TOTP = HMAC(secret, time/30) | HOTP = HMAC(secret, counter)
            OAuth = authorization | OIDC = authentication on top of OAuth

Session 12: Kerberos = TGT → Service Ticket → Access
            FIDO2 = WebAuthn + CTAP; phishing-resistant via origin binding
            Zero Trust = Never Trust, Always Verify; PEP/PA/PE

Session 13: TLS 1.3 = 1-RTT, ECDHE only, AEAD only
            PGP = Web of Trust | S/MIME = Hierarchical PKI
            Sign-then-encrypt (correct PGP order)

Session 14: IT Act Sec 66F = cyber terrorism = life imprisonment
            Blockchain = immutable distributed ledger; PoW vs PoS
            SPF (IP check) + DKIM (signature) + DMARC (alignment + policy)

Session 15: Master revision — combine all concepts for exam
```
