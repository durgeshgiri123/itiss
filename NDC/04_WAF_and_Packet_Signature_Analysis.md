# Web Application Firewall (WAF) & Packet Signature Analysis

## 1. Introduction to Web Application Firewall (WAF)

A WAF is a Layer 7 security control that **filters, monitors, and blocks HTTP/HTTPS traffic** to and from a web application, protecting against application-layer attacks that a traditional/network firewall cannot see.

### 1.1 Why Not Just Use a Regular Firewall?
A network firewall inspects IP/port/protocol — it will happily allow port 443 traffic containing a SQL injection payload since the payload is inside the "allowed" HTTP stream. A WAF inspects the actual **HTTP request/response content**.

### 1.2 WAF Security Models
| Model | Approach | Pros | Cons |
|---|---|---|---|
| **Negative Security Model (Blacklist)** | Block known bad patterns/signatures (e.g., OWASP CRS rules) | Easy to deploy, low false positives initially | Misses unknown/zero-day attack patterns |
| **Positive Security Model (Whitelist)** | Allow only explicitly defined valid input/traffic; deny everything else | Very strong protection, blocks zero-days | High initial tuning effort, can break legit functionality if too strict |
| **Hybrid** | Combination of both | Balanced | Complex to maintain |

> **Tricky Q:** Why is the positive security model considered stronger against zero-day attacks than the negative model?
> **A:** The negative model only blocks **known** malicious patterns — a novel/unseen attack payload can slip through if no signature matches. The positive model defines what is **allowed** and rejects everything else by default, so even unknown attack payloads are blocked simply because they don't match the approved pattern.

### 1.3 Common Attacks a WAF Protects Against (OWASP Top 10 relevance)
- SQL Injection
- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- Local/Remote File Inclusion (LFI/RFI)
- Command Injection
- Cookie/session tampering
- Application-layer DoS (Slowloris, HTTP flood)

### 1.4 WAF Deployment Modes
- **Network-based (appliance)** – low latency, high cost, needs hardware.
- **Host-based (integrated into app server)** – deep visibility, resource-intensive on host.
- **Cloud-based/CDN WAF** – easy to deploy, low maintenance, scalable (e.g., Cloudflare WAF, AWS WAF).

> **Tricky Q:** WAF vs IPS — what's the difference?
> **A:** IPS operates broadly across network protocols/layers using signatures/anomaly detection for general network attacks; WAF is **specialized purely for HTTP/HTTPS and web application logic** (understands application context like cookies, sessions, form fields), giving far deeper protection against web-specific attacks like SQLi/XSS that a generic IPS signature might miss.

---

## 2. Packet Signature and Analysis

### 2.1 What is a Packet Signature?
A **signature** is a specific pattern (byte sequence, header value, string, regex) known to be associated with malicious traffic or a specific attack/exploit — used by IDS/IPS/AV engines for detection.

- Example: Snort rule looking for a specific string in an HTTP payload associated with a known exploit.
- Types of signatures:
  - **Exploit-facing** – matches the payload of the exploit itself.
  - **Vulnerability-facing** – matches attempts to exploit a specific vulnerability (broader coverage, catches variants).

> **Tricky Q:** Why is a vulnerability-facing signature generally considered better than an exploit-facing signature?
> **A:** An exploit-facing signature only matches **one specific known exploit's byte pattern** — attackers can trivially modify/obfuscate the exploit to evade it. A vulnerability-facing signature targets the underlying **protocol/condition being abused**, so it catches multiple variants/mutations of exploits targeting the same vulnerability.

### 2.2 Packet Analysis Process
1. **Capture** – using tools like Wireshark, tcpdump, or SPAN/TAP-fed sensors.
2. **Filter** – isolate relevant traffic (by IP, port, protocol) — e.g., `tcpdump -i eth0 host 10.0.0.5 and port 443`.
3. **Decode/Dissect** – break packet into layers (Ethernet → IP → TCP/UDP → Application).
4. **Pattern Matching** – compare payload against signature database.
5. **Anomaly Comparison** – compare against baseline behavior if no signature matches.
6. **Correlation** – tie findings to other logs (firewall, server, auth) for full attack picture.

### 2.3 Common Tools
| Tool | Purpose |
|---|---|
| **Wireshark** | GUI-based deep packet inspection & protocol dissection |
| **tcpdump** | CLI packet capture, scriptable |
| **Snort / Suricata** | Signature + anomaly-based IDS/IPS engines |
| **NetFlow/sFlow/IPFIX** | Flow-level metadata (no payload) for traffic pattern analysis |
| **Zeek (Bro)** | Protocol-aware network security monitoring, generates rich logs |

### 2.4 Deep Packet Inspection (DPI)
DPI examines the **actual data/payload** portion of a packet (not just headers), enabling:
- Application identification regardless of port used
- Malware/exploit signature matching within payload
- Content filtering (e.g., blocking specific file types)
- Data Loss Prevention (DLP) — detecting sensitive data (credit card numbers, etc.) in transit

> **Tricky Q:** Why can DPI-based firewalls struggle with encrypted (TLS) traffic?
> **A:** DPI needs to see **plaintext payload** to pattern-match; encrypted traffic hides the payload unless the device performs **SSL/TLS interception** (man-in-the-middle decryption using a trusted internal CA), which raises performance, privacy, and certificate-pinning compatibility concerns.

---

## Quick Revision – One-Liners
- WAF = Layer 7 firewall specialized for HTTP(S)/web app attacks (SQLi, XSS, CSRF).
- Negative model = blacklist known-bad; Positive model = whitelist known-good (stronger vs zero-day).
- Packet signature = known malicious byte pattern; vulnerability-facing > exploit-facing (broader coverage).
- DPI inspects payload, not just headers — but needs decryption to see inside TLS.
- Tools: Wireshark/tcpdump (capture), Snort/Suricata (signature IDS/IPS), NetFlow (flow metadata only).
