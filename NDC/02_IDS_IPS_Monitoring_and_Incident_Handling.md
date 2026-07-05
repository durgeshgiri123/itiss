# Intrusion Detection & Prevention, Monitoring, and Incident Handling

## 1. Intrusion Detection System (IDS) vs Intrusion Prevention System (IPS)

| Aspect | IDS | IPS |
|---|---|---|
| Placement | Out-of-band (monitors a copy of traffic via SPAN/TAP) | Inline (in the direct traffic path) |
| Action | **Detects & alerts only** | **Detects & blocks/drops** in real time |
| Impact on traffic | None (passive) | Can introduce latency; false positive = legit traffic blocked |
| Response time | Reactive (after packet passed) | Proactive (before packet reaches target) |

> **Tricky Q:** Why would an organization choose IDS over IPS despite IPS blocking attacks automatically?
> **A:** IPS sits inline — a **false positive can block legitimate business traffic** or even cause an outage; IDS carries zero traffic-disruption risk since it only alerts, letting a human/SOC verify before action.

## 2. Detection Methodologies
| Method | How it Works | Pros | Cons |
|---|---|---|---|
| **Signature-based** | Matches traffic against known attack patterns/signatures | High accuracy for known attacks, low false positives | Cannot detect zero-day/unknown attacks; needs constant updates |
| **Anomaly-based** | Builds a baseline of "normal" behavior, flags deviations | Can catch zero-day attacks | Higher false positive rate; needs training period |
| **Stateful Protocol Analysis** | Compares traffic against vendor-defined protocol standards | Detects protocol misuse | Resource-heavy, complex to maintain |
| **Hybrid** | Combines signature + anomaly | Balanced | Complex tuning |

> **Tricky Q:** Which detection method is best against a zero-day exploit?
> **A:** Anomaly-based — since no signature exists yet for an unknown attack, only deviation-from-baseline detection has a chance of flagging it.

## 3. IDS/IPS Placement Types
- **NIDS/NIPS** (Network-based) – monitors traffic on the network segment (e.g., Snort, Suricata).
- **HIDS/HIPS** (Host-based) – monitors a single host's logs, file integrity, system calls (e.g., OSSEC, Tripwire).
- **Wireless IPS (WIPS)** – detects rogue APs, evil twins.
- **Network Behavior Analysis (NBA)** – uses flow data (NetFlow) to detect anomalies like DDoS, worms.

> **Tricky Q:** Which type would catch an insider copying files locally without any network transmission?
> **A:** HIDS — since NIDS only sees network traffic; file/log-level anomalies are visible only at the host.

## 4. Types of Intrusion Prevention Systems (Detailed)
1. **Network-based IPS (NIPS)** – inline device inspecting all network segment traffic.
2. **Host-based IPS (HIPS)** – agent on endpoint, monitors system calls/registry/files.
3. **Wireless IPS (WIPS)** – monitors RF spectrum for rogue devices/attacks.
4. **Network Behavior Analysis (NBA)** – flow-based anomaly detection (DDoS, scanning, worm propagation).

## 5. IPS Basics — How It Works
1. **Traffic capture** inline.
2. **Preprocessing/normalization** (defragment packets, decode protocols).
3. **Detection engine** (signature/anomaly/stateful analysis).
4. **Decision** – pass, drop, reset connection (TCP RST), or quarantine.
5. **Logging & alerting** to SIEM/console.

## 6. Limitations of IPS
- **False positives** can block legitimate traffic (business impact).
- **False negatives** – evasion via fragmentation, encryption, encoding, timing attacks.
- **Single point of failure/bottleneck** since inline — needs fail-open/fail-closed design decision.
- **Cannot inspect encrypted traffic** without decryption capability.
- **Performance/latency overhead** at high throughput.
- **Signature lag** – zero-day attacks bypass until signature is released.
- Requires **continuous tuning** or alert fatigue occurs.

> **Tricky Q:** What is "fail-open" vs "fail-closed" in IPS design, and why does it matter?
> **A:** Fail-open = if IPS crashes, traffic still passes (favors availability, risk of no protection). Fail-closed = if IPS crashes, all traffic is blocked (favors security, risk of outage). Choice depends on whether business prioritizes uptime or security posture.

---

## 7. Intrusion Risks
- Data theft/exfiltration
- Privilege escalation → full compromise
- Lateral movement to other systems
- Ransomware deployment
- Reputation and regulatory/compliance damage (GDPR, HIPAA, PCI-DSS)
- Backdoor/persistence installation for future access
- Botnet recruitment (used in DDoS against others)

## 8. Security Policy (Governance Layer)
A security policy is the **documented set of rules** governing acceptable use, access control, and protection of assets. Types:
- **Organizational/Master Policy** – top-level intent from management
- **Issue-specific Policy** – e.g., Acceptable Use Policy, BYOD policy
- **System-specific Policy** – e.g., firewall rule policy, password policy

> **Tricky Q:** What's the difference between a security *policy*, a *standard*, and a *procedure*?
> **A:** Policy = high-level "what & why" (mandatory intent). Standard = specific mandatory requirement supporting the policy (e.g., "passwords must be 12+ chars"). Procedure = step-by-step "how to" instructions to implement the standard.

## 9. Monitoring and Reporting of Traffic
- **Tools:** SIEM (Splunk, QRadar, ELK), NetFlow/sFlow analyzers, packet capture (Wireshark/tcpdump)
- **Key metrics monitored:** bandwidth utilization, anomalous flows, top talkers, protocol distribution, failed logins, alert counts
- **Reporting cadence:** real-time dashboards, daily/weekly summary, compliance audit reports
- **Log correlation** – combining firewall, IDS/IPS, server, and auth logs to detect multi-stage attacks

## 10. Traffic Shaping
Traffic shaping (a.k.a. **packet shaping**) controls the volume and rate of traffic sent, by **buffering/delaying** packets that exceed a defined rate, to conform to a desired traffic profile.

- **Shaping vs Policing:**
  | | Shaping | Policing |
  |---|---|---|
  | Excess traffic | Buffered/delayed | Dropped or re-marked |
  | Effect | Smooths bursts | Hard enforcement, can cause retransmits |
  | Use case | Egress traffic control | Ingress rate limiting |

- Achieved via **token bucket** / **leaky bucket** algorithms.
- Used to prioritize latency-sensitive traffic (VoIP, video) over bulk (backups, downloads).

> **Tricky Q:** Why does policing sometimes hurt TCP performance more than shaping?
> **A:** Policing drops excess packets immediately, causing TCP retransmissions and triggering congestion control (slow-start) repeatedly, hurting throughput; shaping buffers/delays packets, smoothing bursts without abrupt drops.

---

## 11. Investigating and Verifying Detected Intrusions
Standard workflow (aligned with NIST SP 800-61 Incident Response Lifecycle):
1. **Preparation** – have tools, playbooks, contacts ready before an incident.
2. **Detection & Analysis** – validate the alert isn't a false positive; correlate logs; determine scope.
3. **Containment** – short-term (isolate host/segment) and long-term (patch, rebuild).
4. **Eradication** – remove malware/backdoors, close vulnerability.
5. **Recovery** – restore systems from clean backups, monitor closely for reinfection.
6. **Post-Incident Activity (Lessons Learned)** – reporting & documentation.

### Verification Techniques
- Correlate IDS alert with firewall/server logs (avoid acting on a single data point).
- Check file hashes against threat intel (VirusTotal).
- Review process trees, network connections (netstat, process explorer).
- Timeline reconstruction from logs.

## 12. Recovering From Intrusions
- Isolate affected systems (network segmentation/VLAN quarantine).
- Preserve evidence **before** remediation (forensic imaging) — chain of custody.
- Patch the exploited vulnerability.
- Restore from known-good backups.
- Reset all potentially compromised credentials.
- Monitor for re-compromise (IOC watch list).

## 13. Reporting and Documenting Intrusions
Should include:
- Timeline of events (detection → containment → recovery)
- Systems/data affected & business impact
- Root cause analysis
- Indicators of Compromise (IOCs)
- Actions taken & by whom
- Recommendations to prevent recurrence
- Regulatory notification requirements if applicable (e.g., breach disclosure laws)

> **Tricky Q:** Why must you image/preserve evidence BEFORE starting remediation?
> **A:** Remediation (e.g., rebooting, patching, deleting malware) can destroy volatile evidence (RAM, running processes, temp files) needed for forensic/legal investigation — always **contain and preserve**, then eradicate.

---

## Quick Revision – One-Liners
- IDS = detect & alert (passive); IPS = detect & block (inline, active).
- Signature-based = known attacks only; Anomaly-based = can catch zero-days but more false positives.
- NIDS/NIPS = network; HIDS/HIPS = host; WIPS = wireless; NBA = flow-based.
- IPS limitation: false positives can block legit traffic; performance bottleneck since inline.
- Traffic shaping buffers/delays; policing drops/re-marks.
- Incident response order: Preparation → Detection/Analysis → Containment → Eradication → Recovery → Lessons Learned.
- Always preserve evidence before remediation.
