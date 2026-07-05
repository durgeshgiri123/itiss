# Security Fundamentals & Firewalls

## 1. Security Fundamentals

### 1.1 CIA Triad
| Element | Meaning | Failure Example |
|---|---|---|
| **Confidentiality** | Only authorized entities can view data | Data leak, sniffing |
| **Integrity** | Data is not altered without authorization | MITM tampering, checksum mismatch |
| **Availability** | Systems/data accessible when needed | DoS/DDoS attack |

Extended models add **AAA** (Authentication, Authorization, Accounting) and sometimes **Non-repudiation**.

### 1.2 Key Definitions (frequently confused in exams)
- **Asset** – anything of value (data, server, reputation).
- **Vulnerability** – a weakness that can be exploited (unpatched OS).
- **Threat** – a potential danger that exploits a vulnerability (hacker, malware).
- **Risk** – probability × impact of a threat exploiting a vulnerability.
- **Exploit** – the actual code/technique used to take advantage of a vulnerability.
- **Attack Vector** – the path/means by which an attacker gains access.
- **Attack Surface** – the sum of all points where an attacker can try to enter.

> **Tricky Q:** Is a threat the same as a risk?
> **A:** No. Threat = potential danger (independent of probability). Risk = likelihood × impact, i.e., a *quantified/qualified* measure combining threat + vulnerability + asset value.

### 1.3 Security Controls
- **Preventive** – firewalls, ACLs, encryption
- **Detective** – IDS, logs, audits
- **Corrective** – patches, backups, IPS blocking
- **Deterrent** – warning banners, CCTV
- **Compensating** – alternate control when primary control isn't feasible

---

## 2. Firewalls

### 2.1 Definition
A firewall is a network security device/software that monitors and controls incoming and outgoing traffic based on predetermined security rules, establishing a barrier between trusted and untrusted networks.

### 2.2 Firewall Generations / Types

| Type | Layer (OSI) | Working | Pros | Cons |
|---|---|---|---|---|
| **Packet Filtering** | Layer 3/4 | Checks src/dst IP, port, protocol against ACL rules; stateless | Fast, simple, cheap | No context/session awareness, vulnerable to spoofing |
| **Stateful Inspection** | Layer 3/4 | Maintains a state table of active connections | Tracks connection state, blocks unsolicited replies | More resource-intensive, still limited app-layer visibility |
| **Circuit-Level Gateway** | Layer 5 (Session) | Validates TCP handshake/session legitimacy, doesn't inspect payload | Hides internal network, low overhead | No content inspection |
| **Application-Level Gateway (Proxy)** | Layer 7 | Terminates and re-establishes connection, inspects application data | Deep content control, can filter commands (e.g., FTP PUT/GET) | Slower, needs a proxy per protocol |
| **Next-Generation Firewall (NGFW)** | Layer 3–7 | Combines stateful inspection + DPI + IPS + app awareness | Best visibility & control | Costly, complex to tune |

> **Tricky Q:** What's the key difference between a packet-filtering firewall and a stateful firewall?
> **A:** Packet filter evaluates **each packet in isolation** (no memory of past packets); stateful firewall maintains a **connection/state table** and allows return traffic only if it matches an established session — this defeats simple spoofed-ACK attacks that fool stateless filters.

> **Tricky Q:** Can a circuit-level gateway stop a SQL injection attack?
> **A:** No — it only validates session/handshake legitimacy, not payload content. You need an application-layer firewall/WAF for that.

### 2.3 Firewall Deployment Architectures
- **Screening router** (border router with ACLs)
- **Dual-homed host** (two NICs, no routing between them by default)
- **Screened host** – firewall + bastion host
- **Screened subnet (DMZ)** – two firewalls sandwiching a buffer network

---

## 3. Linux Firewall – iptables

### 3.1 Core Concepts
iptables is the userspace utility to configure the Linux kernel's **Netfilter** framework.

**Tables** (each table = purpose):
| Table | Purpose |
|---|---|
| `filter` | Default table — ALLOW/DENY packets |
| `nat` | Network Address Translation (SNAT/DNAT/MASQUERADE) |
| `mangle` | Packet header modification (TTL, TOS, marking) |
| `raw` | Exempt packets from connection tracking (NOTRACK) |
| `security` | SELinux marking (rarely tested) |

**Chains** (each chain = hook point):
| Chain | Fires when |
|---|---|
| `INPUT` | Packet destined for the local machine |
| `OUTPUT` | Packet generated locally, going out |
| `FORWARD` | Packet routed through the machine (not for itself) |
| `PREROUTING` | Before routing decision (used in `nat`, `mangle`) |
| `POSTROUTING` | After routing decision, before leaving interface (used in `nat`) |

### 3.2 Common Commands
```bash
# List rules with line numbers
iptables -L -n -v --line-numbers

# Default policy: drop everything, then explicitly allow
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow established/related connections (stateful)
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow SSH only from a specific subnet
iptables -A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT

# Block a specific IP
iptables -A INPUT -s 203.0.113.5 -j DROP

# NAT - Masquerade for internet sharing
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# Port forwarding (DNAT)
iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.10:80

# Save/restore rules (Debian/Ubuntu)
iptables-save > /etc/iptables/rules.v4
iptables-restore < /etc/iptables/rules.v4
```

### 3.3 Packet Flow Order (VERY commonly asked)
```
Incoming packet → PREROUTING (raw→mangle→nat)
   → routing decision →
     If for local machine: INPUT (mangle→filter→security→nat) → Local Process
     If to be forwarded: FORWARD (mangle→filter→security) → POSTROUTING (mangle→nat) → out
Locally generated packet → OUTPUT (raw→mangle→nat→filter→security) → POSTROUTING → out
```

> **Tricky Q:** Which chain would you use to block traffic being routed THROUGH a Linux box acting as a router (not destined to the box itself)?
> **A:** `FORWARD` chain — `INPUT`/`OUTPUT` apply only to traffic to/from the local machine itself.

> **Tricky Q:** Why put an ACCEPT rule for `ESTABLISHED,RELATED` near the TOP of the INPUT chain?
> **A:** iptables processes rules top-down and stops at first match; placing the stateful-accept rule early avoids re-evaluating every packet of already-approved sessions against the entire ruleset — improves performance and correctness.

### 3.4 nftables (modern successor)
- Exam trap: **nftables replaces iptables** in newer distros (single framework instead of iptables/ip6tables/arptables/ebtables), uses different syntax (`nft add rule ...`), but conceptually similar (tables → chains → rules).

---

## 4. Next-Generation Firewall (NGFW) – Overview

### 4.1 Features Beyond Traditional Firewalls
- **Deep Packet Inspection (DPI)** – inspects payload, not just headers
- **Application Awareness/Control** – identifies apps regardless of port (e.g., Skype over port 443)
- **Integrated IPS** – signature and anomaly-based intrusion prevention
- **User Identity Awareness** – ties traffic to AD/LDAP user, not just IP
- **SSL/TLS Inspection** – decrypts and inspects encrypted traffic
- **Threat Intelligence Feeds** – real-time reputation/IOC feeds
- **Sandboxing** – detonates unknown files in isolated environment

> **Tricky Q:** Why is a traditional stateful firewall insufficient against modern threats?
> **A:** It can only see IP/port/protocol/state — it cannot detect malicious *content* inside allowed ports (e.g., malware in HTTP traffic on port 80/443), nor identify the actual application or user, which NGFWs can.

---

## 5. Limitations of Firewalls

- Cannot stop attacks that **don't pass through it** (insider threats, rogue Wi-Fi, USB malware).
- Cannot inspect **encrypted traffic** without SSL inspection (and this adds latency/privacy concerns).
- Cannot prevent **social engineering** or **phishing** (human factor).
- Vulnerable to **misconfiguration** (overly permissive rules).
- Ineffective against **application-layer attacks** if it's only a Layer 3/4 device.
- Cannot detect **malware already inside the network** (needs IDS/IPS/EDR).
- Single point of failure if not deployed in HA (high availability) pair.
- Cannot stop **DDoS** at scale alone (needs upstream scrubbing/CDN).

> **Tricky Q:** "A firewall alone is a complete security solution." True or False? Justify.
> **A:** False. Defense-in-depth requires IDS/IPS, endpoint security, encryption, patching, and user awareness — firewall is only the perimeter control layer.

---

## Quick Revision – One-Liners
- Firewall = perimeter gatekeeper based on rules.
- Stateful > Stateless because it tracks sessions.
- iptables tables: filter (default), nat, mangle, raw.
- iptables chains: INPUT, OUTPUT, FORWARD, PRE/POSTROUTING.
- NGFW = Stateful + DPI + IPS + App-ID + User-ID.
- Firewalls can't see inside encrypted traffic without SSL inspection.
