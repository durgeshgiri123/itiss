# Spoofing Detection & Prevention, DoS/DDoS Mitigation, and QoS Policy

## 1. Spoofing — Detection & Prevention

Spoofing = **impersonating** a trusted source's identity to gain unauthorized access or trust.

| Type | How it works | Detection | Prevention |
|---|---|---|---|
| **IP Spoofing** | Forging source IP in packet header | Ingress/egress filtering anomalies, unusual TTL | **Ingress filtering** (RFC 2827/BCP38), reverse path forwarding (uRPF) checks |
| **MAC Spoofing** | Changing NIC's MAC to impersonate a device | Duplicate MAC alerts, port-security violations | Port Security (limit MACs per port), 802.1X |
| **ARP Spoofing/Poisoning** | Sending forged ARP replies to poison ARP cache (MITM) | ARP cache inconsistency, tools like ARPwatch | Dynamic ARP Inspection (DAI), static ARP entries, DHCP snooping |
| **DNS Spoofing/Cache Poisoning** | Injecting false DNS responses to redirect traffic | DNS response anomalies, mismatched TTL | DNSSEC, use trusted resolvers, randomize query IDs/source ports |
| **Email/Caller ID Spoofing** | Forging sender info | SPF/DKIM/DMARC failures | SPF, DKIM, DMARC enforcement |

> **Tricky Q:** How does uRPF (Unicast Reverse Path Forwarding) stop IP spoofing?
> **A:** The router checks if the **source IP** of an incoming packet would be routed back out the **same interface** it arrived on; if not, the packet is dropped as likely spoofed (asymmetric/spoofed source).

> **Tricky Q:** Why is ARP spoofing effective even on switched networks (unlike simple sniffing)?
> **A:** Switches forward based on MAC tables; ARP spoofing **poisons the ARP cache** of victims so their traffic is *redirected* to the attacker's MAC (classic MITM) — it doesn't rely on broadcast/hub-based sniffing at all.

---

## 2. DoS vs DDoS

| | DoS | DDoS |
|---|---|---|
| Source | Single attacker/machine | Multiple distributed sources (botnet) |
| Scale | Limited | Massive, harder to block by IP |
| Example | Ping flood from one host | Mirai botnet flooding via thousands of IoT devices |

### 2.1 Attack Categories
1. **Volumetric attacks** – overwhelm bandwidth (UDP flood, ICMP flood, DNS amplification, NTP amplification).
2. **Protocol attacks** – exploit protocol weaknesses (SYN flood, Ping of Death, Smurf attack) — target server/firewall/load-balancer resources (connection tables).
3. **Application-layer attacks** – target Layer 7 (HTTP flood, Slowloris, low-and-slow attacks) — mimic legitimate requests, harder to detect by volume alone.

> **Tricky Q:** Why is a SYN flood attack effective, and how does a SYN cookie defend against it?
> **A:** SYN flood sends many SYNs without completing the 3-way handshake, exhausting the server's half-open connection (backlog) table. **SYN cookies** avoid storing state for half-open connections — the server encodes connection info into the SYN-ACK sequence number itself and only allocates resources once the final ACK returns with a valid cookie, so an attacker who never replies never consumes memory.

> **Tricky Q:** Why is a DNS amplification attack so effective at generating volume?
> **A:** The attacker spoofs the victim's IP as the source of a small DNS query but requests a large record type (e.g., ANY); the DNS resolver sends a **much larger response** to the spoofed (victim) address — an *amplification factor* that multiplies attack bandwidth far beyond what the attacker itself sends.

### 2.2 DDoS/DoS Mitigation Techniques
- **Rate limiting** – cap requests per source/time window.
- **Blackhole routing/null routing** – drop all traffic to the targeted IP (sacrifices availability of that IP but protects the rest of network).
- **Sinkholing** – redirect malicious traffic to a monitored/controlled destination.
- **Traffic scrubbing centers** (e.g., cloud DDoS protection: Cloudflare, Akamai, AWS Shield) – filter malicious traffic before it reaches origin.
- **Anycast routing** – distribute traffic across many geographically dispersed nodes, diluting attack volume.
- **SYN cookies** – protocol-layer defense (above).
- **Ingress/Egress filtering (BCP38)** – block spoofed-source packets at network edge.
- **Web Application Firewall** – filters application-layer floods (HTTP flood, Slowloris).
- **CDN caching** – absorbs read-heavy application-layer floods.
- **Auto-scaling / load balancing** – absorb spikes (partial mitigation only, cost concern).

> **Tricky Q:** Why is blackholing considered a "successful attack" for the attacker even though it "stops" the DDoS?
> **A:** Blackholing drops ALL traffic (legit + malicious) to the target IP — the attacker **still achieves the denial-of-service goal** since the service becomes unreachable; it just protects the rest of the network/infrastructure from collateral damage.

---

## 3. QoS (Quality of Service) Policy

QoS manages network resources to guarantee performance for priority traffic (e.g., VoIP, video conferencing) over less time-sensitive traffic (e.g., file downloads).

### 3.1 QoS Mechanisms
| Mechanism | Purpose |
|---|---|
| **Classification & Marking** | Identify traffic type and tag it (DSCP in IP header, CoS in Ethernet 802.1p) |
| **Queuing** | Priority Queuing, Weighted Fair Queuing (WFQ), Class-Based WFQ (CBWFQ), Low Latency Queuing (LLQ) |
| **Congestion Avoidance** | RED / WRED (Random Early Detection) — drop packets proactively before queue overflow |
| **Traffic Shaping** | Smooth bursts to conform to a rate (see IPS file) |
| **Traffic Policing** | Enforce rate limits, drop/re-mark excess |
| **Admission Control** | Reject new flows if resources are insufficient (used in VoIP/RSVP) |

### 3.2 QoS Models
- **Best Effort** – no guarantee (default internet).
- **Integrated Services (IntServ)** – per-flow reservation via RSVP; doesn't scale well.
- **Differentiated Services (DiffServ)** – marks packets (DSCP) into classes (EF, AF, BE) handled per-hop; scalable, widely used.

> **Tricky Q:** Why is DiffServ preferred over IntServ in large ISP networks?
> **A:** IntServ requires **per-flow state** at every router (via RSVP signaling) — doesn't scale to millions of flows. DiffServ only requires routers to look at a **DSCP marking** in the header and apply a pre-configured per-hop behavior — far more scalable, no per-flow state needed.

> **Tricky Q:** LLQ (Low Latency Queuing) = Priority Queuing + CBWFQ. Why combine them?
> **A:** Pure Priority Queuing can **starve** lower-priority traffic if high-priority traffic is unbounded; LLQ gives voice/video a strict-priority queue but **polices/caps** its bandwidth, while CBWFQ fairly shares the rest among other classes — best of both worlds.

---

## Quick Revision – One-Liners
- IP spoofing prevented via ingress filtering/uRPF; ARP spoofing via Dynamic ARP Inspection.
- DoS = 1 attacker; DDoS = distributed botnet.
- 3 DDoS categories: Volumetric, Protocol, Application-layer.
- SYN cookies defend against SYN flood without storing state.
- DNS amplification = spoofed source + large response = amplification factor.
- QoS: DiffServ (scalable, DSCP marking) > IntServ (per-flow, doesn't scale).
- Shaping buffers, Policing drops/re-marks.
