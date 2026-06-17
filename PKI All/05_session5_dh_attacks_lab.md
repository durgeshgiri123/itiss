# Session 5: Diffie-Hellman Key Exchange, Attacks Against Encryption, Cryptographic Issues + CrypTool Lab

## 1. Diffie-Hellman (DH) Key Exchange

DH allows two parties to establish a **shared secret key over an insecure channel**, without ever transmitting the key itself. Published by Whitfield Diffie and Martin Hellman in 1976.

### How it works (step-by-step — commonly asked to derive in exams)

1. Both parties publicly agree on a large prime **p** and a base/generator **g** (these can be public, no secrecy needed).
2. **Alice** picks a private random number **a**, computes **A = g^a mod p**, sends A to Bob.
3. **Bob** picks a private random number **b**, computes **B = g^b mod p**, sends B to Alice.
4. **Alice** computes shared secret: **K = B^a mod p**
5. **Bob** computes shared secret: **K = A^b mod p**
6. Both arrive at the **same K** = g^(ab) mod p, without either ever having transmitted a or b.

**Security basis**: The **Discrete Logarithm Problem (DLP)** — given g, p, and A = g^a mod p, it's computationally infeasible to find a, even though computing A from a is easy.

### Worked numeric example (common exam-style problem)
- p = 23, g = 5
- Alice picks a = 6 → A = 5^6 mod 23 = 8
- Bob picks b = 15 → B = 5^15 mod 23 = 19
- Alice computes K = 19^6 mod 23 = 2
- Bob computes K = 8^15 mod 23 = 2
- Shared secret K = 2 ✓ (matches — practice this style of calculation for exams)

### Critical Weakness: Man-in-the-Middle (MITM) Attack
Plain Diffie-Hellman provides **no authentication** of the parties involved. An attacker (Mallory) can intercept and perform DH separately with both Alice and Bob, establishing two different shared secrets (one with each), relaying/modifying messages while both think they're talking directly to each other.

> **This is the single most tested weakness of DH.** The fix is combining DH with **authentication** — e.g., digital signatures or certificates (this is exactly what TLS does — DH for key exchange + certificates for authentication, tying directly into PKI's purpose).

## 2. Attacks Against Encryption (Cryptanalysis Types)

| Attack Type | Attacker Has | Goal |
|---|---|---|
| **Ciphertext-only attack** | Only ciphertext | Recover plaintext/key using statistical analysis (weakest attacker position) |
| **Known-plaintext attack** | Some plaintext-ciphertext pairs | Deduce the key or decrypt other ciphertext |
| **Chosen-plaintext attack** | Can choose plaintext, see resulting ciphertext | Deduce key (e.g., exploiting an encryption oracle) |
| **Chosen-ciphertext attack** | Can choose ciphertext, see resulting plaintext (via decryption oracle) | Deduce key — strongest attacker model |
| **Brute-force attack** | Tries every possible key | Works eventually, defeated by sufficiently large key space |
| **Man-in-the-Middle (MITM)** | Intercepts communication between two parties | Eavesdrop or alter without detection |
| **Replay attack** | Captures valid data | Resends it later to produce unauthorized effect |
| **Side-channel attack** | Observes physical implementation (timing, power consumption, electromagnetic leaks) | Extract key without breaking the math |
| **Birthday attack** | Exploits probability (birthday paradox) | Find hash collisions faster than brute force |
| **Meet-in-the-middle attack** | Works from both ends toward the middle | Reduces effective security of multiple-encryption schemes (e.g., reduces 2DES security) |

> **Side-channel attacks are an important interview talking point** — they remind us that even mathematically perfect algorithms can be broken through **implementation flaws** (e.g., measuring how long a smart card takes to compute RSA can leak bits of the private key — "timing attacks").

## 3. Cryptographic Issues (Common Implementation/Design Problems)

1. **Weak key generation** — using predictable or low-entropy random number generators (e.g., Debian's OpenSSL bug, 2008, where a code change reduced randomness, generating predictable SSL keys for nearly two years).
2. **Key reuse** — using the same key (or IV/nonce) across multiple sessions, weakening security (e.g., reusing an IV in CBC or a nonce in GCM mode can leak plaintext relationships or break authentication).
3. **Poor key management** — keys stored insecurely, not rotated, or improperly disposed of.
4. **Algorithm/protocol downgrade attacks** — forcing a system to fall back to a weaker, older algorithm (e.g., POODLE attack forced SSL 3.0 fallback).
5. **Padding oracle attacks** — exploiting error messages/timing differences related to padding validation to decrypt ciphertext without the key (e.g., the classic **BEAST** and **Lucky13** attacks on TLS/CBC mode).
6. **Insufficient key length** — using outdated key sizes (56-bit DES, 512-bit RSA) vulnerable to brute force with modern hardware.
7. **Hardcoded/embedded keys** — keys baked into source code or firmware, easily extracted via reverse engineering.

> **Self-Learning tie-in**: This directly connects to the self-learning topic "Common cryptographic implementation mistakes" listed after Session 7 — make sure to study that alongside this section.

## 4. CrypTool Lab — What You're Actually Doing & Why (Viva Prep)

CrypTool is an open-source educational tool used to visualize and experiment with cryptographic algorithms.

### Lab Tasks Mapped to Concepts
| Lab Task | What it Demonstrates |
|---|---|
| Caesar, Substitution cipher | Classical substitution; frequency analysis vulnerability |
| Vernam cipher | Practical demonstration of XOR-based stream cipher / OTP concept |
| DES, Triple DES, AES, RC4 | Symmetric block/stream ciphers — observe avalanche effect (small input change → large output change) |
| XOR | Foundational bitwise operation behind stream ciphers and OTP |
| RSA encryption/decryption | Hands-on asymmetric encryption — generating keys, encrypting, decrypting |
| ECC | Demonstrates elliptic-curve-based key generation and operations |

> **Common viva question**: "Why does changing a single bit of plaintext or key in AES drastically change the entire ciphertext?" — Answer: this is the **avalanche effect**, a deliberate design goal achieved through diffusion (Session 3) — it prevents attackers from inferring small input changes from small output changes, defeating differential cryptanalysis.

---

## Tricky / Conceptual Questions

**Q1. If Diffie-Hellman is mathematically secure against eavesdroppers, why is it still considered "broken" in many practical deployments without modification?**
Because the *mathematical* security (against passive eavesdropping/discrete log problem) is separate from *protocol-level* security (active MITM attacks). Plain DH has no built-in way to verify "is this really Bob's public value, or an attacker's?" — hence it must always be paired with authentication (digital signatures, certificates) in real protocols like TLS.

**Q2. Why does a known-plaintext attack succeed against some historical ciphers but fail against AES?**
Classical ciphers (Caesar, simple substitution) have small key spaces and direct, recoverable mathematical relationships between plaintext, key, and ciphertext — a few known pairs can reveal the entire key. AES's design (confusion + diffusion across many rounds) ensures the relationship between plaintext, key, and ciphertext is so complex that knowing some plaintext-ciphertext pairs gives no practical advantage in deriving the key within a feasible timeframe.

**Q3. What's the difference between a brute-force attack and a meet-in-the-middle attack on 2-key Triple DES?**
A brute-force attack against 2-key 3DES (112-bit nominal security) would try 2^112 keys directly. A meet-in-the-middle attack instead encrypts known plaintext with all possible first keys and decrypts known ciphertext with all possible second keys, looking for a match in the middle — reducing effective security to roughly 2^56–2^57 (much weaker than the nominal 112-bit strength), which is why 2-key 3DES is considered less secure than its key length suggests.

**Q4. Why is reusing a nonce/IV dangerous even with a strong cipher like AES-GCM?**
AES-GCM's security guarantee (confidentiality AND authenticity) **completely breaks down** if a nonce is reused with the same key — it can allow attackers to recover the authentication key and forge messages. This is a famous real-world failure mode (e.g., it affected some real implementations) and is a top "implementation mistake" interviewers love to ask about.

**Q5. In CrypTool, why does encrypting "AAAAAAAA" with AES not produce a repeating ciphertext pattern (unlike with ECB-mode weaknesses described elsewhere)?**
This depends entirely on the **mode of operation** chosen in the lab. If using ECB mode, identical plaintext blocks *would* produce identical ciphertext blocks (pattern visible). If using CBC/CTR/GCM, each block's encryption depends on previous blocks or a unique counter/IV, so identical plaintext does NOT produce identical ciphertext — illustrating why mode choice matters as much as algorithm choice.

---

## Interview Questions & Model Answers

**Q: Walk me through how Diffie-Hellman is vulnerable to MITM, and how TLS fixes this.**
> In raw DH, Alice and Bob exchange public values over an open channel with no way to verify each other's identity — an attacker can intercept and establish separate shared secrets with each party, becoming an invisible relay. TLS fixes this by having the server (and optionally client) present a **digital certificate signed by a trusted CA**, so the DH public value is bound to a verified identity — the client can cryptographically confirm "this DH value really came from the real server," preventing MITM substitution.

**Q: What's a side-channel attack, and why does it matter even if the cipher itself is unbreakable?**
> A side-channel attack exploits information leaked through the physical implementation of a system — timing variations, power consumption, electromagnetic emissions, even sound — rather than attacking the math directly. For example, measuring how long a smart card takes to perform RSA operations can leak bits of the private key. It matters because it proves that "secure algorithm" and "secure system" are not the same thing — implementation matters just as much as the underlying math.

**Q: Give an example of a real-world downgrade attack and explain its impact.**
> The POODLE attack (2014) exploited the fact that some systems would fall back to the older, weaker SSL 3.0 protocol if a TLS handshake failed, then exploited a padding vulnerability in SSL 3.0's CBC mode to decrypt secure cookies. The lesson: backward-compatibility features can themselves become attack vectors if not carefully restricted.

**Q: Why do security engineers care about "perfect forward secrecy," and how does Diffie-Hellman relate to it?**
> Perfect Forward Secrecy (PFS) ensures that even if a server's long-term private key is later compromised, past recorded sessions **cannot** be decrypted retroactively. This is achieved by using **ephemeral** Diffie-Hellman key exchange (DHE/ECDHE) — generating a fresh, temporary DH key pair for every session, so no single compromised long-term key can unlock historical traffic.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| Diffie-Hellman | Establishes shared secret over insecure channel; based on Discrete Log Problem |
| DH's biggest weakness | No authentication → vulnerable to MITM |
| Fix for DH MITM | Combine with digital signatures/certificates (as in TLS) |
| Ciphertext-only / Known-PT / Chosen-PT / Chosen-CT | Increasing attacker power, in that order |
| Side-channel attack | Exploits implementation (timing/power), not math |
| Birthday attack | Exploits probability to find hash collisions faster |
| Meet-in-the-middle | Reduces effective security of multi-encryption schemes (e.g., 2-key 3DES) |
| Nonce/IV reuse | Catastrophic for stream ciphers and AEAD modes like GCM |
| Perfect Forward Secrecy | Achieved via ephemeral DH (DHE/ECDHE) |
| Avalanche effect | Small input change → large, unpredictable output change (design goal) |
