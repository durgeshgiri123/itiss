# Session 2: Basic Encryption Concepts, File Encryption, Folder Encryption

## 1. What is Encryption?

Encryption is the process of converting **plaintext** (readable data) into **ciphertext** (unreadable data) using an algorithm and a **key**, such that only someone with the correct key can reverse the process (**decryption**) and recover the plaintext.

```
Plaintext --[Encryption Algorithm + Key]--> Ciphertext
Ciphertext --[Decryption Algorithm + Key]--> Plaintext
```

### Key Terminology

| Term | Meaning |
|---|---|
| **Plaintext** | Original readable message |
| **Ciphertext** | Encrypted, unreadable message |
| **Key** | A piece of secret information controlling the algorithm's output |
| **Algorithm/Cipher** | The mathematical procedure used for encryption/decryption |
| **Cryptanalysis** | The science of breaking ciphers without the key |
| **Cryptology** | The umbrella field = Cryptography + Cryptanalysis |
| **Kerckhoffs's Principle** | A cryptosystem should be secure even if everything about the system, except the key, is public knowledge |

> **Exam favorite:** Kerckhoffs's Principle is the foundation of modern cryptography — "security through obscurity" (hiding the algorithm) is considered weak design; security must rely **only** on the secrecy of the key.

## 2. Why Encrypt? (Goals)

- Protect data **at rest** (stored files, databases, disks)
- Protect data **in transit** (network communication)
- Protect data **in use** (less common, requires advanced techniques like homomorphic encryption — beyond this course but worth knowing the term for interviews)

## 3. File Encryption

File encryption protects individual files by converting their content into ciphertext. Two common approaches:

1. **Software-based file encryption** — tools like 7-Zip (AES-256), VeraCrypt, GPG encrypt the file content; you need a password/key to open it.
2. **OS-level/Filesystem encryption** — e.g., Windows **EFS (Encrypting File System)**, which transparently encrypts files using a per-file symmetric key, which is itself encrypted with the user's public key (a hybrid approach — important for PKI context!).

### How EFS Works (important PKI tie-in)
1. A random symmetric **File Encryption Key (FEK)** encrypts the actual file (fast, since symmetric encryption is efficient for bulk data).
2. The FEK itself is encrypted using the **user's public key** (asymmetric).
3. To decrypt, the user's **private key** decrypts the FEK, and the FEK decrypts the file.

> This hybrid (symmetric + asymmetric) model reappears throughout PKI — e.g., in TLS/SSL (Session 13) and S/MIME (Session 14). Understanding it here makes later sessions much easier.

## 4. Encrypting Folders

- **Graphical method**: In Windows, right-click → Properties → Advanced → "Encrypt contents to secure data" (uses EFS under the hood).
- **Using `cipher` command** (Windows CLI tool):
  - `cipher /e` — encrypts the specified folder
  - `cipher /d` — decrypts the specified folder
  - `cipher /w:<path>` — wipes deleted/unused disk space (security hygiene — removes remnants of previously deleted unencrypted data)

### Why Folder-level vs File-level matters
Encrypting a folder applies a policy so that **any new file created inside it** is automatically encrypted — useful for ongoing/automated protection rather than manually encrypting every file.

## 5. Important Caveats Often Tested

- EFS keys are tied to the **Windows user account**. If the user profile is deleted/corrupted, and there's no recovery agent or backup of the certificate, the data is **permanently lost** — this is a classic exam/interview trap question.
- Encrypting a file does NOT protect it from a user logged in as that same account (i.e., EFS protects against other users/external access on the same machine or stolen disk, not against someone who is already logged in as you).
- Moving an EFS-encrypted file to a non-NTFS filesystem (e.g., FAT32 USB drive) **decrypts it automatically** — a very common practical gotcha.

---

## Tricky / Conceptual Questions

**Q1. If I encrypt a file using EFS, and I forget my Windows password but an admin resets it for me, can I still open the file?**
No — this is the trap. EFS keys are derived from the user's actual credentials/certificate, not the login password itself in a directly recoverable way. If the password is reset by an admin (rather than changed by the user who remembers the old one), the private key used to decrypt the FEK can become inaccessible unless a recovery agent certificate exists. This is why **EFS recovery agents / key backup** are critical in real deployments.

**Q2. Why use a hybrid approach (symmetric FEK + asymmetric key) instead of just encrypting the file directly with the public key?**
Asymmetric algorithms (RSA, ECC) are computationally expensive and slow for large data. Symmetric algorithms (AES) are fast for bulk encryption. So the **bulk data** is encrypted symmetrically (fast), and only the **small symmetric key** is encrypted asymmetrically (secure key exchange without prior shared secret). This pattern is universal in real-world crypto systems.

**Q3. Does encrypting a folder protect it from ransomware?**
Not really — if ransomware runs as the logged-in user, it inherits the same access/decryption rights as that user and can read, then re-encrypt with its own key. EFS protects against external/offline access, not against malicious code running in an authenticated session.

**Q4. What happens to file encryption if you copy an EFS file from an NTFS drive to a FAT32 USB stick?**
The file is **decrypted automatically during the copy** because FAT32 doesn't support EFS. This is a serious data-leakage risk that organizations must train users about.

---

## Interview Questions & Model Answers

**Q: What's the difference between encrypting a single file and using full-disk encryption (like BitLocker)?**
> File encryption protects specific files even while the OS is running and other users are logged in; full-disk encryption protects the entire drive's contents primarily against offline/physical theft (e.g., a stolen laptop) — once the OS boots and you're authenticated, the disk is already decrypted for use.

**Q: Why is Kerckhoffs's Principle important in real-world systems like PKI?**
> Because algorithms like AES and RSA are public and well-studied — their security comes purely from key secrecy and key length, not from hiding the algorithm. This allows worldwide peer review (strengthening trust) while still keeping data secure, as long as keys are protected.

**Q: If you were auditing a company's file encryption practices, what's the first thing you'd check?**
> Key management — where are the encryption/recovery keys stored, who has access, and is there a recovery process if an employee leaves or loses access? Most real-world encryption failures come from poor key management, not weak algorithms.

---

## Quick Revision Table

| Concept | One-line Recall |
|---|---|
| Plaintext → Ciphertext | via Algorithm + Key |
| Kerckhoffs's Principle | Security must depend only on the key, not secrecy of algorithm |
| EFS hybrid model | FEK (symmetric) encrypts file; FEK encrypted by user's public key |
| `cipher /e` `/d` `/w` | encrypt / decrypt / wipe free space |
| EFS + FAT32 USB | File auto-decrypts on copy (no EFS support) |
| EFS weakness | Tied to user account/certificate; lost cert without recovery agent = permanent data loss |
