# Session 3 – Service Transition & Service Operation (ITIL)

---

## PART A: SERVICE TRANSITION

**Purpose:** Move new or changed services from design into live production — reliably, with minimal risk.

---

## 1. Change Management

**Goal:** Control ALL changes to production — minimize disruption while allowing beneficial change.

### Change Types:

| Type | Description | Approval needed? |
|------|-------------|-----------------|
| **Standard Change** | Pre-approved, low risk, routine | No — pre-authorized |
| **Normal Change** | Must go through full CAB review | Yes — CAB |
| **Emergency Change** | Urgent fix (e.g., security patch) | Yes — ECAB |

**Change Advisory Board (CAB):**
- Reviews and authorizes normal changes
- Made up of stakeholders from IT and business

**Emergency CAB (ECAB):**
- Smaller group for emergency changes
- Meets quickly when time is critical

### Change Lifecycle:
```
Request for Change (RFC) → Review → Assessment → Approval → Implementation → Review (PIR)
```

**PIR = Post Implementation Review** — evaluates if the change succeeded.

> ⚠️ **Tricky Q:** A standard change has been pre-approved and does NOT need to go to the CAB each time. Emergency changes still need ECAB approval — they're not "skip all process."

> ⚠️ **Tricky Q:** The 7 Rs of Change Management:
- **Raised** – who raised it?
- **Reason** – why?
- **Return** – what's the expected return?
- **Risks** – what are the risks?
- **Resources** – what resources are needed?
- **Responsible** – who will implement?
- **Relationship** – what other changes/services are related?

---

## 2. Service Asset and Configuration Management (SACM)

**Goal:** Track and control all IT assets (Configuration Items) and their relationships.

**Configuration Item (CI)** = Any component that needs to be managed to deliver a service.
Examples: servers, applications, documentation, SLAs, people.

**CMDB (Configuration Management Database):**
- Central store for all CI information
- Tracks attributes and relationships between CIs

**CMS (Configuration Management System):**
- Broader system that includes CMDB(s) + tools + processes
- CMS is the system; CMDB is the database within it

> ⚠️ **Tricky Q:** CMS ⊃ CMDB (CMS contains CMDB). They are not the same thing.

**SKMS (Service Knowledge Management System):**
- Even broader — includes CMS, CMDB, and all other knowledge
- Top of the knowledge hierarchy

```
SKMS > CMS > CMDB
```

**Types of CIs:**
- **Service CIs** – service packages, contracts
- **Organization CIs** – business units, people
- **Internal CIs** – components within IT
- **External CIs** – external services, vendors

---

## 3. Transition Planning and Support

**Goal:** Plan and coordinate resources to deploy major releases.

Key activities:
- Define transition strategy
- Maintain release schedule
- Manage risks and issues during transition
- Provide a clear transition plan from design → live

> ⚠️ **Tricky Q:** Transition Planning is separate from Project Management — it focuses specifically on the IT service lifecycle transition, not project delivery.

---

## 4. Release and Deployment Management

**Goal:** Plan, schedule, and control builds, test, and deployment of releases.

### Release Types:

| Type | Description | Example |
|------|-------------|---------|
| **Major Release** | Significant new functionality | New version of ERP |
| **Minor Release** | Small improvements/bug fixes | Software patch |
| **Emergency Release** | Urgent fix for critical issue | Security vulnerability fix |

### Deployment Approaches:

| Approach | Description | Risk |
|----------|-------------|------|
| **Big Bang** | All users at once | High risk, fast |
| **Phased** | Roll out in stages | Lower risk, slower |
| **Push** | System pushes update automatically | Centralized control |
| **Pull** | Users choose when to update | User-controlled |
| **Pilot** | Test with a small group first | Lowest risk |

> ⚠️ **Tricky Q:** "Big Bang" sounds bad but can be needed when consistency across the entire org is required (e.g., all users must be on the same version at the same time).

---

## 5. Knowledge Management

**Goal:** Ensure the right information is available to the right people at the right time.

**DIKW Pyramid:**

```
       Wisdom (Why?)
      Knowledge (How?)
     Information (Who/What/When/Where?)
    Data (Raw facts)
```

- **Data** – Raw numbers and facts
- **Information** – Data with context
- **Knowledge** – Applying information to decisions
- **Wisdom** – Experience-based good judgment

**Known Error Database (KEDB):**
- Stores documented problems with known root cause and workarounds
- Used by Service Desk and Problem Management

> ⚠️ **Tricky Q:** A Known Error is a problem that has been *diagnosed* (root cause identified) but not yet *resolved*. It may have a workaround.

---

## PART B: SERVICE OPERATION

**Purpose:** Deliver and manage services at agreed levels every day.

---

## 6. Balancing Conflicting Goals

Service Operation must balance competing priorities:

| Conflict | Explanation |
|----------|------------|
| **Stability vs. Responsiveness** | Too stable = slow to change; Too responsive = unstable |
| **Quality vs. Cost** | Higher quality costs more |
| **Reactive vs. Proactive** | Reactive = fix problems; Proactive = prevent them |
| **IT Focus vs. Business Focus** | IT may focus on technology; Business focuses on outcomes |

> ⚠️ **Tricky Q:** Neither extreme is correct — Service Operation must find the *right balance*, not choose one over the other.

---

## 7. Event Management

**Event** = Any change of state significant enough to require IT attention.

### Event Types:

| Type | Description | Action |
|------|-------------|--------|
| **Informational** | Normal operation, no action needed | Log it |
| **Warning** | Threshold approaching, watch carefully | Investigate |
| **Exception** | Something has failed or violated a rule | Act immediately |

> ⚠️ **Tricky Q:** Not all events are incidents. An informational event is normal. Only exceptions typically become incidents.

---

## 8. Incident Management

**Incident** = Unplanned interruption or reduction in quality of an IT service.

**Goal:** Restore normal service ASAP — minimize business impact.

### Incident Lifecycle:
```
Detection → Logging → Categorization → Prioritization → Diagnosis → Escalation → Resolution → Closure
```

**Priority = Impact × Urgency**

| Priority | Response Time | Example |
|----------|--------------|---------|
| **P1 – Critical** | 15 mins | Entire organization down |
| **P2 – High** | 1 hour | Department affected |
| **P3 – Medium** | 4 hours | Single user, workaround available |
| **P4 – Low** | 8+ hours | Minor inconvenience |

**Escalation Types:**

| Type | Description |
|------|-------------|
| **Functional (Horizontal)** | Transfer to another team with the right skills |
| **Hierarchical (Vertical)** | Escalate to management for decision/resources |

> ⚠️ **Tricky Q:** Incident Management focuses on *speed of restoration*, not finding the root cause. Root cause analysis is Problem Management's job.

---

## 9. Problem Management

**Problem** = Unknown underlying cause of one or more incidents.

| Term | Definition |
|------|-----------|
| **Problem** | Unknown cause of incident(s) |
| **Known Error** | Problem with identified root cause |
| **Workaround** | Temporary fix reducing impact |
| **Permanent Fix** | Actual resolution — raised as Change |

### Two Modes:

| Mode | Trigger | Focus |
|------|---------|-------|
| **Reactive** | After incidents occur | Find root cause, prevent recurrence |
| **Proactive** | Before incidents occur | Trend analysis, identify risks |

> ⚠️ **Tricky Q:** A Known Error is stored in the **KEDB** (Known Error Database), NOT the CMDB.

> ⚠️ **Tricky Q:** Problem Management does NOT resolve incidents directly — it works to prevent *future* incidents.

---

## 10. Request Fulfillment

**Service Request** = Standard user request (NOT an incident).
Examples: password reset, new software installation, information request.

**Purpose:** Handle routine requests quickly without going through incident or change process.

Uses a **Request Model** — predefined workflow for common requests.

> ⚠️ **Tricky Q:** Password reset is a *service request*, not an incident. Don't confuse them!

---

## 11. Service Desk

**Single Point of Contact (SPOC)** between users and IT.

### Service Desk Types:

| Type | Description |
|------|-------------|
| **Local** | On-site at each location |
| **Centralized** | One location serves all |
| **Virtual** | Distributed, appears as one via technology |
| **Follow the Sun** | 24/7 coverage across global time zones |

**Service Desk responsibilities:**
- Log all contacts
- First-line resolution
- Escalation
- Communication to users
- Customer satisfaction

> ⚠️ **Tricky Q:** A Virtual Service Desk *appears* as one unified desk to the user but is physically distributed. It uses technology (VoIP, cloud tools) to seem centralized.

---

## 12. Technical and Application Management

| Function | Responsibility |
|----------|---------------|
| **Technical Management** | IT infrastructure (networks, servers, storage) |
| **Application Management** | Business applications lifecycle |
| **IT Operations Management** | Day-to-day operations (includes IT Operations Control + Facilities Management) |

> ⚠️ **Tricky Q:** IT Operations Management has two sub-functions: **IT Operations Control** (console management, job scheduling, backup, print) and **Facilities Management** (data centers, UPS, cooling).

---

## Key Roles in Service Transition & Operation

| Role | Area | Responsibility |
|------|------|---------------|
| **Change Manager** | Transition | Manages Change Management process |
| **CAB Member** | Transition | Reviews/authorizes normal changes |
| **Release Manager** | Transition | Plans and controls releases |
| **Configuration Manager** | Transition | Maintains CMDB |
| **Incident Manager** | Operation | Manages incident process |
| **Problem Manager** | Operation | Manages problem process |
| **Service Desk Manager** | Operation | Runs Service Desk function |

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Standard vs Normal vs Emergency change? | Standard = pre-approved; Normal = CAB review; Emergency = ECAB |
| What is PIR? | Post Implementation Review |
| CMDB vs CMS? | CMDB = database of CIs; CMS = system including CMDB + tools |
| DIKW pyramid bottom? | Data |
| What is a Known Error? | Problem with identified root cause, not yet resolved |
| Incident vs Problem? | Incident = restore service fast; Problem = find root cause |
| Priority formula? | Impact × Urgency |
| KEDB stores? | Known Errors |
| Service Request example? | Password reset, new software |
| Service Desk types? | Local, Centralized, Virtual, Follow the Sun |
| Functional vs Hierarchical escalation? | Functional = skills-based; Hierarchical = management escalation |
| What is a CI? | Configuration Item — any managed component |
