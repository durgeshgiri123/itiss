# Session 15: Master Revision Sheet & Mixed Mock Q&A Bank (All Sessions)

---

## MASTER QUICK-REVISION SHEET

---

### MODULE 1: Foundations (Sessions 1–3)

#### CIA Triad
| Pillar | Threat | Defense |
|--------|--------|---------|
| Confidentiality | Eavesdropping, sniffing | Encryption, access control |
| Integrity | Tampering, MitM | Hashing, digital signatures, MAC |
| Availability | DoS/DDoS | Redundancy, rate limiting, CDN |

#### Attack Types
- **Passive** = only observe (confidentiality breach) — harder to detect
- **Active** = modify/disrupt (integrity/availability breach) — easier to detect
- **Active subtypes:** Masquerade, Replay, Modification, DoS

#### Classic Ciphers
| Cipher | Type | Key Concept |
|--------|------|-------------|
| Caesar | Substitution | Shift of 3 (ROT-3) |
| Vigenère | Polyalphabetic | Keyword-based shifts; broken by Kasiski test |
| Rail Fence | Transposition | Characters written diagonally |
| Playfair | Digraph substitution | 5×5 matrix, encrypts pairs |
| Hill | Polygraphic | Matrix multiplication mod 26 |
| Vernam/OTP | XOR stream | Unbreakable IF key = message length, truly random, used once |

> **Tricky:** One-Time Pad is the ONLY theoretically unbreakable cipher (Shannon proved it).

#### Kerckhoffs's Principle
> The security of a cryptosystem must rely only on the **secrecy of the key**, NOT the secrecy of the algorithm.

#### Shannon's Properties of Good Ciphers
- **Confusion** — Obscure relationship between ciphertext and key (substitution)
- **Diffusion** — Spread influence of plaintext across ciphertext (transposition/permutation)

---

### MODULE 2: Symmetric Encryption (Session 4)

#### DES
- 64-bit block, **56-bit key** (8 bits parity), 16 rounds Feistel
- Broken: **DES Cracker (1998)** cracked in 22 hours
- **3DES (Triple DES):** EDE = Encrypt(K1) → Decrypt(K2) → Encrypt(K3)
  - 3DES key sizes: 168-bit (3 keys), 112-bit (2 keys — K1=K3)
  - Deprecated by NIST in 2023

#### AES
- Block size: **128 bits** (fixed)
- Key sizes: **128, 192, or 256 bits**
- Rounds: 10 (128-bit), 12 (192-bit), 14 (256-bit)
- Operations per round: SubBytes → ShiftRows → MixColumns → AddRoundKey
- Standard since 2001 (NIST FIPS 197)
- NOT a Feistel cipher — uses Substitution-Permutation Network (SPN)

> **Tricky Q:** AES block size is always 128 bits regardless of key size.

#### Block Cipher Modes
| Mode | IV Needed | Parallel | Error Propagation | Use Case |
|------|-----------|----------|-------------------|---------|
| ECB | No | Yes | None | Avoid (reveals patterns) |
| CBC | Yes | Decrypt only | Yes (2 blocks) | General purpose |
| CFB | Yes | Decrypt only | Yes | Stream-like |
| OFB | Yes | No | No | Error-tolerant streams |
| CTR | Yes (nonce) | Yes | No (1 bit) | High performance |
| GCM | Yes (nonce) | Yes | No | AEAD — TLS 1.3 |

> **Tricky:** ECB mode of AES on an image shows the original pattern — never use ECB for structured data.

#### RC5 / RC6
- **RC5:** Variable block size (32/64/128), variable key (0–2040 bits), variable rounds. Uses data-dependent rotations.
- **RC6:** AES finalist; extends RC5 with integer multiplication

---

### MODULE 3: Asymmetric Encryption (Session 4)

#### RSA
- Based on **integer factorization** problem
- Key generation: p, q primes → n=p×q → φ(n)=(p-1)(q-1) → e coprime to φ(n) → d = e⁻¹ mod φ(n)
- Encrypt: C = M^e mod n | Decrypt: M = C^d mod n
- Sign: S = H(M)^d mod n | Verify: H(M) = S^e mod n
- Key sizes: 2048 (minimum), 3072, 4096 bits

#### ECC — Elliptic Curve Cryptography
- Based on **elliptic curve discrete logarithm problem (ECDLP)**
- Smaller keys for same security: **256-bit ECC ≈ 3072-bit RSA**
- Curves: P-256 (prime256v1), P-384, P-521, Curve25519 (X25519), Ed25519
- Used in: TLS, Bitcoin (secp256k1), FIDO2, SSH

#### Key Size Comparison
| Security Level | Symmetric | RSA/DH | ECC |
|---------------|-----------|--------|-----|
| 80-bit | 80 | 1024 | 160 |
| 112-bit | 112 | 2048 | 224 |
| 128-bit | 128 | 3072 | 256 |
| 192-bit | 192 | 7680 | 384 |
| 256-bit | 256 | 15360 | 521 |

---

### MODULE 4: Diffie-Hellman & Attacks (Session 5)

#### Diffie-Hellman Key Exchange
```
Public: p (large prime), g (generator/primitive root of p)
Alice: private a → public A = g^a mod p
Bob:   private b → public B = g^b mod p
Shared secret: K = B^a mod p = A^b mod p = g^(ab) mod p
```
- **DH is NOT for encryption** — only key agreement
- Vulnerable to: **MitM attack** (no authentication)
- **ECDH** = DH on elliptic curves (more efficient)
- **ECDHE** = Ephemeral ECDH (provides Forward Secrecy)

#### Cryptographic Attacks
| Attack | Description |
|--------|-------------|
| **Brute Force** | Exhaustive key search |
| **Dictionary** | List of probable keys/passwords |
| **Known Plaintext (KPA)** | Attacker has some plaintext+ciphertext pairs |
| **Chosen Plaintext (CPA)** | Attacker can choose plaintexts to encrypt |
| **Chosen Ciphertext (CCA)** | Attacker can choose ciphertexts to decrypt |
| **Birthday Attack** | Find hash collision using birthday paradox |
| **Meet-in-the-Middle** | Attack on double encryption (why 2DES is weak) |
| **Timing Attack** | Measure time differences to infer key bits |
| **Differential Cryptanalysis** | Analyze difference propagation through cipher |
| **Linear Cryptanalysis** | Approximate cipher with linear equations |
| **Side-Channel** | Power, EM, timing, acoustic analysis |

> **Tricky:** Birthday paradox: ~50% chance of collision in √N attempts. For SHA-256 (2^256 outputs), expect collision after ~2^128 attempts.

---

### MODULE 5: Hashing (Session 6)

#### Hash Functions
| Algorithm | Output Size | Status |
|-----------|-------------|--------|
| MD5 | 128 bits | Broken (collisions found) |
| SHA-1 | 160 bits | Deprecated (SHAttered 2017) |
| SHA-256 | 256 bits | Secure — widely used |
| SHA-384 | 384 bits | Secure |
| SHA-512 | 512 bits | Secure |
| SHA-3 (Keccak) | Variable | Secure — different design from SHA-2 |
| BLAKE2/BLAKE3 | Variable | Faster than SHA-2; secure |

#### Properties of Cryptographic Hash Functions
1. **Pre-image resistance** — Given H, can't find M such that H(M) = H (one-way)
2. **Second pre-image resistance** — Given M, can't find M' ≠ M with same hash
3. **Collision resistance** — Can't find ANY M, M' with same hash

#### HMAC — Hash-based Message Authentication Code
```
HMAC(K, M) = H((K ⊕ opad) || H((K ⊕ ipad) || M))
```
- Provides: **integrity + authentication** (NOT confidentiality)
- Key is mixed in — can't compute without key
- HMAC-SHA256 = most common
- Used in: TLS MAC, JWT signatures, API authentication

> **Tricky:** HMAC provides authentication AND integrity but NOT confidentiality. To add confidentiality, encrypt the HMAC-protected message.

#### MAC vs Digital Signature
| Feature | MAC (HMAC) | Digital Signature |
|---------|-----------|-------------------|
| Key type | Symmetric (shared) | Asymmetric |
| Non-repudiation | No | Yes |
| Speed | Fast | Slower |
| Use case | TLS record layer | Certificates, code signing |

---

### MODULE 6: PKI (Sessions 7–10)

#### Certificate Hierarchy
```
Root CA (self-signed, offline, trust anchor)
  └── Intermediate CA (online, issues end-entity certs)
        └── End-Entity Certificate (server, user, code signing)
```

#### X.509 Certificate Fields
- Version (v3), Serial Number, Signature Algorithm
- Issuer DN, Subject DN
- Validity (Not Before, Not After)
- Subject Public Key Info
- Extensions: SAN, Key Usage, Extended Key Usage, Basic Constraints, CDP, AIA

#### Certificate Extensions
| Extension | Purpose |
|-----------|---------|
| **SAN** | Subject Alternative Names (domains, IPs, emails) |
| **Key Usage** | digitalSignature, keyEncipherment, keyCertSign |
| **Extended Key Usage** | serverAuth, clientAuth, codeSigning, emailProtection |
| **Basic Constraints** | CA=TRUE for CA certs; path length constraint |
| **CDP** | CRL Distribution Points |
| **AIA** | Authority Info Access (OCSP URL, issuer cert URL) |

#### Revocation
| Method | Description | Drawback |
|--------|-------------|---------|
| **CRL** | Periodic list of revoked serials | Large, slow to update |
| **OCSP** | Real-time check per certificate | Privacy concern; CA load |
| **OCSP Stapling** | Server caches OCSP response; attaches to TLS handshake | Best of both worlds |
| **CRLite** | Compressed CRL bloom filter in browser | Firefox implementation |

---

### MODULE 7: Authentication (Session 11)

#### Authentication Factors
| Factor | Type | Examples |
|--------|------|---------|
| Something you **know** | Knowledge | Password, PIN, security questions |
| Something you **have** | Possession | OTP token, smart card, phone |
| Something you **are** | Inherence | Fingerprint, iris, face |
| Somewhere you **are** | Location | GPS, IP geolocation |

#### OTP Types
| Type | Algorithm | Standard |
|------|-----------|---------|
| **HOTP** | HMAC-SHA1(secret, counter) | RFC 4226 |
| **TOTP** | HMAC-SHA1(secret, time÷30) | RFC 6238 |

- TOTP window: ±1 step (30 sec) tolerance
- Google Authenticator, Microsoft Authenticator use TOTP

#### SSO Protocols
| Protocol | Mechanism |
|----------|-----------|
| **SAML 2.0** | XML assertions between IdP and SP |
| **OAuth 2.0** | Delegated authorization; access tokens |
| **OpenID Connect** | Identity layer on OAuth 2.0; ID tokens (JWT) |
| **Kerberos** | Ticket-based; Windows AD |

---

### MODULE 8: Advanced (Sessions 12–15)

#### TLS 1.3 Must-Know Facts
- 1-RTT handshake (0-RTT for resumption)
- Key exchange: **ECDHE only** (no RSA key transport)
- Cipher suites: **AEAD only** (AES-GCM, ChaCha20-Poly1305)
- Perfect Forward Secrecy: **mandatory**
- Removed: RSA key exchange, static DH, RC4, 3DES, SHA-1/MD5 in ciphers

#### FIDO2 Core Points
- WebAuthn (W3C) + CTAP (FIDO Alliance)
- Phishing-resistant: keys bound to origin (Relying Party ID)
- No password transmitted or stored at server
- Passkeys = synced FIDO2 credentials across devices

#### Zero Trust Core
- Never Trust, Always Verify (NTAV)
- Components: PE (Policy Engine) + PA (Policy Administrator) + PEP (Policy Enforcement Point)
- Pillars: Identity, Device, Network, Application, Data, Infrastructure

#### Blockchain Key Facts
- Immutable ledger via chained block hashes
- Merkle tree = efficient transaction verification
- Bitcoin: PoW + SHA-256 + ECDSA (secp256k1)
- 51% attack = double spend risk
- Smart contract bugs: reentrancy (DAO hack 2016)

#### Email Security (SPF + DKIM + DMARC)
```
SPF  → validates sending server IP (DNS TXT)
DKIM → validates message integrity (cryptographic signature)
DMARC → alignment policy + reporting (none/quarantine/reject)
```

---

## MIXED MOCK Q&A BANK (100 Questions)

### Section A: True / False with Explanation

1. **T/F: SHA-256 is a symmetric encryption algorithm.**
   > **FALSE.** SHA-256 is a hash function — it produces a fixed-size digest, not encryption. It's one-way and cannot be reversed.

2. **T/F: AES uses a 64-bit block size.**
   > **FALSE.** AES always uses a **128-bit block size**, regardless of key size (128/192/256 bits).

3. **T/F: DES is still recommended for modern secure communications.**
   > **FALSE.** DES uses a 56-bit key and was cracked in 1998. It's deprecated and insecure.

4. **T/F: The One-Time Pad is theoretically unbreakable.**
   > **TRUE.** OTP provides perfect secrecy if the key is truly random, at least as long as the message, and used only once (Shannon's proof).

5. **T/F: TLS 1.3 supports RSA key exchange.**
   > **FALSE.** TLS 1.3 removed RSA key exchange. Only ECDHE (and DHE) are supported for key agreement.

6. **T/F: HMAC provides both confidentiality and integrity.**
   > **FALSE.** HMAC provides **integrity + authentication** only. For confidentiality, data must be encrypted.

7. **T/F: OCSP stapling improves user privacy.**
   > **TRUE.** The CA never sees which certificate is being verified since the server handles the OCSP check.

8. **T/F: A digital signature provides non-repudiation.**
   > **TRUE.** Only the private key holder can create the signature, preventing denial.

9. **T/F: Kerberos requires synchronized clocks.**
   > **TRUE.** Timestamps prevent replay attacks; clocks must be within 5 minutes (default).

10. **T/F: FIDO2 can be phished like passwords.**
    > **FALSE.** Keys are bound to the origin (RP ID). A phishing site with a different domain cannot trigger the legitimate key.

---

### Section B: Multiple Choice (Select the BEST answer)

**11. Which cipher mode provides authenticated encryption?**
a) CBC  b) ECB  c) **GCM**  d) CTR
> **c) GCM** — Galois/Counter Mode provides AEAD (Authenticated Encryption with Associated Data).

**12. What does the "E" in ECDHE stand for?**
a) Encrypted  b) Extended  c) **Ephemeral**  d) Enhanced
> **c) Ephemeral** — temporary key pairs used per session, enabling forward secrecy.

**13. Which section of the IT Act covers cyber terrorism?**
a) 66C  b) 66D  c) 69  d) **66F**
> **d) 66F** — Cyber terrorism, punishable by up to life imprisonment.

**14. A hash collision attack on SHA-256 requires approximately how many operations?**
a) 2^256  b) **2^128**  c) 2^64  d) 2^32
> **b) 2^128** — Birthday paradox means collisions are found in √(2^256) = 2^128 operations.

**15. Which Kerberos ticket is issued first?**
a) Service Ticket  b) **TGT (Ticket Granting Ticket)**  c) Session Key  d) Master Key
> **b) TGT** — The AS issues a TGT, which is used to request Service Tickets from the TGS.

**16. Which consensus mechanism does Ethereum currently use?**
a) Proof of Work  b) **Proof of Stake**  c) PBFT  d) Proof of Authority
> **b) Proof of Stake** — Ethereum switched from PoW to PoS in September 2022 ("The Merge").

**17. PKCS#12 file extension is:**
a) .pem  b) .csr  c) .crt  d) **.pfx or .p12**
> **d) .pfx or .p12** — PKCS#12 bundles private key + certificate.

**18. Which attack exploits the birthday paradox?**
a) Brute force  b) Rainbow table  c) **Collision attack**  d) Replay attack
> **c) Collision attack** — finds two different inputs with the same hash output.

**19. What is the correct order in PGP for sign-then-encrypt?**
a) Encrypt message → sign ciphertext
b) **Sign message → encrypt signed message**
c) Hash → sign hash → encrypt hash
d) Sign → compress → encrypt
> **b)** Sign first (covers plaintext), then encrypt. This ensures the signature is over meaningful content.

**20. Which Zero Trust component makes the access decision?**
a) PEP  b) PA  c) **PE (Policy Engine)**  d) IAM
> **c) Policy Engine** — evaluates requests and makes allow/deny decisions.

---

### Section C: Short Answer (Exam-Style)

**21. What is the difference between stream cipher and block cipher?**
> **Block cipher** encrypts fixed-size blocks (e.g., 128 bits for AES) using a key. **Stream cipher** encrypts one bit/byte at a time using a keystream (e.g., RC4, ChaCha20). Stream ciphers are faster and suit real-time data; block ciphers are more structured.

**22. Explain the Meet-in-the-Middle attack. Why does 2DES fail?**
> For 2DES (encrypt twice with two keys), the attacker encrypts all possible K1 values from the plaintext side and decrypts all possible K2 values from the ciphertext side, then finds matching values in the middle. This reduces 2DES security from 2^112 to ~2^57 operations — almost as weak as DES.

**23. What is a Merkle tree and what is its purpose in blockchain?**
> A binary tree where each leaf is a transaction hash and each parent is the hash of its children. The Merkle root summarizes all transactions in one hash. It enables efficient transaction verification without downloading the full block (SPV), and any tampered transaction changes the Merkle root, invalidating the block.

**24. What is the purpose of the nonce in AES-GCM?**
> The nonce (Number Used Once) initializes the counter for CTR-mode encryption in GCM. It must be unique per key per message. Reusing a nonce with the same key in GCM is catastrophic — it leaks the authentication key and reveals plaintext XOR.

**25. Explain the difference between authentication and non-repudiation.**
> Authentication verifies identity at the time of communication. Non-repudiation prevents a party from denying they sent/received a message. Digital signatures provide both: the signer's private key proves identity, and only they have that key, so they cannot deny signing.

**26. What is HSTS and what attack does it prevent?**
> HTTP Strict Transport Security is a response header (`Strict-Transport-Security: max-age=31536000`) that instructs browsers to only connect over HTTPS for the specified duration. It prevents SSL stripping attacks where an attacker downgrades HTTPS to HTTP.

**27. What is a Golden Ticket attack in Kerberos?**
> The attacker compromises a Domain Controller and steals the KRBTGT account's password hash. Using this hash, they can forge any TGT for any user with any group membership and any expiry — granting persistent domain admin access even after user password changes.

**28. How does ECC achieve the same security as RSA with smaller keys?**
> ECC relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), which is computationally harder than integer factorization (RSA). Solving ECDLP requires exponential effort, while factorization has sub-exponential algorithms (GNFS). Result: 256-bit ECC ≈ 3072-bit RSA in security.

**29. What is PKCS#5 / PBKDF2?**
> PBKDF2 (Password-Based Key Derivation Function 2) derives a cryptographic key from a password by applying HMAC repeatedly (many iterations) with a salt. The high iteration count makes brute-force attacks expensive. Used in: WPA2, LUKS disk encryption, password storage.

**30. What is DMARC alignment?**
> DMARC requires that the domain in the From: header aligns with either the SPF-authenticated domain (envelope from) or the DKIM-signed domain. This prevents attackers from using SPF/DKIM passing domains in the envelope while spoofing the visible From address.

---

### Section D: Scenario-Based Questions

**31. A user receives an email from `ceo@company.com` asking for urgent wire transfer. How do you verify it's genuine?**
> Check DMARC/DKIM/SPF pass status in email headers. Verify the digital signature if S/MIME is enabled. Call the CEO directly using a known number. Look for display name spoofing (visible name ≠ actual email). This is likely a BEC (Business Email Compromise) attack.

**32. Your company's TLS certificate expired. Users see a browser warning. What do you check?**
> Renew the certificate from the CA. Check `openssl x509 -in cert.crt -noout -dates`. Also check intermediate CA cert expiry. Ensure auto-renewal is configured (e.g., Let's Encrypt with certbot). Verify the certificate chain is complete.

**33. An attacker captured HTTPS traffic 2 years ago. Today they stole the server's private key. Can they decrypt old traffic?**
> **Only if the cipher suite lacked forward secrecy.** If the server used RSA key exchange (no DHE/ECDHE), yes. If ECDHE was used (forward secrecy), no — session keys were ephemeral and not derivable from the long-term private key.

**34. A blockchain shows a suspicious transaction that happened 3 blocks ago. Can it be reversed?**
> Not practically. Reversing a confirmed transaction requires re-mining that block and all subsequent blocks faster than the honest network (51% attack). In Bitcoin, after 6 confirmations, reversal is computationally infeasible.

**35. You discover a server running TLS 1.0. What vulnerabilities does it face and how do you fix it?**
> TLS 1.0 is vulnerable to BEAST (CBC mode attack), POODLE (if SSL 3.0 fallback allowed), and uses weak MAC. Fix: disable TLS 1.0 and 1.1 in server config; enable only TLS 1.2 and 1.3 with strong cipher suites (ECDHE + AES-GCM).

---

### Section E: Fill in the Blanks (Key Facts)

36. AES has _____ rounds for a 256-bit key. → **14**
37. DES key size is _____ bits (effective). → **56**
38. The output of SHA-256 is _____ bits. → **256**
39. Kerberos default clock skew tolerance is _____ minutes. → **5**
40. TLS 1.3 handshake requires _____ round trips. → **1** (0-RTT for resumption)
41. FIPS 140-2 has _____ security levels. → **4**
42. PKCS#12 files use the extension _____ or _____. → **.pfx / .p12**
43. The Zero Trust principle is "_____ Trust, _____ Verify." → **Never / Always**
44. OCSP stapling attaches the OCSP response to the _____. → **TLS handshake**
45. Birthday attack on SHA-256 requires ~2^_____ operations. → **128**
46. 256-bit ECC is equivalent in security to _____ -bit RSA. → **3072**
47. The IT Act section for cyber terrorism is _____. → **66F**
48. DMARC policies are: none, _____, _____. → **quarantine, reject**
49. Bitcoin uses _____ curve for ECDSA. → **secp256k1**
50. Merkle root in blockchain summarizes all _____. → **transactions in a block**

---

### Section F: Compare & Contrast (Important Pairs)

| Pair | Key Differences |
|------|----------------|
| **Symmetric vs Asymmetric** | Same key vs key pair; fast vs slow; AES vs RSA |
| **Hash vs MAC** | No key vs keyed; integrity only vs integrity+auth |
| **MAC vs Digital Signature** | Symmetric vs asymmetric; no non-repudiation vs non-repudiation |
| **DES vs AES** | 56-bit/64-bit block/16 rounds vs 128-256-bit/128-bit block/10-14 rounds |
| **RSA vs ECC** | Factorization vs ECDLP; larger keys vs smaller keys |
| **CRL vs OCSP** | Batch download vs real-time; slow vs fast; privacy-friendly vs not |
| **PGP vs S/MIME** | Web of Trust vs CA hierarchy; RFC 4880 vs RFC 5751 |
| **TLS 1.2 vs TLS 1.3** | 2-RTT vs 1-RTT; optional PFS vs mandatory PFS; RSA allowed vs removed |
| **DoS vs DDoS** | Single source vs distributed botnet |
| **RADIUS vs TACACS+** | UDP+password-encrypt vs TCP+full-encrypt; combined vs separated AAA |

---

### Section G: Definitions Rapid Fire

| Term | One-Line Definition |
|------|---------------------|
| **Nonce** | Number used once; prevents replay attacks |
| **Salt** | Random data added to password before hashing; prevents rainbow table attacks |
| **IV (Initialization Vector)** | Random value to ensure same plaintext → different ciphertext each time |
| **Key Escrow** | Copies of keys held by trusted third party for lawful access |
| **HSM** | Hardware Security Module — tamper-resistant device for key storage/crypto ops |
| **MITM** | Man-in-the-Middle — attacker intercepts and possibly alters communication |
| **Zero-Day** | Vulnerability unknown to vendor; no patch available |
| **Ephemeral Key** | Temporary key generated per-session; discarded after use; provides PFS |
| **Certificate Pinning** | App hardcodes expected cert/public key; rejects others |
| **Trust Anchor** | Root CA certificate that is implicitly trusted |
| **CPS** | Certification Practice Statement — CA's document explaining how it issues certs |
| **OCSP Stapling** | Server caches and serves its own OCSP response in TLS handshake |
| **Replay Attack** | Retransmitting valid captured data to gain unauthorized access |
| **Differential Privacy** | Adding statistical noise to protect individual data in aggregate queries |
| **AAA** | Authentication, Authorization, Accounting |

---

### Section H: Quick Mnemonics & Memory Tricks

```
CIA = Confidentiality, Integrity, Availability
AAA = Authentication, Authorization, Accounting
DAD = Disclosure, Alteration, Destruction (attacks on CIA)

AES rounds: 10-12-14 for 128-192-256 (add 2 for each key size jump)

SHA sizes: MD5=128, SHA1=160, SHA256=256, SHA384=384, SHA512=512

Kerberos: AS gives TGT → TGS gives ST → SS gives access
         "Always Thinking; Thinking Seriously; So Successful"

TLS 1.3 removed: "RSA DH RC4 3DES SHA1 MD5" = "Really Dumb Rotten Ciphers Should March"

FIDO2 = WebAuthn (W3C) + CTAP (FIDO Alliance)
       "Web-based Auth + Client-To-Authenticator Protocol"

Zero Trust pillars: I-D-N-A-D-I
Identity, Device, Network, Application, Data, Infrastructure

Email auth: SPF (server IP) → DKIM (sign) → DMARC (align + policy)
           "Some Ducks Migrate" = SPF, DKIM, DMARC

PKCS important ones:
  #7 = CMS/S-MIME (Seven letters in S/MIME → 7)
  #10 = CSR (10 letters in "certificate")
  #11 = Cryptoki/HSM (11 → hardware)
  #12 = PFX bundle (12 = dozen = complete package)

Blockchain consensus:
  PoW = Work (Bitcoin), PoS = Stake (Ethereum), PBFT = Byzantine (Hyperledger)
```

---

### Section I: Important Numbers to Remember

| Number | Significance |
|--------|-------------|
| **56** | DES key size (bits) |
| **128** | AES block size (bits) / minimum symmetric security bits |
| **128/192/256** | AES key sizes |
| **10/12/14** | AES rounds (for 128/192/256-bit keys) |
| **160** | SHA-1 output / RSA minimum deprecated size |
| **256** | SHA-256 output / minimum ECC key for good security |
| **2048** | Minimum RSA key size (current recommendation) |
| **3072** | RSA equivalent to 256-bit ECC |
| **5** | Kerberos clock skew tolerance (minutes) |
| **1-RTT** | TLS 1.3 handshake round trips |
| **2-RTT** | TLS 1.2 handshake round trips |
| **389/636** | LDAP / LDAPS ports |
| **443** | HTTPS port |
| **1812/1813** | RADIUS auth/accounting ports |
| **49** | TACACS+ port |
| **6 hours** | CERT-In incident reporting window |
| **30 seconds** | TOTP time step |
| **4** | FIPS 140-2 security levels |
| **6** | Bitcoin confirmations for finality |
| **51%** | Threshold for blockchain majority attack |

---

*End of Master Revision Sheet — Good luck in your competition exams and interviews!*
