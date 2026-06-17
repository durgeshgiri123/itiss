# Session 13: SSL, TLS, PGP/S-MIME, OpenSSL Hierarchical PKI Lab

---

## 1. SSL — Secure Sockets Layer

### History & Evolution
| Version | Year | Status |
|---------|------|--------|
| SSL 1.0 | Never released | Internal Netscape |
| SSL 2.0 | 1995 | Deprecated (POODLE, DROWN) |
| SSL 3.0 | 1996 | Deprecated (POODLE attack) |
| TLS 1.0 | 1999 | Deprecated 2020 |
| TLS 1.1 | 2006 | Deprecated 2020 |
| TLS 1.2 | 2008 | Still widely used |
| TLS 1.3 | 2018 | Current standard |

> **Tricky Q:** SSL is technically dead — all modern "SSL" is actually TLS. When someone says "SSL certificate," they mean a TLS certificate.

---

## 2. TLS — Transport Layer Security

### What TLS Provides
| Service | Mechanism |
|---------|-----------|
| **Confidentiality** | Symmetric encryption (AES-GCM) |
| **Integrity** | MAC / AEAD (Authenticated Encryption) |
| **Authentication** | Certificates (X.509) + asymmetric crypto |
| **Forward Secrecy** | Ephemeral key exchange (DHE/ECDHE) |

### TLS operates between:
- **Transport Layer** (TCP) and **Application Layer** (HTTP, SMTP, etc.)
- Port 443 = HTTPS (HTTP over TLS)

---

## 3. TLS 1.2 Handshake (Full Detail)

```
Client                                      Server
  |                                            |
  |-------- ClientHello ---------------------->|
  |  (TLS version, cipher suites, random_C)    |
  |                                            |
  |<-------- ServerHello ----------------------|
  |  (chosen cipher suite, random_S, session)  |
  |                                            |
  |<-------- Certificate ----------------------|
  |  (Server's X.509 certificate)              |
  |                                            |
  |<-------- ServerKeyExchange (if DHE/ECDHE)--|
  |  (DH parameters signed by server)          |
  |                                            |
  |<-------- ServerHelloDone ------------------|
  |                                            |
  |-------- ClientKeyExchange --------------->|
  |  (PreMasterSecret encrypted with server pub key OR DH public value) |
  |                                            |
  |-------- ChangeCipherSpec ---------------->|
  |-------- Finished (MAC of handshake) ----->|
  |                                            |
  |<-------- ChangeCipherSpec -----------------|
  |<-------- Finished (MAC of handshake) ------|
  |                                            |
  |====== Encrypted Application Data =========|
```

### Master Secret Derivation (TLS 1.2)
```
PreMasterSecret + random_C + random_S → PRF → MasterSecret (48 bytes)
MasterSecret → Key Material → Client/Server Write Keys + IVs + MACs
```

---

## 4. TLS 1.3 Handshake (Improved)

### Key Differences from TLS 1.2
| Feature | TLS 1.2 | TLS 1.3 |
|---------|---------|---------|
| Handshake RTT | 2-RTT | **1-RTT** (0-RTT for resumption) |
| Key Exchange | RSA or DHE/ECDHE | **ECDHE only** (no RSA key exchange) |
| Cipher Suites | Many (including weak) | Only AEAD ciphers (AES-GCM, ChaCha20) |
| Forward Secrecy | Optional | **Mandatory** |
| Removed Features | — | RSA key transport, static DH, RC4, 3DES, SHA-1, MD5 |

### TLS 1.3 Handshake Flow
```
Client                                      Server
  |-------- ClientHello + key_share --------->|
  |  (Sends DH public key upfront)            |
  |                                            |
  |<-------- ServerHello + key_share ----------|
  |<-------- {Certificate} -------------------|
  |<-------- {CertificateVerify} -------------|
  |<-------- {Finished} ----------------------|
  |  (Server already computes session keys)   |
  |                                            |
  |-------- {Finished} ---------------------->|
  |====== Encrypted Application Data =========|
```

### 0-RTT (Early Data) in TLS 1.3
- Client can send data in the **first message** using a pre-shared key (PSK) from a previous session
- **Risk:** Replay attacks (0-RTT data can be replayed)
- Safe only for **idempotent operations** (e.g., GET requests)

---

## 5. TLS Cipher Suites

### Format (TLS 1.2)
```
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
 |     |     |        |    |    |
 |     |     |        |    |    +-- MAC / PRF hash
 |     |     |        |    +------- Mode (GCM = AEAD)
 |     |     |        +------------ Key size (256-bit)
 |     |     +--------------------- Bulk encryption (AES)
 |     +--------------------------- Auth (RSA certificate)
 +--------------------------------- Key Exchange (ECDHE)
```

### TLS 1.3 Cipher Suites (simplified — only 5 allowed)
- `TLS_AES_256_GCM_SHA384`
- `TLS_CHACHA20_POLY1305_SHA256`
- `TLS_AES_128_GCM_SHA256`

---

## 6. Perfect Forward Secrecy (PFS)

- **PFS** ensures session keys cannot be derived even if the server's **long-term private key** is compromised later
- Achieved by using **ephemeral key exchange** — DHE or ECDHE
- Each session uses a **fresh, temporary DH key pair** discarded after the session
- Without PFS: if private key is stolen, past encrypted traffic (captured by attacker) can be decrypted retroactively

> **Exam Q:** Which cipher suites provide PFS?
> Any suite with **DHE** or **ECDHE** in the name provides forward secrecy.

---

## 7. Common TLS Attacks

| Attack | Description | Defense |
|--------|-------------|---------|
| **POODLE** | Forces SSL 3.0 downgrade; CBC padding oracle | Disable SSL 3.0/TLS 1.0 |
| **BEAST** | CBC mode attack on TLS 1.0 | Use TLS 1.2+ / RC4 (ironically) |
| **CRIME** | Compression + TLS = info leak via side channel | Disable TLS compression |
| **BREACH** | HTTP compression oracle (similar to CRIME) | Disable HTTP compression |
| **HEARTBLEED** | OpenSSL bug — reads memory beyond buffer | Patch OpenSSL (CVE-2014-0160) |
| **FREAK** | Forces export-grade RSA (512-bit) | Disable export cipher suites |
| **Logjam** | Forces weak 512-bit DH params | Use 2048+ bit DH; disable DHE_EXPORT |
| **DROWN** | SSLv2 allows decryption of TLS sessions | Disable SSLv2 on all servers |
| **Downgrade Attack** | Force older TLS version | TLS_FALLBACK_SCSV; disable old versions |
| **Certificate Pinning Bypass** | SSLstrip, MitM via rogue cert | Certificate pinning, HSTS |

### SSL Stripping
- Attacker downgrades HTTPS to HTTP silently
- **Defense:** HSTS (HTTP Strict Transport Security) — browser refuses HTTP

---

## 8. HTTPS & Certificate Validation

### Browser Certificate Validation Steps
1. Check certificate **not expired**
2. Verify **signature chain** up to trusted root CA
3. Check **hostname** matches CN or SAN
4. Verify certificate **not revoked** (CRL/OCSP)
5. Check **key usage** extensions

### Certificate Pinning
- App/browser hardcodes expected certificate or public key
- Prevents MitM even with rogue CA-issued cert
- **HPKP** (HTTP Public Key Pinning) — deprecated header
- Modern apps use static pins in code

---

## 9. DTLS — Datagram TLS

- TLS adapted for **UDP** (connectionless)
- Used for: VoIP, video conferencing, WebRTC, gaming
- Adds: sequence numbers, retransmission, reordering handling
- Same security as TLS but tolerates packet loss

---

## 10. PGP — Pretty Good Privacy

### What is PGP?
A cryptographic system for **encrypting emails and files** using a combination of symmetric and asymmetric cryptography. Created by **Phil Zimmermann** in 1991.

### PGP Trust Model — Web of Trust
- **No central CA** — users sign each other's keys
- Each user builds their own **trust network**
- Key servers (e.g., `keyserver.ubuntu.com`) store public keys
- **Trust levels:** Ultimate → Full → Marginal → Unknown → Untrusted

### PGP Encryption Process
```
1. Generate random Session Key (symmetric, e.g., AES-256)
2. Encrypt MESSAGE with Session Key
3. Encrypt Session Key with RECIPIENT's Public Key
4. Send: [Encrypted Session Key + Encrypted Message]

Recipient:
1. Decrypt Session Key using their Private Key
2. Decrypt Message using Session Key
```

### PGP Signing Process
```
1. Hash the MESSAGE (SHA-256)
2. Encrypt hash with SENDER's Private Key = Digital Signature
3. Send: [Message + Signature]

Recipient:
1. Decrypt signature with Sender's Public Key = hash
2. Hash the received message independently
3. Compare hashes — match = authentic + intact
```

### PGP Combined (Sign + Encrypt)
```
Sign first → Encrypt the signed message
(Never encrypt first then sign — signature would cover ciphertext, not plaintext)
```

> **Tricky Q:** Should you sign-then-encrypt or encrypt-then-sign in PGP?
> **Sign first, then encrypt.** If you encrypt first and then sign, the signature covers only the ciphertext, not the original message content.

### OpenPGP vs GPG
- **OpenPGP** = RFC 4880 standard based on PGP
- **GPG (GnuPG)** = Free, open-source implementation of OpenPGP

---

## 11. S/MIME — Secure/Multipurpose Internet Mail Extensions

### What is S/MIME?
Standard for **securing email** using X.509 certificates (PKI-based), unlike PGP's web of trust.

### S/MIME vs PGP
| Feature | S/MIME | PGP |
|---------|--------|-----|
| Trust model | Hierarchical PKI (CA) | Web of Trust |
| Certificate type | X.509 | PGP key |
| Standard | RFC 5751 | RFC 4880 |
| Key distribution | Via CAs | Key servers / manual |
| Enterprise use | Yes (Outlook, etc.) | Less common |
| Personal use | Moderate | More common |

### S/MIME Operations
- **EnvelopedData** — Encrypted message (confidentiality)
- **SignedData** — Signed message (integrity + authentication)
- **CompressedData** — Compressed content
- **AuthEnvelopedData** — Authenticated encryption

### S/MIME in Email Clients
- Microsoft Outlook, Apple Mail support S/MIME natively
- Requires: S/MIME certificate from a CA (free from some CAs like Actalis)
- Both sender and recipient must have certificates

---

## 12. OpenSSL — Command Line Reference

### Key Generation
```bash
# Generate RSA private key (2048-bit)
openssl genrsa -out private.key 2048

# Generate RSA private key with AES-256 encryption
openssl genrsa -aes256 -out private.key 2048

# Generate EC private key (P-256 curve)
openssl ecparam -name prime256v1 -genkey -noout -out ec_private.key

# Extract public key from private key
openssl rsa -in private.key -pubout -out public.key
```

### Certificate Signing Request (CSR)
```bash
# Generate CSR
openssl req -new -key private.key -out request.csr

# Generate CSR with subject inline
openssl req -new -key private.key -out request.csr \
  -subj "/C=IN/ST=Tamil Nadu/L=Chennai/O=MyOrg/CN=www.example.com"

# View CSR contents
openssl req -in request.csr -text -noout
```

### Self-Signed Certificate
```bash
# Generate self-signed certificate (valid 365 days)
openssl req -x509 -new -nodes -key private.key -sha256 -days 365 -out cert.crt

# One-liner: generate key + self-signed cert
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
```

### Hierarchical PKI Lab (Root CA → Intermediate CA → End Entity)

#### Step 1: Create Root CA
```bash
mkdir -p root-ca/{certs,crl,newcerts,private}
echo 1000 > root-ca/serial
touch root-ca/index.txt

# Generate Root CA private key
openssl genrsa -aes256 -out root-ca/private/ca.key.pem 4096

# Generate Root CA self-signed certificate
openssl req -config openssl.cnf -key root-ca/private/ca.key.pem \
  -new -x509 -days 7300 -sha256 -extensions v3_ca \
  -out root-ca/certs/ca.cert.pem
```

#### Step 2: Create Intermediate CA
```bash
# Generate Intermediate CA key
openssl genrsa -aes256 -out intermediate/private/intermediate.key.pem 4096

# Generate Intermediate CA CSR
openssl req -config intermediate/openssl.cnf -new -sha256 \
  -key intermediate/private/intermediate.key.pem \
  -out intermediate/csr/intermediate.csr.pem

# Sign Intermediate CA CSR with Root CA
openssl ca -config openssl.cnf -extensions v3_intermediate_ca \
  -days 3650 -notext -md sha256 \
  -in intermediate/csr/intermediate.csr.pem \
  -out intermediate/certs/intermediate.cert.pem
```

#### Step 3: Issue End-Entity Certificate
```bash
# Generate server key
openssl genrsa -out server.key.pem 2048

# Generate server CSR
openssl req -new -key server.key.pem -out server.csr.pem

# Sign with Intermediate CA
openssl ca -config intermediate/openssl.cnf -extensions server_cert \
  -days 375 -notext -md sha256 \
  -in server.csr.pem -out server.cert.pem

# Create certificate chain file
cat server.cert.pem intermediate/certs/intermediate.cert.pem > chain.pem
```

### Verification & Inspection
```bash
# View certificate details
openssl x509 -in cert.crt -text -noout

# Verify certificate chain
openssl verify -CAfile root-ca/certs/ca.cert.pem \
  -untrusted intermediate/certs/intermediate.cert.pem server.cert.pem

# Check SSL connection
openssl s_client -connect example.com:443

# Check TLS version supported
openssl s_client -connect example.com:443 -tls1_3

# Check certificate expiry
openssl x509 -in cert.crt -noout -dates

# Check certificate fingerprint
openssl x509 -in cert.crt -noout -fingerprint -sha256
```

### Encryption/Decryption with OpenSSL
```bash
# Encrypt file with AES-256-CBC
openssl enc -aes-256-cbc -salt -in plaintext.txt -out encrypted.enc -k password

# Decrypt
openssl enc -d -aes-256-cbc -in encrypted.enc -out decrypted.txt -k password

# RSA encrypt (for small data)
openssl rsautl -encrypt -inkey public.key -pubin -in data.txt -out data.enc

# RSA decrypt
openssl rsautl -decrypt -inkey private.key -in data.enc -out data.txt
```

### Hashing with OpenSSL
```bash
openssl dgst -sha256 file.txt
openssl dgst -sha512 file.txt
openssl dgst -md5 file.txt  # Avoid for security use
```

---

## 13. STARTTLS vs SSL/TLS

| Feature | STARTTLS | SSL/TLS |
|---------|----------|---------|
| Connection start | Plain → upgrades to TLS | TLS from start |
| Port (SMTP) | 587 (SUBMISSION) | 465 (SMTPS) |
| Port (IMAP) | 143 | 993 |
| Port (POP3) | 110 | 995 |
| Vulnerability | Downgrade attack possible | More secure |

---

## 14. TLS in Different Protocols

| Protocol | Plain Port | TLS Port |
|----------|-----------|---------|
| HTTP | 80 | 443 (HTTPS) |
| SMTP | 25 | 465 (SMTPS) |
| IMAP | 143 | 993 |
| POP3 | 110 | 995 |
| FTP | 21 | 990 (FTPS) |
| LDAP | 389 | 636 (LDAPS) |
| RDP | 3389 | 3389 (with TLS) |

---

## 15. Tricky & Frequently Asked Interview Questions

**Q1: What is the difference between SSL and TLS?**
> SSL is the predecessor to TLS. SSL 2.0/3.0 are deprecated due to vulnerabilities (POODLE, DROWN). TLS 1.2 and 1.3 are current standards. "SSL" certificates are technically TLS certificates.

**Q2: What is Perfect Forward Secrecy and why does it matter?**
> PFS ensures each session uses an ephemeral key pair. Even if a server's long-term private key is compromised, previously captured traffic cannot be decrypted retroactively.

**Q3: How many round trips does TLS 1.3 take vs TLS 1.2?**
> TLS 1.2 = 2 RTT. TLS 1.3 = 1 RTT (0-RTT for session resumption).

**Q4: What is the HEARTBLEED vulnerability?**
> A buffer over-read bug in OpenSSL's heartbeat extension (CVE-2014-0160) that allowed attackers to read up to 64KB of server memory — potentially exposing private keys and session data.

**Q5: Difference between PGP and S/MIME?**
> PGP uses a Web of Trust (no CA needed); S/MIME uses hierarchical PKI with X.509 certificates from a CA. S/MIME is more common in enterprise email; PGP is more common for personal use.

**Q6: What is HSTS?**
> HTTP Strict Transport Security — a response header telling browsers to always use HTTPS for a domain. Defends against SSL stripping attacks.

**Q7: Why is 0-RTT in TLS 1.3 risky?**
> 0-RTT early data can be replayed by an attacker. It should only be used for idempotent operations.

**Q8: What does a TLS cipher suite like TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 mean?**
> ECDHE = key exchange; RSA = authentication; AES_256_GCM = bulk encryption with AEAD; SHA384 = PRF/MAC hash.

**Q9: Explain the Logjam attack.**
> Forces a downgrade to 512-bit export-grade DH parameters (DHE_EXPORT). An attacker can precompute discrete logs for 512-bit primes. Defense: use 2048+ bit DH groups and disable DHE_EXPORT.

**Q10: What is certificate transparency (CT)?**
> A public log of all issued TLS certificates that allows domain owners and browsers to detect misissued or rogue certificates. Required by Chrome for all publicly trusted certs since 2018.

---

## Quick Revision Mnemonics

- **TLS 1.3 removes:** RSA key exchange, static DH, RC4, 3DES, SHA-1, MD5 — remember **"RSA-DH-RC4-3D-SHA1-MD5 Gone"**
- **PGP** = Web of Trust | **S/MIME** = Hierarchical PKI
- **PFS** = DHE or ECDHE cipher suites
- **POODLE** → SSL 3.0 | **HEARTBLEED** → OpenSSL | **BEAST** → TLS 1.0 | **CRIME** → Compression
- **Sign-then-Encrypt** = correct PGP order
