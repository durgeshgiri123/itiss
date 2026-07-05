# DMZ & Virtual Hosts, Unified Threat Management, Threat Hunting, and Reverse Proxy

## 1. DMZ (Demilitarized Zone)

A DMZ is a **perimeter/buffer network segment** placed between an internal trusted network and an untrusted external network (internet), hosting public-facing services (web, mail, DNS servers) so that if compromised, the attacker still doesn't have direct access to the internal LAN.

### 1.1 DMZ Architectures
| Architecture | Description |
|---|---|
| **Single Firewall (Three-legged)** | One firewall with 3 interfaces: internal, DMZ, external. Simpler but single point of failure/misconfiguration risk |
| **Dual/Screened Subnet Firewall** | Two firewalls — external firewall (internet ↔ DMZ) and internal firewall (DMZ ↔ internal LAN). More secure — a breach of DMZ still faces a second firewall before reaching internal LAN |

> **Tricky Q:** Why is the dual-firewall (screened subnet) DMZ architecture considered more secure than a single three-legged firewall?
> **A:** With a single firewall, **one misconfiguration or compromise** exposes both DMZ and internal segments through the same device. A dual-firewall design forces an attacker who breaches the DMZ to defeat a **second, independent** firewall (ideally from a different vendor to avoid shared vulnerabilities) before reaching the internal network — defense in depth.

### 1.2 What Goes in a DMZ?
- Public web servers, mail relay servers, public DNS servers, FTP servers, reverse proxies, VPN termination points.
- **Never** place internal databases with sensitive data directly in the DMZ — DMZ servers should only hold what's necessary for the public-facing function.

### 1.3 Virtual Host
A **virtual host** allows a single physical server (or firewall/VPN appliance) to host **multiple logical domains/services**, each isolated by configuration (e.g., Apache/Nginx serving multiple websites on one IP using `Host` header-based routing, or a VPN device supporting multiple isolated virtual VPN instances/tenants).

- **Name-based virtual hosting** – multiple domains share one IP, differentiated by the HTTP `Host` header (doesn't work well pre-TLS-SNI for HTTPS).
- **IP-based virtual hosting** – each site has a unique IP on the same physical server.
- In firewall/security context: **virtual firewall/virtual context** — a single physical firewall appliance partitioned into multiple independent virtual firewalls, each with its own policies (useful for multi-tenant/MSSP environments).

> **Tricky Q:** Why did name-based virtual hosting have trouble with HTTPS before SNI (Server Name Indication)?
> **A:** The TLS handshake (choosing which certificate to present) happens **before** the encrypted HTTP request (containing the `Host` header) is visible — the server didn't know which domain/certificate to serve. **SNI** extension solves this by including the requested hostname in the **unencrypted** part of the TLS ClientHello, letting the server pick the right certificate for that virtual host.

---

## 2. Unified Threat Management (UTM)

UTM is an **all-in-one security appliance** consolidating multiple security functions into a single device/platform:
- Firewall (stateful/NGFW)
- IDS/IPS
- Antivirus/Anti-malware gateway
- VPN (site-to-site & remote access)
- Content/URL filtering
- Anti-spam
- Data Loss Prevention (DLP)
- Application control

### 2.1 UTM vs NGFW
| | UTM | NGFW |
|---|---|---|
| Focus | Broad — consolidates many security *functions* (AV, spam, content filter, VPN, firewall) mainly for **SMB/branch offices** | Focus — deep app/user awareness, DPI, IPS, primarily for **enterprise perimeter/datacenter** performance at scale |
| Performance | Can suffer if many modules enabled simultaneously (all inline, single box) | Typically optimized hardware/ASICs for high-throughput deep inspection |

> **Tricky Q:** Why might a large enterprise data center avoid using UTM despite its convenience?
> **A:** UTM bundles many inspection engines (AV, spam filter, IPS, content filter) into a **single appliance processing everything inline**, which can create a **performance bottleneck** and a single point of failure at high traffic volumes; large enterprises prefer distributing functions across dedicated best-of-breed devices (dedicated NGFW + separate IPS + separate email security gateway) for scalability and resilience.

### 2.2 Pros/Cons of UTM
- **Pros:** Simplified management (single console), lower cost for SMBs, faster deployment.
- **Cons:** Single point of failure, potential performance degradation, "jack of all trades, master of none" — may lag behind specialized point solutions in depth.

---

## 3. Threat Hunting

**Threat hunting** is the **proactive, human-led** process of searching through networks/systems to detect and isolate advanced threats that evade existing automated security tools (unlike reactive alert-driven monitoring).

### 3.1 Threat Hunting Models/Approaches
1. **Hypothesis-Driven Hunting** – hunter forms a hypothesis (e.g., "an attacker may be using PowerShell for lateral movement") based on threat intelligence, then investigates supporting/refuting evidence.
2. **Indicator of Compromise (IOC)-Based Hunting** – search for known IOCs (malicious hashes, IPs, domains) from threat intel feeds across the environment.
3. **TTP-Based Hunting (using MITRE ATT&CK framework)** – hunt for known adversary **Tactics, Techniques, and Procedures** rather than static indicators (more resilient — TTPs change less often than IOCs).
4. **Analytics/ML-Driven (Data-Driven) Hunting** – use statistical/ML anomaly detection across large datasets to surface unusual patterns for investigation.

> **Tricky Q:** Why is TTP-based hunting (via MITRE ATT&CK) considered more effective/durable than pure IOC-based hunting?
> **A:** IOCs (specific IPs, file hashes, domains) are **easy for attackers to change** — a single infrastructure swap invalidates the indicator ("IOCs are perishable"). TTPs describe **behavioral patterns** (e.g., "uses WMI for lateral movement," "abuses scheduled tasks for persistence") that are **much harder and costlier for adversaries to change**, making detections built around TTPs far more durable over time — this is the essence of the "Pyramid of Pain" concept.

### 3.2 The Threat Hunting Process (Loop)
1. **Trigger** – a hypothesis, anomaly, or intel feed prompts a hunt.
2. **Investigation** – using tools (SIEM, EDR, packet capture) to test the hypothesis.
3. **Uncover new patterns/TTPs** (if found).
4. **Enrichment & Response** – feed findings back into automated detection (create new signatures/analytics), and hand off confirmed incidents to the IR team.

---

## 4. Reverse Proxy

### 4.1 Introduction
A **reverse proxy** sits in front of one or more backend servers and forwards client requests to them, returning the server's response to the client **as if it came from the proxy itself**. This is the opposite of a forward proxy, which sits in front of clients and represents them to external servers.

| | Forward Proxy | Reverse Proxy |
|---|---|---|
| Represents | The **client** to the outside world | The **server(s)** to the outside world |
| Typical use | Corporate internet access control, anonymity, caching for internal users | Load balancing, SSL termination, protecting/hiding backend servers |

### 4.2 Reverse Proxy Policies/Functions
- **Load Balancing** – distribute requests across multiple backend servers (round robin, least connections, IP hash).
- **SSL/TLS Termination** – decrypt HTTPS at the proxy, reducing load on backend servers.
- **Caching** – serve frequently requested content without hitting backend every time.
- **Compression** – reduce payload size to clients.
- **Security/Obfuscation** – hides backend server topology/IPs/versions from clients, reducing attack surface.
- **Web Application Firewall integration** – many reverse proxies (Nginx, F5, Cloudflare) integrate WAF rules.
- **Authentication offloading** – centralize auth checks before requests reach backend apps.
- **Access control policies** – IP whitelisting/blacklisting, rate limiting per client.

> **Tricky Q:** Why is a reverse proxy considered a security benefit even without a WAF module?
> **A:** It **hides internal server details** (real IPs, server software/version banners, internal architecture) from external clients — an attacker interacting with the proxy can't directly fingerprint or reach the backend servers, reducing the attack surface and making reconnaissance/direct attacks harder, independent of any content-filtering feature.

> **Tricky Q:** How does SSL termination at a reverse proxy affect DPI-based security tools sitting further inside the network?
> **A:** Once SSL is terminated (decrypted) at the reverse proxy, traffic **from the proxy to backend servers may travel in plaintext** (or be re-encrypted) — internal DPI/IDS tools placed **behind** the proxy can inspect payload content easily if plaintext, but if re-encrypted, they again lose visibility unless they too can decrypt — an important architectural consideration when placing inspection tools relative to the proxy.

---

## Quick Revision – One-Liners
- DMZ = buffer zone for public-facing servers; dual-firewall screened subnet > single three-legged firewall.
- Virtual host = multiple logical services/domains on one physical device; SNI solves HTTPS + name-based hosting.
- UTM = all-in-one (AV+IPS+VPN+content filter); good for SMB, can bottleneck at enterprise scale.
- Threat hunting = proactive, human-led; TTP-based (MITRE ATT&CK) > IOC-based for durability ("Pyramid of Pain").
- Reverse proxy represents servers to clients (opposite of forward proxy); provides load balancing, SSL termination, caching, and hides backend topology.
