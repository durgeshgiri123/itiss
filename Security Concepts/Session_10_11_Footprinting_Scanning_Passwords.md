# Sessions 10–11: Footprinting, Scanning, Enumeration & Password Cracking

> **Exam Weight:** Very High | **Type:** Theory + Lab

---

# SESSION 10: Footprinting, Scanning & Reconnaissance

## 1. Footprinting (Reconnaissance)

### 1.1 What is Footprinting?
Collecting information about a target system/organization **before attacking**. Also called **Reconnaissance**.

**Goal:** Build a map of the target — IPs, domains, technologies, personnel, network topology.

### 1.2 Passive vs Active Reconnaissance

| | Passive | Active |
|--|---------|--------|
| Contact with target? | No | Yes |
| Detection risk | Very low | Higher |
| Examples | WHOIS, Google Dorks, Shodan | Port scanning, banner grabbing |
| Legality | Always legal | May be illegal without permission |

### 1.3 Passive Footprinting Techniques

#### WHOIS Lookup
Queries domain registration databases:
```
Information obtained:
- Domain registrant name, email, phone
- Registration and expiration dates
- Name servers
- Registrar information

Tools: whois (CLI), who.is, domaintools.com
```
> **Defense:** WHOIS privacy protection / domain privacy services

#### DNS Reconnaissance
```bash
# DNS lookup types
nslookup domain.com           # A record (IP)
nslookup -type=MX domain.com  # Mail servers
nslookup -type=NS domain.com  # Name servers
nslookup -type=TXT domain.com # SPF/DMARC records

# DNS Zone Transfer (AXFR) — gets ALL DNS records!
# Should be blocked but sometimes misconfigured:
dig axfr @ns1.domain.com domain.com
nslookup
> server ns1.domain.com
> set type=any
> ls -d domain.com
```

> **Tricky Q:** What is a DNS Zone Transfer and why is it dangerous?
> **A:** AXFR (Authoritative Transfer) copies all DNS records from a name server. If not restricted, attackers can get complete list of all hostnames/IPs in a domain — a complete network map.

#### Google Dorks (Google Hacking)
Using advanced Google search operators to find sensitive info:

| Dork | Finds |
|------|-------|
| `site:target.com` | All pages on a domain |
| `filetype:pdf site:target.com` | PDFs on the site |
| `intitle:"index of" site:target.com` | Open directory listings |
| `inurl:admin site:target.com` | Admin pages |
| `filetype:sql "password"` | SQL files with passwords |
| `intitle:"login" site:target.com` | Login pages |
| `"confidential" filetype:doc site:target.com` | Confidential docs |

> **Tricky Q:** What database contains indexed Google Dork queries?
> **A:** Google Hacking Database (GHDB) maintained by Offensive Security / Exploit-DB

#### Shodan
- Search engine for **internet-connected devices** (IoT, servers, webcams)
- Scans all of internet and indexes banners/services
- Can find: exposed databases, default-credential devices, vulnerable services

```
Shodan queries:
port:22 country:IN            # SSH servers in India
"default password" port:23    # Telnet with default passwords
product:"Apache httpd" 2.4.49 # Vulnerable Apache version
```

#### Netcraft
- Shows hosting history, technologies used by a website
- OS, web server, ISP, IP changes over time
- Site Risk Rating

#### Recon-ng Framework
- Modular reconnaissance framework (similar to Metasploit)
- Automates OSINT gathering
- Modules for: WHOIS, DNS, social media, Shodan, etc.

### 1.4 Traceroute in Footprinting
```bash
traceroute target.com  # Linux
tracert target.com     # Windows

# Shows path packets take (each hop's IP and latency)
# Reveals network topology, ISPs, potential firewall locations
# Gaps in hops = firewall dropping TTL-expired packets
```

> **Tricky Q:** How does traceroute work?
> **A:** Sends packets with incrementing TTL values (1, 2, 3...). Each router decrements TTL by 1 and when it reaches 0, sends back "TTL Exceeded" ICMP message, revealing that router's IP.

### 1.5 FOCA (Fingerprinting Organizations with Collected Archives)
- Extracts **metadata** from documents (PDF, DOCX, XLS, etc.)
- Metadata can reveal: usernames, software versions, internal paths, GPS coordinates, emails
- Example: A PDF uploaded to website may contain author's username, computer name, internal file path

---

## 2. Port Scanning & Network Scanning

### 2.1 Definitions
- **Port Scanning** — finding open ports on a host
- **Network Scanning** — discovering active hosts on network
- **Vulnerability Scanning** — finding known vulnerabilities on discovered services

### 2.2 NMAP — Complete Reference (EXAM CRITICAL)

#### Host Discovery
```bash
nmap -sn 192.168.1.0/24     # Ping scan (host discovery, no port scan)
nmap -PR 192.168.1.1        # ARP ping (LAN only, most reliable)
nmap -PE 192.168.1.1        # ICMP Echo ping
nmap -sn -PS22,80 <target>  # TCP SYN ping on specific ports
```

#### Scan Types
```bash
nmap -sS target   # SYN scan (half-open, stealthy, default for root)
nmap -sT target   # TCP Connect scan (full connection, logged)
nmap -sU target   # UDP scan
nmap -sA target   # ACK scan (firewall rule mapping)
nmap -sF target   # FIN scan
nmap -sX target   # XMAS scan (FIN+PSH+URG flags)
nmap -sN target   # NULL scan (no flags)
nmap -sI zombie target  # Idle/IPID scan (completely stealthy)
```

#### Service/Version Detection
```bash
nmap -sV target             # Version detection
nmap -O target              # OS detection
nmap -A target              # Aggressive: OS+version+scripts+traceroute
nmap -sC target             # Default NSE scripts
nmap --script vuln target   # Vulnerability scripts
```

#### Output Formats
```bash
nmap -oN output.txt target  # Normal output
nmap -oX output.xml target  # XML output
nmap -oG output.grep target # Grepable output
nmap -oA output target      # All formats
```

### 2.3 Scan Types — Deep Dive

#### SYN Scan (Half-Open Scan)
```
Attacker → SYN → Target (port open: Target replies SYN-ACK)
Attacker → RST → Target (attacker resets WITHOUT completing handshake)

Advantages: Not logged by many systems (no full connection)
Requires: Root/Admin privileges
```

#### XMAS Scan
```
Packet has FIN + PSH + URG flags set (all "lit up" like Christmas tree)
Open port:   No response
Closed port: RST + ACK response
Filtered:    No response (or ICMP unreachable)

Does NOT work on Windows (Windows sends RST regardless)
```

#### NULL Scan
```
Packet has NO flags set
Open port:   No response
Closed port: RST + ACK
Works only on Unix/Linux targets
```

#### FIN Scan
```
Only FIN flag set
Open port:   No response (ignores FIN without connection)
Closed port: RST + ACK
Evades many firewalls that only filter SYN
```

#### IDLE/IPID Scan (Most Stealthy)
```
Uses a "zombie" host (idle machine with predictable IPID)
Attacker probes zombie's IPID → sends SYN to target spoofed as zombie
→ If target port open: target sends SYN-ACK to zombie → zombie RSTs → IPID increments by 2
→ If target port closed: no IPID increment
Attacker infers port state from zombie IPID changes
(Attacker's IP never directly contacts target!)
```

> **Tricky Q:** Which Nmap scan is the most stealthy and why?
> **A:** IDLE scan — the attacker's IP never directly contacts the target. A zombie host is used as an intermediary, and port state is inferred from changes in the zombie's IPID counter.

> **Tricky Q:** Why does XMAS scan not work on Windows?
> **A:** Windows doesn't follow RFC 793 strictly — it sends RST for all unexpected packets regardless of port state, making open/closed indistinguishable.

### 2.4 OSINT Tools Summary

| Tool | Purpose |
|------|---------|
| **WHOIS** | Domain registration info |
| **Netcraft** | Web tech, hosting history |
| **Shodan** | Internet device search engine |
| **Google Dorks** | Sensitive info via search operators |
| **Recon-ng** | Automated OSINT framework |
| **Maltego** | Visual link analysis (people, domains, IPs) |
| **theHarvester** | Email, hostname, IP harvesting |
| **SpiderFoot** | Automated OSINT for many data sources |
| **FOCA** | Document metadata extraction |

---

# SESSION 11: Enumeration, Password Cracking & Network Attacks

## 1. Enumeration

### 1.1 What is Enumeration?
**Extracting detailed information** from discovered services: usernames, shares, groups, routing tables, SNMP info.

**Difference from Scanning:** Scanning finds what's OPEN. Enumeration finds what's INSIDE.

### 1.2 NetBIOS Enumeration
```bash
# NetBIOS = Network Basic Input/Output System
# Port 137 (UDP) - Name Service
# Port 138 (UDP) - Datagram Service
# Port 139 (TCP) - Session Service

nbtstat -A 192.168.1.1        # Windows — NetBIOS table of remote host
nbtscan 192.168.1.0/24        # Scan network for NetBIOS names
net view \\target             # List shares on target
```

### 1.3 SMB Enumeration
```bash
# SMB (Server Message Block) = Windows file sharing
# Port 445 (TCP) - Direct SMB over TCP
# Port 139 (TCP) - SMB over NetBIOS

enum4linux -a 192.168.1.1     # Full Linux SMB enumeration
smbclient -L //192.168.1.1    # List shares
smbmap -H 192.168.1.1         # Map shares with permissions
```

### 1.4 SNMP Enumeration
```bash
# SNMP (Simple Network Management Protocol)
# Port 161 UDP (agent), 162 UDP (trap)
# Community strings = passwords (default: "public" read, "private" write)

snmpwalk -v1 -c public 192.168.1.1   # Walk MIB tree
snmpcheck 192.168.1.1                 # Check SNMP info
onesixtyone -c /wordlist.txt target  # Brute force community strings

# SNMP MIB Objects of interest:
# 1.3.6.1.2.1.25.4.2.1.2  → Running processes
# 1.3.6.1.2.1.25.6.3.1.2  → Installed software
# 1.3.6.1.4.1.77.1.2.25   → User accounts
```

> **Tricky Q:** What is the default SNMP community string?
> **A:** "public" for read access, "private" for write access. These are well-known defaults and should always be changed.

### 1.5 DNS Enumeration
```bash
# Zone transfer (AXFR)
dig axfr @nameserver domain.com
# Brute force subdomains
dnsenum domain.com
dnsrecon -d domain.com
fierce --domain domain.com
```

### 1.6 LDAP Enumeration
```bash
# LDAP (Lightweight Directory Access Protocol) - Port 389, 636(TLS)
# Active Directory uses LDAP
ldapsearch -x -h 192.168.1.1 -b "dc=domain,dc=com"
```

---

## 2. Password Cracking

### 2.1 Password Cracking Methods

| Method | Description | Speed | Requirement |
|--------|-------------|-------|------------|
| **Dictionary Attack** | Try words from wordlist | Fast | Good wordlist |
| **Brute Force** | Try every combination | Very Slow | Time |
| **Hybrid Attack** | Dictionary + rules (add numbers, symbols) | Medium | Wordlist + rules |
| **Rainbow Table** | Pre-computed hash→password lookup | Instant | Large storage |
| **Credential Stuffing** | Use leaked credentials from breaches | Fast | Leaked DB |
| **Password Spraying** | One common password tried on many accounts | Slow | Avoids lockout |
| **Pass-the-Hash** | Use hash directly (no need to crack) | Instant | Hash |

> **Tricky Q:** What is the difference between a dictionary attack and brute force?
> **A:** Dictionary uses a pre-defined wordlist of likely passwords. Brute force tries every possible character combination. Dictionary is faster but misses custom passwords; brute force is exhaustive but very slow.

> **Tricky Q:** What is Pass-the-Hash?
> **A:** In Windows/NTLM, the hash itself is used for authentication. If an attacker captures the NTLM hash, they can authenticate without knowing the actual password.

### 2.2 Rainbow Tables
```
Pre-computed table of hash → plaintext pairs

Normal cracking: Password → Hash → Compare (repeated for each guess)
Rainbow table:   Look up hash → get password immediately

Defeated by SALTING:
Salt = random value added before hashing
Password "abc123" + Salt "xyz" → Hash("xyzabc123")
Same password, different salt → different hash
Rainbow tables useless against salted hashes!
```

> **Tricky Q:** What is a "salt" in password hashing and why is it important?
> **A:** A random value added to the password before hashing. Makes rainbow table attacks impractical since every hash is unique even for identical passwords.

### 2.3 Password Cracking Tools

| Tool | Best For | Method |
|------|---------|--------|
| **John the Ripper** | Offline hash cracking | Dictionary, brute force, hybrid |
| **Hashcat** | GPU-accelerated cracking | All methods, fastest for GPU |
| **Hydra** | Online attacks (HTTP, SSH, FTP) | Dictionary, brute force |
| **Medusa** | Online service attacks | Parallel brute force |
| **Aircrack-ng** | WPA/WEP WiFi password cracking | PMK attacks |
| **CeWL** | Custom wordlist generator | Spider website for wordlist |

```bash
# John the Ripper
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --format=NT hash.txt    # Windows NTLM

# Hashcat
hashcat -m 0 hash.txt wordlist.txt          # MD5
hashcat -m 1000 hash.txt wordlist.txt       # NTLM
hashcat -m 1800 hash.txt wordlist.txt       # SHA-512 (Linux)
hashcat -a 3 hash.txt ?a?a?a?a?a?a         # Brute force 6 chars

# Hydra (online)
hydra -l admin -P wordlist.txt ssh://192.168.1.1
hydra -L users.txt -P passwords.txt http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"
```

### 2.4 Windows Password Storage

| Storage | Format | Notes |
|---------|--------|-------|
| **SAM database** | NTLM hash | `C:\Windows\System32\config\SAM` |
| **NTDS.dit** | NTLM hash | Active Directory domain controller |
| **LSA Secrets** | Various | Service account passwords |
| **Cached credentials** | MS-Cache | Last 10 domain logins cached locally |

---

## 3. SMB Relay & MITM Attacks

### 3.1 SMB Relay Attack
```
Normal: Client → authenticates to → Server (with NTLM hash)

Relay: Client → NTLM hash → Attacker (Responder) → relays hash to → Server
              → Server authenticates → Attacker gets access!
```

**Responder** tool: poisons LLMNR/NBT-NS queries to capture NTLM hashes

### 3.2 IP Spoofing
- **Forging the source IP** address in packets
- Used in: DDoS amplification, IDLE scan, trust exploitation
- TCP connections can't be maintained with spoofed IPs (can't receive reply)
- UDP/ICMP attacks work well with spoofing

### 3.3 HTTP Tunneling
- Encapsulating non-HTTP traffic inside HTTP to bypass firewalls
- Tools: HTTPTunnel, NCAT, Meterpreter over HTTP/HTTPS
- Example: SSH traffic wrapped in HTTP headers on port 80

### 3.4 Proxy Servers in Attacks
- **Anonymity:** Hide attacker's real IP
- **Chain of proxies:** Multiple hops make tracing harder
- **Tools:** ProxyChains, Tor, VPN
- **TOR:** Routes through 3 encrypted relay nodes; exit node sees traffic

---

## 4. Nessus — Vulnerability Scanner
```
Nessus = Industry-standard vulnerability scanner

What it does:
- Discovers hosts and open ports
- Identifies service versions
- Checks against 60,000+ vulnerability plugins
- Produces risk-rated reports

Scan types:
- Basic network scan
- Credentialed scan (with credentials → more thorough)
- Web application scan
- PCI DSS compliance scan

Output: Severity levels: Critical, High, Medium, Low, Info
```

---

## 5. Exam Questions Bank

**Q1:** What information can you gather from WHOIS?
**A:** Domain registrant name/email/phone, registration date, expiration date, name servers, registrar

**Q2:** What is the purpose of DNS Zone Transfer (AXFR)?
**A:** Copies all DNS records from a name server — attackers use it to map all hosts in a domain if misconfigured to allow transfers from any IP

**Q3:** What does the -sS flag in NMAP do?
**A:** SYN scan (half-open/stealth scan) — sends SYN, receives SYN-ACK for open ports, then sends RST without completing handshake

**Q4:** What does -A flag do in NMAP?
**A:** Aggressive scan — enables OS detection (-O), version detection (-sV), script scanning (-sC), and traceroute

**Q5:** Which port does SMB use directly (without NetBIOS)?
**A:** Port 445 TCP

**Q6:** What tool captures NTLM hashes via LLMNR/NBT-NS poisoning?
**A:** Responder

**Q7:** How does salting defeat rainbow table attacks?
**A:** Salt adds a unique random value to each password before hashing, making pre-computed rainbow tables useless since every hash is unique

**Q8:** What is password spraying and why is it used?
**A:** Using one common password (like "Password1!") against many accounts. Avoids account lockout since each account only gets one attempt.

**Q9:** What is the default community string for SNMP read access?
**A:** "public"

**Q10:** What is credential stuffing?
**A:** Using username/password combinations leaked from one breach to attempt login to other sites, exploiting password reuse

---

*Next: Sessions 12–13 → Trojans, Backdoors, Rootkits*
