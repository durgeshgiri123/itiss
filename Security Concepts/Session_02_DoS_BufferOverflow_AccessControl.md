# Session 2: Denial of Service, Buffer Overflows, Input Validation & Access Control

> **Exam Weight:** High | **Type:** Theory + Lab

---

## 1. Denial of Service (DoS)

### 1.1 Definition
A **Denial of Service (DoS)** attack makes a resource (server, network, application) **unavailable to legitimate users** by overwhelming it with requests or exploiting vulnerabilities.

**Key Principle:** Availability (CIA triad) is targeted.

### 1.2 Types of DoS Attacks

#### Volume-Based Attacks (Bandwidth Flooding)
- Overwhelm bandwidth with massive traffic
- Examples: UDP Flood, ICMP Flood, Ping of Death
- Measured in **Gbps**

#### Protocol Attacks (State-Exhaustion)
- Exploit weaknesses in Layer 3/4 protocols
- Consume server/firewall resources (connection tables)
- Examples: **SYN Flood**, Smurf Attack, Fragmentation attacks
- Measured in **Packets per second (pps)**

#### Application Layer Attacks (Layer 7)
- Target specific application features
- Harder to detect (looks like legitimate traffic)
- Examples: HTTP Flood, Slowloris, R.U.D.Y.
- Measured in **Requests per second (rps)**

### 1.3 DoS vs DDoS

| | DoS | DDoS |
|---|-----|------|
| Source | Single machine | Multiple machines (botnet) |
| Volume | Lower | Extremely high |
| Traceability | Easier | Very difficult |
| Defense | Block single IP | Rate limiting, scrubbing centers |

> **Tricky Q:** Why is DDoS harder to defend than DoS?
> **A:** Attacks come from thousands of legitimate-looking IPs (botnets), making IP blocking ineffective.

### 1.4 SYN Flood Attack (Critical for Exams)

**Normal TCP 3-way Handshake:**
```
Client → SYN → Server
Client ← SYN-ACK ← Server
Client → ACK → Server
[Connection Established]
```

**SYN Flood:**
```
Attacker → SYN (spoofed IP) → Server
           ← SYN-ACK ← Server (waits for ACK that never comes)
Server holds half-open connection in backlog until timeout
[Server's connection table fills up → legitimate connections rejected]
```

**Defense:** SYN Cookies — server doesn't allocate resources until full handshake completes.

### 1.5 Smurf Attack
- Attacker sends ICMP Echo Request to **broadcast address** with **spoofed victim IP**
- All hosts on network reply to victim → amplification
- Amplification factor = number of hosts on network
- **Defense:** Disable IP broadcast on routers

### 1.6 Slowloris Attack
- Opens many partial HTTP connections to server
- Sends headers very slowly, keeping connections alive
- Server's max connections reached → legitimate users can't connect
- **Defense:** Connection timeouts, rate limiting per IP

### 1.7 Ping of Death
- Sends oversized ICMP packets (>65,535 bytes)
- Causes buffer overflow on receiving host → crash
- Mostly patched in modern OS

> **Tricky Q:** Which DoS attack exploits the TCP 3-way handshake?
> **A:** SYN Flood

> **Tricky Q:** What layer does Slowloris attack operate on?
> **A:** Application Layer (Layer 7)

---

## 2. Buffer Overflows

### 2.1 What is a Buffer Overflow?
A **buffer** is a fixed-size memory area. A buffer overflow occurs when a program **writes more data than the buffer can hold**, overwriting adjacent memory.

```
Normal:         [AAAAAAAAAA|return_address|other_data]
                     buffer (10 bytes)

Overflow input: [AAAAAAAAAAAAAAAAAAAAA<malicious_code>]
                     overflows → overwrites return address!
```

### 2.2 Why It's Dangerous
- Can **overwrite the return address** on the stack
- Attacker redirects execution to **shellcode** (injected malicious code)
- Results in: **Remote Code Execution (RCE)**, privilege escalation, crash

### 2.3 Types of Buffer Overflows

| Type | Memory Region | Example |
|------|--------------|---------|
| **Stack Overflow** | Stack (local vars, return addr) | Classic `gets()` overflow in C |
| **Heap Overflow** | Heap (dynamic allocation) | `malloc()` abuse |
| **Integer Overflow** | Any | Size calculation wraps around |
| **Format String** | Any | `printf(user_input)` instead of `printf("%s", user_input)` |

### 2.4 Classic C Vulnerable Code
```c
// VULNERABLE
void vulnerable(char *input) {
    char buffer[10];
    strcpy(buffer, input);  // No bounds checking!
}
// Input "AAAAAAAAAAAAAAAA..." overflows buffer[10]
```

### 2.5 Defenses Against Buffer Overflows

| Defense | Mechanism |
|---------|-----------|
| **ASLR** (Address Space Layout Randomization) | Randomizes memory addresses each run |
| **DEP/NX** (Data Execution Prevention / No-eXecute) | Marks memory regions as non-executable |
| **Stack Canaries** | Places a "canary" value before return address; checked before function returns |
| **Safe functions** | Use `strncpy()` instead of `strcpy()`, `fgets()` instead of `gets()` |
| **Bounds checking** | Validate input length before copy |
| **Compiler flags** | `-fstack-protector`, `-D_FORTIFY_SOURCE=2` |

> **Tricky Q:** What is a stack canary?
> **A:** A random value placed on the stack between the buffer and return address. If overwritten by an overflow, the program detects it and terminates before the attacker gains control.

> **Tricky Q:** What is the difference between ASLR and DEP?
> **A:** ASLR randomizes memory addresses (makes guessing harder). DEP/NX prevents execution of code in non-code memory regions (e.g., stack/heap). Both must be bypassed for a successful exploit.

> **Tricky Q:** What is Return-Oriented Programming (ROP)?
> **A:** A technique to bypass DEP by chaining existing code snippets ("gadgets") in memory ending with `RET` instruction, without injecting new code.

### 2.6 Input Validation

#### Types of Validation
| Type | Where | Description |
|------|-------|-------------|
| **Client-side** | Browser | JavaScript validation — fast but bypassable |
| **Server-side** | Server | Essential, authoritative validation |
| **Whitelist** | Both | Allow only known-good input |
| **Blacklist** | Both | Reject known-bad input (weaker) |

> **Tricky Q:** Is client-side input validation sufficient for security?
> **A:** NO. Attackers can bypass it with Burp Suite, curl, or browser DevTools. Server-side validation is mandatory.

#### Validation Techniques
- **Type checking** — is it an integer, string, date?
- **Range checking** — is the value within expected range?
- **Length checking** — is the input too long/short?
- **Format checking** — matches regex pattern (email, phone)?
- **Business logic** — makes sense in context?

---

## 3. Access Control

### 3.1 What is Access Control?
Access control enforces **who can do what** with resources. Broken Access Control is #1 in OWASP 2021.

### 3.2 Types of Access Control

#### Discretionary Access Control (DAC)
- Owner of resource decides permissions
- Example: Unix file permissions (`chmod`)
- Flexible but error-prone

#### Mandatory Access Control (MAC)
- Central authority sets policies
- Users cannot override
- Example: SELinux, military systems
- Most secure but inflexible

#### Role-Based Access Control (RBAC)
- Permissions assigned to **roles**, users assigned to roles
- Example: Admin, Manager, User
- Most common in enterprise apps

#### Attribute-Based Access Control (ABAC)
- Decisions based on attributes (time, location, department)
- Most flexible and granular
- Used in cloud environments

### 3.3 Common Access Control Vulnerabilities

| Vulnerability | Description |
|--------------|-------------|
| **IDOR** (Insecure Direct Object Reference) | Changing `?id=123` to `?id=124` accesses another user's data |
| **Privilege Escalation** | Gaining higher privileges than intended |
| **Horizontal PE** | Accessing same-level resources of another user |
| **Vertical PE** | Gaining admin/higher privilege from lower |
| **Path Traversal** | `../../../../etc/passwd` to access files outside webroot |
| **Forced Browsing** | Directly accessing `/admin` without authorization |

> **Tricky Q:** What is IDOR?
> **A:** Insecure Direct Object Reference — when an app uses user-controllable input (like an ID) to access objects directly without authorization checks.

> **Tricky Q:** What is the difference between horizontal and vertical privilege escalation?
> **A:** Horizontal = accessing another user's data at the same privilege level. Vertical = gaining higher privileges (e.g., user → admin).

### 3.4 Principle of Least Privilege
Users/processes should have **only the minimum permissions** necessary to perform their function.

### 3.5 CIA Triad (Fundamental Concept)

| | What it protects | DoS targets | Buffer overflow targets | Access Control targets |
|---|---|---|---|---|
| **Confidentiality** | Data secrecy | ✗ | Sometimes | ✓ |
| **Integrity** | Data accuracy | ✗ | ✓ | ✓ |
| **Availability** | System uptime | ✓ | ✓ (crash) | ✗ |

---

## 4. Exam Questions Bank

**Q1:** Which CIA property does a DoS attack primarily violate?
**A:** Availability

**Q2:** What unsafe C function is most commonly exploited for buffer overflows?
**A:** `gets()` and `strcpy()` — they don't check buffer length

**Q3:** What does ASLR stand for and what does it do?
**A:** Address Space Layout Randomization — randomizes memory addresses to make exploitation harder

**Q4:** Which OWASP 2021 category covers IDOR?
**A:** A01 – Broken Access Control

**Q5:** What's the amplification factor in a Smurf attack?
**A:** Equal to the number of hosts on the broadcast network (e.g., 500 hosts = 500x amplification)

**Q6:** What defense specifically protects against SYN Flood?
**A:** SYN Cookies — avoids allocating resources until handshake completes

**Q7:** What is a "zero-day" vulnerability?
**A:** A vulnerability unknown to the vendor, for which no patch exists yet

**Q8:** Whitelisting vs Blacklisting — which is better for security?
**A:** Whitelisting — explicitly allow only safe values. Blacklisting can always be bypassed by finding unlisted malicious inputs.

---

*Next: Session 3 → Web App Security Risks, Threat Modeling, Burp Suite*
