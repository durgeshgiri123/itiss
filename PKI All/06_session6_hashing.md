# Session 6: Secure Hashing Methods — SHA & HMAC

## 1. What is Hashing?

A **hash function** takes an input (message) of any length and produces a **fixed-size output** (called a hash, digest, or message digest). Unlike encryption, hashing is a **one-way function** — there is no "decryption" to recover the original input.

### Properties of a Cryptographically Secure Hash Function

| Property | Meaning |
|---|---|
| **Pre-image resistance** | Given a hash H, it should be infeasible to find any message M such that hash(M) = H |
| **Second pre-image resistance** | Given M1, it should be infeasible to find a different M2 such that hash(M1) = hash(M2) |
| **Collision resistance** | It should be infeasible to find ANY two different messages M1, M2 such that hash(M1) = hash(M2) |
| **Avalanche effect** | A tiny change in input produces a drastically different, unpredictable hash output |
| **Deterministic** | Same input always produces the same output |
| **Fixed output length** | Regardless of input size (1 byte or 1 GB), output is always the same fixed length |

> **Tricky distinction often confused in exams**: Pre-image resistance vs collision resistance. Pre-image resistance is about reversing a *specific* hash back to *any* matching input. Collision resistance is about finding *any two* inputs (doesn't matter which) that happen to hash to the same value. Collision resistance is actually a **stronger/harder** requirement, and many older hash function breaks (like MD5, SHA-1) were collision attacks, not pre-image attacks.

## 2. SHA (Secure Hash Algorithm) Family

| Version | Output Size | Status |
|---|---|---|
| **SHA-0** | 160-bit | Withdrawn shortly after publication (flaw found) |
| **SHA-1** | 160-bit | **Broken** — practical collision demonstrated by Google ("SHAttered" attack, 2017); deprecated |
| **SHA-2** | 224/256/384/512-bit (SHA-224, SHA-256, SHA-384, SHA-512) | Currently widely used and considered secure |
| **SHA-3** | 224/256/384/512-bit (variable, via Keccak sponge construction) | Newer standard (2015), structurally different from SHA-2, used as a backup/alternative |

### How SHA Works (General/Simplified, Merkle–Damgård construction used by SHA-1/SHA-2)
1. **Padding**: The message is padded so its length is a multiple of the block size (e.g., 512 bits for SHA-256), including appending the original message length.
2. **Initialization**: Fixed initial hash values (constants) are set.
3. **Processing in blocks**: The message is divided into blocks; each block is processed through a compression function combining the block with the current hash state, producing a new state.
4. **Output**: After all blocks are processed, the final state is the hash digest.

> **SHA-3's structural difference (important interview point)**: SHA-3 is based on the **Keccak sponge construction**, which is fundamentally different from the Merkle-Damgård structure used in SHA-1/SHA-2. This was a deliberate design choice by NIST — having a structurally different "backup" algorithm means that if a future mathematical breakthrough breaks Merkle-Damgård-based hashes (SHA-1/SHA-2), SHA-3 wouldn't necessarily be vulnerable to the same attack.

## 3. Why Hashing Alone Is Not Enough for Integrity Against an Active Attacker

If Alice sends a message M and its hash H(M) to Bob over an insecure channel, an attacker can intercept, modify M to M', recompute H(M'), and send both — Bob has no way to detect tampering because he doesn't have a secret to verify against. **Plain hashing only protects against *accidental* corruption (e.g., checksums for file integrity during download), not deliberate tampering.** This is exactly the gap that HMAC fills.

## 4. HMAC (Hash-based Message Authentication Code)

HMAC combines a **hash function** with a **secret key** to provide both integrity AND authenticity (proof the message came from someone who knows the secret key) — something plain hashing cannot do.

### HMAC Formula (commonly asked to state in exams)
```
HMAC(K, M) = H( (K' ⊕ opad) || H( (K' ⊕ ipad) || M ) )
```
Where:
- H = the underlying hash function (e.g., SHA-256)
- K' = the secret key, padded/adjusted to the block size
- ipad, opad = fixed, distinct padding constants ("inner pad," "outer pad")
- || = concatenation, ⊕ = XOR

### Why HMAC uses the key in **two nested hash operations** (not just one)
A naive approach like `H(K || M)` is vulnerable to **length-extension attacks** on Merkle-Damgård hash functions (like SHA-256) — an attacker who knows H(K||M) can sometimes compute H(K||M||X) for an attacker-chosen extension X, **without knowing K**, due to how the internal state of Merkle-Damgård hashes works. HMAC's nested construction (hash-of-hash, with two differently-padded versions of the key) is specifically designed to defeat this class of attack.

### HMAC vs Plain Hash vs Digital Signature (heavily tested comparison)

| Feature | Plain Hash | HMAC | Digital Signature |
|---|---|---|---|
| **Integrity check** | Yes (against accidental change) | Yes (against deliberate tampering) | Yes |
| **Authentication (origin proof)** | No | Yes (proves sender knows shared secret key) | Yes (proves sender holds private key) |
| **Non-repudiation** | No | **No** (shared secret means either party could have created it — can't prove *which* one) | **Yes** (only the private key holder could have signed) |
| **Key type** | None | Symmetric (shared secret) | Asymmetric (private/public key pair) |

> **This is one of THE most-asked tricky questions in PKI exams: "Why doesn't HMAC provide non-repudiation, but digital signatures do?"**
> Because HMAC uses a **shared secret key known to both sender and receiver** — if a dispute arises, the receiver cannot prove to a third party that they themselves didn't generate the HMAC, since they also possess the same key. Digital signatures use the sender's **private key, known only to the sender** — so anyone with the corresponding public key can verify it could only have been created by that specific private key holder, giving non-repudiation.

## 5. Practical Uses of Hashing & HMAC

- **Password storage**: storing `hash(password + salt)` instead of plaintext passwords (see self-learning section below).
- **File integrity verification**: checksums published alongside downloadable software (e.g., SHA-256 checksums on Linux ISO download pages).
- **Digital signatures**: you don't sign the entire message directly (too slow with asymmetric crypto) — you **hash the message first, then sign the hash** (covered in detail in Session 7).
- **HMAC in protocols**: TLS record integrity (older cipher suites), API request signing (e.g., AWS Signature V4), JWT (JSON Web Token) signing (HS256 algorithm).
- **Blockchain**: hash chains link blocks together (tie-in to Session 14/15's Blockchain topic).

---

## Tricky / Conceptual Questions

**Q1. Why can't you "decrypt" a hash to get back the original message?**
Hashing is a **many-to-one** mapping by design — the fixed-size output space is much smaller than the (potentially infinite) input space, so information is lost during hashing (unlike encryption, which is reversible/bijective). This is fundamentally different from encryption and is precisely why hashing is used for verification, not for confidentiality.

**Q2. If SHA-256 produces a 256-bit output, doesn't that mean there are only 2^256 possible hashes, and therefore infinite collisions must exist (pigeonhole principle)?**
Yes — mathematically, since the input space is infinite (or far larger than 2^256) and the output space is fixed at 2^256, collisions **must** exist by the pigeonhole principle. "Collision resistant" doesn't mean "no collisions exist" — it means it's **computationally infeasible to find one** within a practical timeframe, even though they mathematically must exist somewhere.

**Q3. Why was SHA-1 deprecated even though no pre-image attack was found?**
Because SHA-1 was broken via a practical **collision attack** (Google/CWI's "SHAttered" in 2017, producing two different PDFs with the same SHA-1 hash) — and many real-world security applications (like digital signatures, Git commit integrity in some configurations) rely specifically on collision resistance, not just pre-image resistance, so a collision break is enough to deprecate it for security-critical uses.

**Q4. Why does HMAC specifically need the key processed through TWO nested hash applications instead of just prepending the key once?**
As explained above — to defeat **length-extension attacks**. Many hash functions (Merkle-Damgård based, like SHA-256) leak their entire internal state as the final output, which means an attacker could exploit `H(K||M)` to forge `H(K||M||extra_data)` without knowing K. HMAC's double-hashing with different pad constants (ipad/opad) breaks this exploitable property.

**Q5. Is HMAC-SHA256 "more secure" than plain SHA-256 in terms of collision resistance?**
This is a trick — they serve **different purposes**, not a security ranking on the same axis. Plain SHA-256 has no secret key, so anyone can compute and verify it (useful for public integrity checks, like download checksums) but offers zero authentication. HMAC-SHA256 requires a secret key, so only parties who know the key can compute/verify it (useful for authenticated integrity) — it's "more secure" specifically *for authentication purposes*, not "more collision-resistant" as a hash.

---

## Interview Questions & Model Answers

**Q: Explain the difference between encryption and hashing in one sentence each.**
> Encryption is a reversible transformation designed to keep data confidential — anyone with the right key can recover the original. Hashing is a one-way transformation designed to verify integrity or create a fixed-size fingerprint — there's no key to "undo" it, and recovering the original is intentionally infeasible.

**Q: Why do we hash a message before signing it, rather than signing the message directly?**
> Asymmetric encryption operations (used in digital signatures) are computationally expensive and have message-size limitations relative to key size. Hashing first compresses an arbitrarily large message into a small, fixed-size digest, which is then signed — making the signature operation fast regardless of the original message's size, while the hash's collision resistance ensures the signature still effectively "covers" the whole original message.

**Q: A junior developer stores user passwords as `SHA256(password)` in the database. What's wrong with this, and how would you fix it?**
> Plain SHA-256 of a password is vulnerable to **rainbow table attacks** — precomputed tables mapping common passwords to their hashes, allowing instant reversal of unsalted hashes, especially for common/weak passwords. The fix: add a unique, random **salt** per user before hashing (`SHA256(password + salt)`), and ideally use a purpose-built password hashing algorithm like **bcrypt, scrypt, or Argon2**, which are deliberately slow and memory-intensive to resist brute-force/GPU-based cracking — unlike SHA-256, which is designed to be *fast* (a property that actually helps attackers when used for passwords).

**Q: What's the role of HMAC in an API authentication scheme (e.g., AWS Signature V4 or a webhook signature)?**
> HMAC lets the API provider and the calling client share a secret key in advance. When sending a request, the client computes an HMAC over the request data using the shared secret, and sends it alongside the request. The server recomputes the HMAC independently and compares — if they match, it proves the request truly came from someone with the correct secret key and wasn't tampered with in transit, all without needing the overhead of full asymmetric digital signatures.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| Hash function | One-way, fixed-output, deterministic |
| Pre-image resistance | Can't reverse a hash to find ANY matching input |
| Collision resistance | Can't find ANY two inputs with the same hash (stronger property) |
| SHA-1 | Broken (collision attack, 2017) — deprecated |
| SHA-2 (256/512) | Currently standard and secure |
| SHA-3 | Structurally different (Keccak sponge) — backup standard |
| Plain hash | Integrity only, NO authentication |
| HMAC | Integrity + Authentication (via shared secret key) |
| HMAC NOT non-repudiation | Both parties share the same secret — can't prove who created it |
| Digital signature | Integrity + Authentication + Non-repudiation (via private key) |
| Length-extension attack | Defeated by HMAC's nested double-hash construction |
| Password storage best practice | Salted hash + slow algorithm (bcrypt/scrypt/Argon2), NOT plain SHA-256 |
