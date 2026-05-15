# Session 2 – Service Design (ITIL)

---

## 1. Purpose of Service Design

Service Design translates the strategy into a detailed **blueprint** for delivering services.

**Goal:** Design IT services with required quality, reliability, and security — ready for transition and operation.

**5 Major Aspects of Service Design (5 Ds):**

| Aspect | What it covers |
|--------|---------------|
| **Design of Services** | New or changed service solutions |
| **Design of Service Management Systems** | Processes and procedures |
| **Design of Technology Architectures** | Infrastructure, apps, data |
| **Design of Processes** | How work gets done |
| **Design of Measurement Systems** | Metrics and reporting |

> ⚠️ **Tricky Q:** Service Design is not just about technology — it includes *processes, people, and measurement* too (the 4 Ps: People, Processes, Products/Technology, Partners).

---

## 2. Architecture, Processes, Policies, and Documentation

A good service design must produce:

- **Architecture** – Technical infrastructure blueprint
- **Processes** – Step-by-step workflows
- **Policies** – Rules and guidelines that govern behavior
- **Documentation** – Written specifications and standards

**Design Coordinates:**
All must balance:
- **Functionality** (what it does)
- **Quality** (how well it does it)
- **Cost** (within budget)
- **Time** (delivered when needed)

> ⚠️ **Tricky Q:** There's always a trade-off triangle between Quality, Cost, and Time — you can usually only optimize two at once.

---

## 3. Service Design Package (SDP)

The **SDP** is the key deliverable of Service Design — a document (or set of documents) that defines ALL aspects of a service throughout its lifecycle.

### What SDP Contains:

| Section | Content |
|---------|---------|
| **Business Requirements** | Why the service is needed |
| **Service Applicability** | Who and what it applies to |
| **Service Contacts** | Ownership and stakeholders |
| **Service Functional Requirements** | What the service must do |
| **Service Level Requirements** | SLA targets, availability, performance |
| **Service and Operational Management** | How it will be run |
| **Service Acceptance Criteria** | How we know it's done correctly |
| **Transition Plan** | How we move from design to live |

> ⚠️ **Tricky Q:** SDP is created *before* transition — it's a design artifact, not an operational one. It's handed over to Service Transition.

---

## 4. Service Catalog Management

**Service Catalog** = Published list of live IT services available to customers.

### Two Views of the Service Catalog:

| View | Audience | Content |
|------|---------|---------|
| **Business / Customer-Facing** | Business users | Services in business language, no tech jargon |
| **Technical / Supporting** | IT teams | Underlying components, dependencies |

**Difference: Service Catalog vs. Service Portfolio:**

| | Service Portfolio | Service Catalog |
|--|-----------------|-----------------|
| **Scope** | All services (pipeline + live + retired) | Only live/available services |
| **Audience** | IT management | Business + IT users |
| **Purpose** | Investment decisions | Requesting services |

> ⚠️ **Tricky Q:** The Service Catalog is a *subset* of the Service Portfolio. Not all portfolio items are in the catalog!

---

## 5. Service Level Management (SLM)

**Goal:** Negotiate, document, monitor, and improve service quality agreements.

### Key Documents:

| Document | Full Form | Between Whom | Purpose |
|----------|-----------|-------------|---------|
| **SLA** | Service Level Agreement | IT Provider ↔ Customer | Defines agreed service targets |
| **OLA** | Operational Level Agreement | Internal IT teams | Supports SLA delivery |
| **UC** | Underpinning Contract | IT Provider ↔ External supplier | External vendor commitments |

**SLA Types:**

| Type | Description | Example |
|------|-------------|---------|
| **Service-based SLA** | One SLA for one service, all customers | All users get 99.9% uptime for Email |
| **Customer-based SLA** | One SLA per customer group | VIP dept gets higher targets |
| **Multi-level SLA** | Layered (corporate + customer + service) | Most flexible, most complex |

> ⚠️ **Tricky Q:** OLAs and UCs *support* SLAs — they are not alternatives. If OLA or UC fails, SLA will likely fail too.

> ⚠️ **Tricky Q:** SLA is NOT a legal contract by default — it's a service agreement. A UC (Underpinning Contract) with an external vendor *is* a legal contract.

---

## 6. Capacity Management

**Goal:** Ensure IT capacity always matches current and future business demand — cost-effectively.

### Three Sub-Processes:

| Sub-Process | Focus |
|-------------|-------|
| **Business Capacity Management** | Future business requirements → plan ahead |
| **Service Capacity Management** | Current service performance vs. targets |
| **Component Capacity Management** | Individual components (CPU, RAM, disk) |

**Key Concepts:**

- **Capacity Plan** – Document showing current/future capacity requirements
- **CMIS** (Capacity Management Information System) – Database of capacity data
- **Tuning** – Adjusting resources to optimize performance
- **Demand Management** – Influencing demand to balance capacity

> ⚠️ **Tricky Q:** Capacity management deals with *both* over-capacity (wasted money) *and* under-capacity (poor performance). Both are problems.

---

## 7. IT Service Continuity Management (ITSCM)

**Goal:** Ensure IT services can continue during and after a disaster.

**Key Terms:**

| Term | Definition |
|------|-----------|
| **BCP** | Business Continuity Plan – overall business |
| **DRP** | Disaster Recovery Plan – IT-specific recovery |
| **RTO** | Recovery Time Objective – max time to restore service |
| **RPO** | Recovery Point Objective – max data loss acceptable |

> ⚠️ **Tricky Q:** ITSCM supports BCP — it does *not* replace it. Business drives continuity requirements; IT supports them.

> ⚠️ **Tricky Q:** Lower RTO = more expensive (faster recovery). Lower RPO = more backups/replication needed.

### Recovery Options (Escalating Cost):

| Option | Description | Speed |
|--------|-------------|-------|
| **Manual Workarounds** | Paper-based processes | Slowest |
| **Reciprocal Arrangements** | Agreement with another org | Moderate |
| **Gradual Recovery (Cold Standby)** | Empty site, install everything | Days |
| **Intermediate Recovery (Warm Standby)** | Partially configured site | Hours |
| **Fast Recovery (Hot Standby)** | Fully configured, near-live | Minutes |
| **Immediate Recovery (Mirror Site)** | Identical live copy | Seconds |

---

## 8. Information Security Management (ISM)

**Goal:** Protect all information assets — confidentiality, integrity, and availability.

### CIA Triad:

| Principle | Meaning | Threat example |
|-----------|---------|---------------|
| **Confidentiality** | Only authorized access | Data breach |
| **Integrity** | Data is accurate and unaltered | Unauthorized modification |
| **Availability** | Accessible when needed | DDoS attack |

**Two additional principles (extended):**
- **Authenticity** – Confirming identity
- **Non-repudiation** – Can't deny performing an action

**Security Controls:**

| Type | Example |
|------|---------|
| **Preventive** | Firewalls, encryption |
| **Detective** | IDS, audit logs |
| **Corrective** | Patching, incident response |
| **Deterrent** | CCTV, security warnings |

> ⚠️ **Tricky Q:** Availability is part of the CIA triad — so ISM overlaps with both Capacity Management and ITSCM!

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| What is an SDP? | Service Design Package – blueprint for a service |
| SLA vs OLA vs UC? | SLA = customer agreement; OLA = internal; UC = external vendor |
| Service Catalog vs Portfolio? | Catalog = live only; Portfolio = all (pipeline + live + retired) |
| CIA triad? | Confidentiality, Integrity, Availability |
| RTO vs RPO? | RTO = recovery time; RPO = data loss tolerance |
| Hot standby vs Cold standby? | Hot = minutes; Cold = days |
| Is SLA a legal contract? | No – UC with external vendor is |
| 3 types of SLA? | Service-based, Customer-based, Multi-level |
| What does SLM stand for? | Service Level Management |
| Business Capacity Management focus? | Future business requirements |
