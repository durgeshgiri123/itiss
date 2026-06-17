# Session 4: Symmetric Key Encryption (DES, AES, RC5) & Asymmetric Key Encryption (RSA, ECC)

## PART A: Symmetric Key Encryption

### 1. DES (Data Encryption Standard)

- Developed by **IBM**, adopted as a US federal standard (**NIST/NBS**) in **1977**.
- **Block cipher**: operates on **64-bit blocks** of plaintext.
- **Key size**: 64 bits total, but only **56 bits effective** (8 bits used for parity/error-checking, not security).
- **Structure**: Based on a **Feistel network** with **16 rounds**, each using substitution (S-boxes) and permutation.
- **Operations per round**: Expansion, key mixing (XOR), substitution, permutation.

**Why DES is considered insecure today:**
- 56-bit key → only 2⁵⁶ possible keys → brute-forceable with modern hardware/cloud computing in hours/days (famously cracked by the **EFF's "Deep Crack"** machine in 1998 in under 3 days).

**3DES (Triple DES)** — a stopgap fix:
- Applies DES three times: Encrypt-Decrypt-Encrypt (EDE), typically with two or three different keys, giving an effective security of ~112 bits (with 2 keys) or 168 bits (with 3 keys, though meet-in-the-middle attacks reduce effective strength).
- Slower than AES; now deprecated/being phased out (NIST disallowed 3DES for new applications post-2023 in many contexts).

### 2. AES (Advanced Encryption Standard)

- Selected by **NIST in 2001** as DES's replacement, based on the **Rijndael** algorithm (designed by Belgian cryptographers Joan Daemen and Vincent Rijmen).
- **Block size**: Fixed at **128 bits**.
- **Key sizes**: 128, 192, or 256 bits (AES-128, AES-192, AES-256).
- **Rounds**: 10 (for 128-bit key), 12 (192-bit), 14 (256-bit).
- **Structure**: NOT a Feistel network — uses a **Substitution-Permutation Network (SPN)** with operations: SubBytes (substitution via S-box), ShiftRows (permutation), MixColumns (diffusion via matrix multiplication), AddRoundKey (XOR with round key).
- Currently considered secure against brute force for the foreseeable future (256-bit AES is even considered resistant to quantum attacks reducing it to ~128-bit equivalent strength via Grover's algorithm — a great interview point).

### 3. RC5 (and brief note on RC4)

- **RC5**: Designed by **Ron Rivest** (1994) — a fast, simple block cipher with **variable block size, key size, and number of rounds** (making it highly configurable — a key exam point distinguishing it from fixed-parameter ciphers like AES/DES).
- **RC4**: A **stream cipher** (not block cipher), historically used in WEP and early SSL — now considered broken/weak due to biases in its keystream output; deprecated in modern protocols (e.g., removed from TLS 1.3).

> **Block cipher vs Stream cipher (classic exam question):**
> - **Block cipher**: encrypts fixed-size blocks of data at once (e.g., DES, AES). Needs padding for partial blocks; needs a "mode of operation" (ECB, CBC, CTR, GCM) to handle multiple blocks securely.
> - **Stream cipher**: encrypts data **bit-by-bit or byte-by-byte** continuously, typically by XORing with a generated keystream (e.g., RC4). Faster for streaming data, no padding needed.

### Modes of Operation (often tested alongside block ciphers)

| Mode | Description | Weakness/Note |
|---|---|---|
| **ECB** (Electronic Code Book) | Each block encrypted independently | Identical plaintext blocks → identical ciphertext blocks (pattern leakage) — **insecure for most real use** |
| **CBC** (Cipher Block Chaining) | Each block XORed with previous ciphertext block before encryption | Needs an IV (Initialization Vector); sequential, can't parallelize encryption |
| **CTR** (Counter Mode) | Turns a block cipher into a stream cipher using a counter | Parallelizable, no padding needed |
| **GCM** (Galois/Counter Mode) | CTR mode + built-in authentication (integrity) | Provides **AEAD** (Authenticated Encryption with Associated Data) — used heavily in TLS 1.3 |

---

## PART B: Asymmetric Key Encryption

### 4. RSA (Rivest-Shamir-Adleman)

- Published in **1977/1978** by Ron Rivest, Adi Shamir, Leonard Adleman at MIT.
- Security is based on the **mathematical difficulty of factoring the product of two large prime numbers**.

**Simplified Key Generation Steps (often asked to recall in exams):**
1. Choose two large distinct prime numbers, **p** and **q**.
2. Compute **n = p × q** (this becomes part of both public and private keys — the "modulus").
3. Compute **φ(n) = (p-1)(q-1)** (Euler's totient function).
4. Choose public exponent **e** such that 1 < e < φ(n) and gcd(e, φ(n)) = 1 (commonly e = 65537).
5. Compute private exponent **d** such that **d × e ≡ 1 (mod φ(n))** (d is the modular multiplicative inverse of e).
6. **Public Key = (e, n)**, **Private Key = (d, n)**.

**Encryption**: `C = M^e mod n`
**Decryption**: `M = C^d mod n`

**Why RSA is secure:** Given only the public key (e, n), deriving the private key (d) requires factoring n into p and q — which is computationally infeasible for sufficiently large n (current standard: 2048-bit or higher).

**Typical key sizes**: 2048-bit (current minimum standard), 3072-bit, 4096-bit (higher security, but slower).

### 5. ECC (Elliptic Curve Cryptography)

- Based on the algebraic structure of elliptic curves over finite fields.
- Security relies on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)** — given points P and Q = kP on a curve, it's computationally infeasible to find k.

**Key advantage over RSA**: ECC achieves **equivalent security with much smaller key sizes**.

| Security Level | RSA Key Size | ECC Key Size |
|---|---|---|
| 80-bit | 1024-bit | 160-bit |
| 112-bit | 2048-bit | 224-bit |
| 128-bit | 3072-bit | 256-bit |
| 256-bit | 15360-bit | 521-bit |

> **This table is a favorite in exams** — be ready to explain *why*: smaller keys mean less computation, less storage, less bandwidth — crucial for mobile devices, smart cards, and IoT, which is why ECC is increasingly preferred in modern TLS certificates and cryptocurrencies (e.g., Bitcoin uses ECDSA, a form of ECC).

---

## Tricky / Conceptual Questions

**Q1. Why is AES not based on a Feistel structure like DES, and does that matter?**
AES uses an SPN (Substitution-Permutation Network) instead of Feistel. In a Feistel cipher, only half the block is transformed each round (the other half is just XORed in), requiring more rounds for full diffusion. SPN structures transform the **entire block** every round, achieving faster diffusion and allowing AES to be secure with fewer rounds while being highly efficient in hardware and software.

**Q2. If 3DES is more secure than DES (more bits), why is it being phased out in favor of AES rather than kept as the "safe upgrade"?**
3DES is significantly **slower** (3x the operations of single DES) and has a smaller effective block size (64 bits) that makes it vulnerable to birthday-bound attacks (e.g., the **Sweet32 attack**) when large volumes of data are encrypted under one key. AES is faster, has a larger 128-bit block size, and is purpose-built for modern hardware — making it superior on both speed and security grounds.

**Q3. Can RSA be used to encrypt large files directly?**
Not practically — RSA can only encrypt data smaller than its modulus size (e.g., a 2048-bit RSA key can encrypt at most ~256 bytes minus padding overhead), and it's computationally expensive. In practice, RSA is used to encrypt a **symmetric session key** (hybrid approach), and that symmetric key is then used to encrypt the actual file/message with AES.

**Q4. Why does ECC achieve the same security with a much smaller key than RSA?**
Because the best-known algorithms for solving the discrete logarithm problem on elliptic curves are fundamentally harder/slower than the best-known algorithms for factoring large integers (RSA's basis). This means an attacker needs proportionally far more computational effort per key-bit to break ECC than to break RSA, letting ECC use smaller keys for equivalent security.

**Q5. What is a common mistake when comparing AES key size to RSA key size?**
Assuming "AES-256 is weaker than RSA-2048 because 256 < 2048" — this is wrong. Symmetric and asymmetric key sizes are **not directly comparable** because they rely on entirely different mathematical hardness assumptions (brute-force search space vs factoring difficulty). Refer to the equivalence table above: AES-256 roughly corresponds to a much larger RSA key (~15360 bits) for matching security levels.

**Q6. Why does the choice of "mode of operation" matter even if the underlying cipher (e.g., AES) is secure?**
Because a strong cipher used in a weak mode (e.g., ECB) can leak information through patterns (identical plaintext blocks → identical ciphertext blocks), regardless of how strong AES itself is. Security is a property of the **whole system**, not just the algorithm.

---

## Interview Questions & Model Answers

**Q: Why was DES replaced by AES instead of just increasing DES's key size?**
> DES's 56-bit key size couldn't simply be extended without redesigning the core algorithm structure (the Feistel rounds and S-boxes were tuned for that key/block size). Rather than patch an aging design, NIST ran an open international competition (the AES competition, 1997–2001) and selected Rijndael — a modern, efficient, and more secure design from the ground up.

**Q: Explain RSA's security assumption in one sentence, as you would to a junior developer.**
> RSA is secure because, although it's easy to multiply two large prime numbers together to get n, it's computationally infeasible to reverse that process and find the original primes once n gets large enough (thousands of bits) — and you need those primes to compute the private key.

**Q: When would you choose ECC over RSA in a real system design, and why?**
> I'd choose ECC for resource-constrained environments — mobile devices, IoT, smart cards, or any system where bandwidth/storage/compute matters — because ECC gives equivalent security with far smaller keys, faster computations, and lower power consumption, which is why modern TLS certificate authorities increasingly offer ECDSA certificates.

**Q: What's a real-world consequence of using ECB mode incorrectly?**
> A famous real example is the original ECB-encrypted version of a well-known image (often shown in textbooks: the "ECB penguin") where encrypting an image in ECB mode still showed the outline of the penguin, because identical pixel-block patterns in the plaintext produced identical ciphertext blocks — visually leaking the image's structure despite "encryption."

---

## Quick Revision Table

| Algorithm | Type | Key Size | Block Size | Structure | Status |
|---|---|---|---|---|---|
| DES | Symmetric | 56-bit effective | 64-bit | Feistel, 16 rounds | Broken/deprecated |
| 3DES | Symmetric | 112/168-bit | 64-bit | 3x DES (EDE) | Deprecated |
| AES | Symmetric | 128/192/256-bit | 128-bit | SPN, 10/12/14 rounds | Current standard |
| RC4 | Symmetric (stream) | 40–2048-bit | N/A (stream) | Keystream XOR | Broken/deprecated |
| RC5 | Symmetric | Variable | Variable | Variable rounds | Legacy/configurable |
| RSA | Asymmetric | 2048+ bit | N/A | Factoring-based | Current standard |
| ECC | Asymmetric | 256-bit ≈ RSA 3072-bit | N/A | Discrete log on curves | Current/preferred for efficiency |
