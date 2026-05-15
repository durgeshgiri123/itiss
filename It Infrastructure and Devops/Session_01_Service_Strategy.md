# Session 1 – Service Strategy (ITIL)

---

## 1. Introduction to ITIL

**ITIL** = Information Technology Infrastructure Library
A framework of best practices for delivering IT services aligned with business needs.

- Developed by **AXELOS** (originally by UK government's OGC)
- Current version: **ITIL 4** (focuses on value co-creation)
- Core concept: **Service Lifecycle** with 5 stages:
  1. Service Strategy
  2. Service Design
  3. Service Transition
  4. Service Operation
  5. Continual Service Improvement (CSI)

> ⚠️ **Tricky Q:** ITIL is a *framework*, not a standard. You *adopt and adapt* it. ISO/IEC 20000 is the actual certifiable standard.

---

## 2. General Strategy

Strategy defines **why** services exist and **how** they create value.

Key questions a strategy must answer:
- What services do we provide?
- Who do we provide them to?
- How do we differentiate ourselves from competitors?
- How do we create value?

### 4 Ps of Service Strategy

| P | Meaning |
|---|---------|
| **Perspective** | Vision and direction |
| **Position** | How you differentiate |
| **Plan** | How you will achieve goals |
| **Pattern** | Repeatable actions / behaviors |

> ⚠️ **Tricky Q:** All 4 Ps must be consistent with each other. A conflict between Position and Plan is a common exam trap.

---

## 3. Competition and Market Space

- **Market Space** = All possible services an IT provider *could* offer based on customer needs
- **Market Space ≠ Current portfolio** — it's the *opportunity*, not what you currently do

### Types of Value

| Value Type | Definition | Memory Hook |
|-----------|-----------|-------------|
| **Utility** | Fitness for *purpose* (what it does) | "Does it do the job?" |
| **Warranty** | Fitness for *use* (reliability, availability, security) | "Can I rely on it?" |

> ⚠️ **Tricky Q:** A service must deliver BOTH utility AND warranty to create value. Utility without warranty = unreliable. Warranty without utility = useless.

---

## 4. Service Provider Types

| Type | Description | Example |
|------|-------------|---------|
| **Type I – Internal** | Serves a single business unit | IT dept serving only Finance |
| **Type II – Shared Services** | Serves multiple units in same org | Corporate IT serving all depts |
| **Type III – External** | Serves external customers | Cloud providers, MSPs |

> ⚠️ **Tricky Q:** Type I = lowest overhead, least economies of scale. Type III = highest economies of scale, lowest control. Type II = middle ground.

---

## 5. Service Management as a Strategic Asset

Service management must be treated as a **strategic asset**, not just a cost center.

### Capabilities vs. Resources

| | Capabilities | Resources |
|--|-------------|-----------|
| **Definition** | Skills, knowledge, processes | Infrastructure, people, money, apps |
| **Example** | Change management process | Servers, storage |
| **Nature** | Intangible, hard to copy | Tangible, easier to acquire |

> ⚠️ **Tricky Q:** Capabilities are more strategically valuable because they're harder to replicate. Resources alone don't create competitive advantage.

---

## 6. Key Process Activities

### A. Financial Management

**Three mandatory activities:**

| Activity | What it does |
|----------|-------------|
| **Budgeting** | Planning future spend |
| **Accounting** | Tracking where money was spent |
| **Charging** | Billing customers (internal or external) |

**Cost Types:**

| Type | Description | Example |
|------|-------------|---------|
| **Direct** | Attributable to one service | Dedicated server |
| **Indirect** | Shared across multiple services | Data center electricity |
| **Fixed** | Doesn't change with usage | Annual software license |
| **Variable** | Changes with usage | Cloud compute per hour |

**ROI Formula:**
```
ROI = (Net Benefit / Cost of Investment) × 100
```

> ⚠️ **Tricky Q:** Charging is **optional** in ITIL. Budgeting and accounting are always required.

---

### B. Service Portfolio Management

**Service Portfolio** = Complete set of services managed by provider.

| Section | What it Contains | Status |
|---------|-----------------|--------|
| **Service Pipeline** | Services under development | Not yet live |
| **Service Catalog** | Active, available services | Live |
| **Retired Services** | Decommissioned services | No longer offered |

> ⚠️ **Tricky Q:** Service Catalog is a *subset* of the Service Portfolio — not the same thing!

**Portfolio Management decisions:**
- **Invest** – Grow the service
- **Replace** – Modernize / upgrade
- **Retire** – Remove if no longer needed
- **Retain** – Keep as-is

---

### C. Demand Management

**Goal:** Understand and influence customer demand so supply meets it without waste.

**Key Concepts:**

| Concept | Definition |
|---------|-----------|
| **Pattern of Business Activity (PBA)** | How business activity fluctuates over time |
| **User Profiles** | Different users with different demand patterns |

**Techniques to manage demand:**
- **Incentives** – Pricing discounts for off-peak usage
- **Reservations** – Schedule usage in advance
- **Priority access** – VIP user tiers

> ⚠️ **Tricky Q:** Demand management ≠ Capacity management. Demand management *influences behavior*; capacity management *ensures infrastructure keeps up*.

---

## 7. Key Roles and Responsibilities

| Role | Responsibility |
|------|---------------|
| **IT Steering Group (ISG)** | Aligns IT strategy with business strategy |
| **Service Owner** | Accountable for a specific service end-to-end |
| **Process Owner** | Sets policy and standards for a process |
| **Process Manager** | Runs the process day-to-day |
| **Service Strategy Manager** | Develops and maintains IT strategy |

> ⚠️ **Tricky Q:** Process Owner = *what* the process should achieve. Process Manager = *how* it runs. One person can hold both roles.

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Is ITIL a standard? | No – it's a framework. ISO/IEC 20000 is the standard. |
| What creates service value? | Utility + Warranty (both required) |
| Which provider type serves external clients? | Type III |
| 3 sections of the Service Portfolio? | Pipeline, Catalog, Retired |
| Is charging mandatory in ITIL? | No – optional |
| PBA stands for? | Pattern of Business Activity |
| Demand vs Capacity management difference? | Demand = influence behavior; Capacity = ensure infra |
| Which is more strategic — capabilities or resources? | Capabilities (harder to replicate) |
| ITIL 4 key concept? | Value co-creation |
