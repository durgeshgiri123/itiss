# Virtual Private Networks (VPN) — Deployment, Management & Performance Tuning

## 1. What is a VPN?
A VPN creates an **encrypted tunnel** over a public/untrusted network (like the internet), allowing secure, private communication as if devices were on the same private network.

## 2. Types of VPN
| Type | Use Case |
|---|---|
| **Site-to-Site VPN** | Connects two networks/offices permanently (gateway-to-gateway) |
| **Remote Access VPN** | Connects individual users to a corporate network (client-to-gateway) |
| **Client-to-Client VPN** | Rare, direct peer connections |
| **Extranet VPN** | Connects an organization to external partners/vendors with restricted access |

## 3. VPN Protocols

| Protocol | Layer | Encryption | Notes |
|---|---|---|---|
| **IPSec** | Network (L3) | AES, 3DES via ESP/AH | Industry standard for site-to-site; complex configuration |
| **SSL/TLS VPN** | Transport/Application | TLS | Easier, browser/clientless access possible, per-app tunnels |
| **PPTP** | Data Link (L2) | MPPE (weak) | Legacy, **insecure** — avoid in modern deployments |
| **L2TP/IPSec** | Data Link + Network | Uses IPSec for encryption (L2TP has none itself) | L2TP provides tunneling only, no encryption alone |
| **WireGuard** | Network | ChaCha20 | Modern, lightweight, fast, simple codebase (~4000 lines vs IPSec's much larger stack) |
| **OpenVPN** | Application (uses TLS) | AES | Open-source, flexible, works over UDP/TCP |

> **Tricky Q:** Why is L2TP "always" paired with IPSec?
> **A:** L2TP provides **tunneling only** (encapsulation) — it has **no built-in encryption or confidentiality**. IPSec supplies the encryption/authentication; together (L2TP/IPSec) you get both tunneling and security.

> **Tricky Q:** Why is PPTP considered insecure today?
> **A:** PPTP uses **MS-CHAPv2** authentication and **MPPE** encryption, both of which have known cryptographic weaknesses (MS-CHAPv2 crackable, effectively reducing security to DES-level); it's deprecated in favor of IPSec/SSL VPNs or WireGuard.

## 4. IPSec Deep Dive (Frequently Tested)

### 4.1 IPSec Modes
| Mode | What's Encrypted | Use Case |
|---|---|---|
| **Transport Mode** | Only the payload (data), original IP header stays intact | Host-to-host communication |
| **Tunnel Mode** | Entire original packet (header + payload) encapsulated in a new IP packet | Site-to-site (gateway-to-gateway) VPNs |

### 4.2 IPSec Protocols
- **AH (Authentication Header)** – provides authentication & integrity, **NO encryption/confidentiality**.
- **ESP (Encapsulating Security Payload)** – provides encryption **+** optional authentication/integrity.

> **Tricky Q:** If you need confidentiality, should you use AH or ESP?
> **A:** ESP — AH provides only integrity/authentication (no encryption), so data would travel in plaintext; ESP encrypts the payload.

### 4.3 IKE (Internet Key Exchange) — Two Phases
- **Phase 1** – establishes a secure, authenticated channel (ISAKMP SA) between peers (Main Mode = 6 messages/more secure, or Aggressive Mode = 3 messages/faster but exposes identity).
- **Phase 2** – negotiates the actual IPSec SAs (Quick Mode) used to encrypt the real data traffic.

> **Tricky Q:** Why does IKE need two phases instead of negotiating everything at once?
> **A:** Phase 1 sets up a secure, authenticated channel **once** (relatively expensive computation, e.g., Diffie-Hellman); Phase 2 can then **rapidly negotiate/rekey** multiple IPSec SAs using that already-secured channel — separating expensive one-time setup from cheaper repeated data-SA negotiation improves efficiency and allows periodic rekeying without redoing full authentication.

---

## 5. Deploying and Managing VPN

### 5.1 Deployment Steps
1. Define requirements (site-to-site vs remote access, number of users).
2. Choose protocol (IPSec for site-to-site, SSL/WireGuard for remote users).
3. Configure authentication (Pre-Shared Key, certificates, or AAA/RADIUS with MFA).
4. Define encryption/hashing algorithms (AES-256, SHA-256 preferred over legacy DES/MD5).
5. Configure split-tunneling vs full-tunneling policy.
6. Set up routing (static routes or dynamic routing over the tunnel).
7. Test failover/redundancy (dual ISP links, VPN concentrator HA pair).
8. Monitor tunnel status, logging, and periodic key rotation.

### 5.2 Split Tunneling vs Full Tunneling
| | Split Tunneling | Full Tunneling |
|---|---|---|
| Traffic routed | Only corporate-destined traffic goes through VPN; internet traffic goes direct | ALL traffic (including internet) goes through VPN |
| Pros | Saves bandwidth, faster general browsing | Centralized security monitoring/filtering |
| Cons | Security risk — local network traffic bypasses corporate inspection | Higher bandwidth/latency load on VPN concentrator |

> **Tricky Q:** Why might a security-conscious organization disable split tunneling despite the bandwidth savings?
> **A:** With split tunneling, a compromised user's **local/internet traffic bypasses corporate security controls** (firewall, DLP, content filtering) entirely — an attacker could pivot through the user's unprotected local connection. Full tunneling forces all traffic through inspection at the cost of bandwidth/latency.

---

## 6. VPN Performance Tuning and Error Handling

### 6.1 Performance Tuning Techniques
- **MTU/MSS adjustment** – VPN overhead (encapsulation headers) reduces effective MTU; misconfigured MTU causes fragmentation and **black-hole** issues (silent packet drops). Tune MSS clamping on the tunnel interface.
- **Choose efficient ciphers** – AES-GCM (with AES-NI hardware acceleration) over older CBC-mode ciphers for throughput.
- **Enable compression carefully** – can help low-bandwidth links but adds CPU overhead and is vulnerable to CRIME-style attacks on some protocols.
- **UDP over TCP** – prefer UDP-based VPN transport (WireGuard, OpenVPN over UDP) — avoids "TCP-over-TCP meltdown" problem.
- **Hardware offloading** – use crypto accelerator cards/AES-NI CPU instructions.
- **Load balancing across multiple VPN gateways/concentrators.**
- **Keep-alives / DPD (Dead Peer Detection)** – detect and recover from silently dropped tunnels.

> **Tricky Q:** What is "TCP-over-TCP meltdown" and why does it matter for VPN protocol choice?
> **A:** If a VPN tunnel itself runs over TCP (reliable, retransmitting) while carrying inner TCP traffic (also reliable, retransmitting), a single packet loss triggers **retransmission at both layers simultaneously**, causing exponential backoff conflicts and severe throughput collapse under any packet loss/congestion. This is why UDP-based VPN transport (OpenVPN over UDP, WireGuard, IPSec/ESP) is preferred — inner TCP handles its own reliability without a redundant/conflicting outer TCP layer.

### 6.2 Common VPN Errors & Troubleshooting
| Issue | Likely Cause | Fix |
|---|---|---|
| Tunnel won't establish (Phase 1 fails) | Mismatched PSK, encryption/hash algorithm, or IKE version | Verify matching IKE proposals on both peers |
| Tunnel up but no traffic passes (Phase 2 fails) | Mismatched proxy IDs / traffic selectors, ACL blocking | Verify matching subnets/traffic selectors |
| Intermittent drops | NAT-Traversal (NAT-T) not enabled behind NAT devices | Enable NAT-T (UDP 4500) |
| Slow throughput | MTU/fragmentation issues, weak cipher, no hardware acceleration | Adjust MSS/MTU, use AES-GCM, enable AES-NI |
| Users disconnect randomly | Idle timeout, DPD misconfigured, ISP interference | Tune keepalive/DPD intervals |

> **Tricky Q:** Why does NAT cause problems for IPSec specifically, and how is it solved?
> **A:** Standard IPSec (AH particularly) authenticates the **entire packet including IP headers** — NAT devices **rewrite the IP/port**, breaking that authentication (AH is essentially incompatible with NAT). The fix is **NAT-Traversal (NAT-T)**, which encapsulates ESP packets inside UDP (port 4500), allowing them to pass through NAT devices without breaking integrity checks.

---

## Quick Revision – One-Liners
- Site-to-site = gateway-to-gateway; Remote access = client-to-gateway.
- AH = integrity only (no encryption); ESP = encryption (+ optional integrity).
- Transport mode = payload only; Tunnel mode = entire packet encapsulated.
- IKE Phase 1 = secure channel setup; Phase 2 = negotiates actual IPSec SAs.
- Split tunneling = bandwidth-efficient but less secure; Full tunneling = secure but heavier load.
- Prefer UDP-based VPN transport to avoid TCP-over-TCP meltdown.
- NAT-T (UDP 4500) fixes IPSec + NAT incompatibility.
