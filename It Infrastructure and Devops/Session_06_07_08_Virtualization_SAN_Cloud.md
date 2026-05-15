# Sessions 6–8: Virtualization, SAN & Cloud Computing (DevOps)

---

# SESSION 6 – BASICS OF VIRTUALIZATION

---

## 1. What is Virtualization?

**Virtualization** = Creating a virtual (software-based) version of something — hardware, OS, storage, network.

**Benefits:**
- Better hardware utilization (run multiple VMs on one server)
- Isolation (VMs don't affect each other)
- Cost savings (fewer physical machines)
- Easy backup, migration, disaster recovery
- Faster provisioning

---

## 2. Type 1 vs Type 2 Hypervisor

| Feature | Type 1 (Bare-Metal) | Type 2 (Hosted) |
|---------|---------------------|-----------------|
| **Runs on** | Directly on hardware | On top of a host OS |
| **Performance** | High | Lower (extra OS layer) |
| **Use case** | Production/enterprise | Dev/testing |
| **Examples** | VMware ESXi, Microsoft Hyper-V, KVM | VirtualBox, VMware Workstation, Parallels |

> ⚠️ **Tricky Q:** KVM is technically Type 1 because it's built into the Linux kernel and runs directly on hardware — even though it looks like it runs within Linux.

---

## 3. Hardware Virtualization

**Full Virtualization:**
- Guest OS runs *unmodified*
- Hypervisor intercepts all hardware calls
- Requires hardware support: **Intel VT-x** or **AMD-V**
- Example: Standard VMware VMs

> ⚠️ **Tricky Q:** Full virtualization requires CPU support (Intel VT-x / AMD-V). If the CPU doesn't support it, you need para-virtualization.

---

## 4. Para-Virtualization

- Guest OS is **modified** to be aware it's running in a virtual environment
- Guest OS makes **hypercalls** directly to the hypervisor (faster than trapping/intercepting)
- Better performance than full virtualization in some workloads
- Example: **Xen** hypervisor with modified Linux guest

| | Full Virtualization | Para-Virtualization |
|--|---------------------|---------------------|
| Guest OS | Unmodified | Modified |
| Communication | Trapping (intercepted) | Hypercalls (direct) |
| Compatibility | Higher | Lower (needs modified OS) |
| Performance | Good | Better in some cases |

> ⚠️ **Tricky Q:** Windows cannot be para-virtualized easily (closed source, can't be modified). Para-virtualization is mainly used with Linux guests.

---

## 5. Cloning, Snapshot, Template

| Concept | Definition | Use Case |
|---------|-----------|---------|
| **Clone** | Full copy of a VM | Create identical VMs |
| **Snapshot** | Point-in-time state of a VM | Quick rollback before risky change |
| **Template** | Master image used to deploy new VMs | Consistent, repeatable VM deployment |

**Clone vs. Template:**
- Clone = exact copy of running/stopped VM
- Template = sealed, non-runnable master for deploying new VMs

> ⚠️ **Tricky Q:** Taking too many snapshots degrades VM performance — snapshots create delta disk files that grow over time and chain up. Snapshots are NOT backups.

---

## 6. Role of Virtualization in DevOps and Cloud

- **Infrastructure as Code (IaC):** Virtualization enables defining infra in code (Terraform, Ansible)
- **CI/CD pipelines:** VMs/containers spin up and tear down automatically
- **Isolation:** Dev, test, staging, and production environments are isolated VMs
- **Cloud foundation:** Cloud computing is built on virtualization (AWS EC2 = VMs)

---

# SESSION 7 – STORAGE AREA NETWORK (SAN)

---

## 1. What is a SAN?

**SAN (Storage Area Network)** = A dedicated high-speed network that provides block-level storage access to servers.

**SAN vs NAS vs DAS:**

| | SAN | NAS | DAS |
|--|-----|-----|-----|
| **Type** | Block-level | File-level | Block-level |
| **Connection** | Fibre Channel or iSCSI | Ethernet (TCP/IP) | Direct cable (SATA/SAS) |
| **Shared?** | Yes | Yes | No |
| **Protocol** | FC, iSCSI, FCoE | NFS, SMB/CIFS | N/A |
| **Performance** | Very high | Moderate | High |
| **Use case** | Databases, VMs | File sharing | Single server |

> ⚠️ **Tricky Q:** SAN provides **block storage** — the OS sees it as a raw disk. NAS provides **file storage** — you see folders and files. This is a very common exam question.

---

## 2. Configuring SAN (FreeNAS / TrueNAS)

**FreeNAS** (now called **TrueNAS CORE**) = Open-source NAS/SAN OS based on FreeBSD.

Key FreeNAS features:
- Uses **ZFS** file system
- Supports **iSCSI** for block-level SAN access
- Supports **NFS and SMB** for file-level NAS access
- Web-based administration interface

**Setting up iSCSI on FreeNAS:**
1. Create a ZFS pool
2. Create a ZFS Volume (zvol) for block storage
3. Enable iSCSI service
4. Create: **Target** (server name), **Initiator** (who can connect), **Extent** (the zvol)
5. Associate target with initiator and extent
6. Connect from client using iSCSI Initiator

---

## 3. High Availability Using SAN

**HA with SAN benefits:**
- Multiple servers share the same storage
- If one server fails, another takes over (using same data)
- No need to replicate data between servers

**MPIO (Multipath I/O):**
- Multiple physical paths between server and storage
- Provides redundancy AND load balancing
- If one path fails, traffic automatically routes through another

> ⚠️ **Tricky Q:** MPIO is **not the same as RAID**. MPIO handles the network *path* redundancy; RAID handles *disk* redundancy. Both can (and should) be used together.

---

## 4. ZFS Volume Configuration

**ZFS** = Zettabyte File System — advanced file system with built-in volume management.

**ZFS Key Features:**

| Feature | Description |
|---------|-------------|
| **Copy-on-Write (CoW)** | Data is never overwritten in place — safe writes |
| **Snapshots** | Instant, space-efficient point-in-time copies |
| **RAID-Z** | Software RAID with no write-hole issue |
| **Data Integrity** | Checksums detect and correct silent corruption |
| **Compression** | Inline transparent compression |
| **Deduplication** | Remove duplicate blocks to save space |

**ZFS RAID Levels:**

| Level | Min Disks | Disks Can Fail |
|-------|-----------|---------------|
| **RAID-Z1** | 3 | 1 |
| **RAID-Z2** | 4 | 2 |
| **RAID-Z3** | 5 | 3 |
| **Mirror** | 2 | N-1 (all but one) |

> ⚠️ **Tricky Q:** ZFS has **no write-hole problem** (unlike traditional RAID-5) because of Copy-on-Write. This is a key ZFS advantage over hardware RAID-5.

---

## 5. IP-Based Storage Communication (iSCSI)

**iSCSI** = Internet SCSI — transports SCSI commands over TCP/IP networks.

| Component | Role |
|-----------|------|
| **iSCSI Target** | Storage server (FreeNAS) |
| **iSCSI Initiator** | Client (connects to target) |
| **IQN** | iSCSI Qualified Name — unique identifier |
| **LUN** | Logical Unit Number — the virtual disk |

**iSCSI vs Fibre Channel:**

| | iSCSI | Fibre Channel |
|--|-------|---------------|
| **Transport** | TCP/IP (Ethernet) | FC protocol |
| **Cost** | Cheap (uses existing network) | Expensive (dedicated FC switches/HBAs) |
| **Performance** | Good | Excellent |
| **Complexity** | Low | High |

> ⚠️ **Tricky Q:** iSCSI runs over regular Ethernet — no special hardware needed beyond a NIC. This is why it's preferred for smaller organizations over Fibre Channel.

---

# SESSION 8 – CLOUD COMPUTING

---

## 1. Introduction to Cloud Computing

**NIST Definition:** Cloud computing is a model for enabling ubiquitous, convenient, on-demand network access to a shared pool of configurable computing resources.

**5 Essential Characteristics:**

| Characteristic | Description |
|---------------|-------------|
| **On-demand self-service** | Provision resources without human interaction |
| **Broad network access** | Available over the network (internet, intranet) |
| **Resource pooling** | Shared multi-tenant model |
| **Rapid elasticity** | Scale up/down quickly |
| **Measured service** | Pay for what you use |

> ⚠️ **Tricky Q:** All 5 characteristics must be present for something to be considered cloud computing. Missing one means it's just "hosting" or "managed services."

---

## 2. Cloud Service Models (SPI)

| Model | Full Name | Provider Manages | Customer Manages | Example |
|-------|-----------|-----------------|-----------------|---------|
| **IaaS** | Infrastructure as a Service | Physical hardware, network, virtualization | OS, middleware, apps, data | AWS EC2, Azure VM |
| **PaaS** | Platform as a Service | Everything up to runtime | Applications, data | Heroku, Google App Engine |
| **SaaS** | Software as a Service | Everything | Nothing (just use it) | Gmail, Salesforce, Office 365 |

**Shared Responsibility Model:**

```
SaaS: Customer manages → Data, Access
PaaS: Customer manages → Data, Access, Apps
IaaS: Customer manages → Data, Access, Apps, OS, Middleware
```

> ⚠️ **Tricky Q:** In IaaS, you are responsible for patching the OS. In PaaS/SaaS, the provider patches the OS. This affects your security responsibilities.

---

## 3. Deployment Models

| Model | Description | Who has access | Example |
|-------|-------------|---------------|---------|
| **Public** | Shared infrastructure, owned by provider | Anyone | AWS, Azure, GCP |
| **Private** | Dedicated infrastructure | One organization | On-premises VMware |
| **Hybrid** | Mix of public + private | Controlled sharing | AWS + on-prem data |
| **Community** | Shared by organizations with common interest | Specific community | Government cloud |

> ⚠️ **Tricky Q:** A **Hybrid Cloud** is NOT just "some stuff on-prem and some in public cloud." It must have **orchestration/integration** between the two environments to be called hybrid.

---

## 4. Cloud Security

### SLA in Cloud:
- Defines **availability guarantees** (e.g., 99.99% uptime)
- Specifies **penalties** for breaches
- Cloud providers rarely offer SLAs below 99.9% for core services

### IAM (Identity and Access Management):
- **Authentication** – Verify who you are (password, MFA)
- **Authorization** – What you're allowed to do (policies, roles)
- **Key concepts:** Least privilege, Role-Based Access Control (RBAC), Multi-Factor Authentication (MFA)

**AWS IAM Key Concepts:**

| Concept | Description |
|---------|-------------|
| **User** | Individual identity |
| **Group** | Collection of users |
| **Role** | Assigned to services/resources (not users directly) |
| **Policy** | JSON document defining permissions |

> ⚠️ **Tricky Q:** IAM Roles in AWS are NOT for human users — they're for services and applications. Humans use IAM Users. Roles are assumed temporarily.

---

## 5. Cloud Architecture Principles

- **High Availability (HA):** Deploy across multiple **Availability Zones (AZs)**
- **Fault Tolerance:** System continues even if components fail
- **Elasticity:** Scale automatically based on demand (Auto Scaling)
- **Loose Coupling:** Components are independent — failure of one doesn't cascade
- **Stateless design:** No state stored on individual servers → easy to scale

> ⚠️ **Tricky Q:** **Elasticity** = automatic scaling. **Scalability** = *ability* to scale (but not necessarily automatic). Elasticity is more powerful in cloud.

---

## 6. Cloud Services Overview

| Service Category | AWS Examples | Purpose |
|-----------------|-------------|---------|
| **Compute** | EC2, Lambda, ECS | Run applications |
| **Storage** | S3, EBS, Glacier | Store data |
| **Database** | RDS, DynamoDB, Aurora | Managed databases |
| **Networking** | VPC, CloudFront, Route 53 | Connect services |
| **Security** | IAM, Shield, WAF | Protect resources |
| **Monitoring** | CloudWatch, CloudTrail | Monitor & audit |

---

## 7. Cloud Development Best Practices

**The 12-Factor App (key cloud-native principles):**
1. Codebase tracked in version control
2. Dependencies explicitly declared
3. Config stored in environment variables
4. Backing services (DBs) treated as attached resources
5. Build/release/run stages separated
6. Stateless processes
7. Port binding for service exports
8. Concurrency via process model
9. Fast startup and graceful shutdown
10. Dev/staging/prod parity
11. Logs as event streams
12. Admin processes run as one-off tasks

> ⚠️ **Tricky Q:** Storing config (passwords, API keys) in environment variables (not in code) is a 12-Factor App principle. Hardcoding credentials is a critical security violation.

---

## 8. Cloud API Integration

- Cloud resources are managed via **REST APIs**
- **SDK** (Software Development Kit) = pre-built libraries (boto3 for Python/AWS)
- **CLI** (Command Line Interface) = terminal-based management (aws cli)
- **IaC tools** (Terraform, CloudFormation) use APIs under the hood

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Type 1 vs Type 2 hypervisor difference? | Type 1 = bare metal; Type 2 = runs on host OS |
| KVM is Type? | Type 1 (built into Linux kernel) |
| Para-virtualization requires? | Modified guest OS; makes hypercalls |
| Snapshot vs backup? | Snapshot is NOT a backup — it's a point-in-time delta |
| SAN vs NAS difference? | SAN = block-level; NAS = file-level |
| iSCSI runs over? | TCP/IP (Ethernet) |
| ZFS advantage over RAID-5? | No write-hole problem (Copy-on-Write) |
| MPIO purpose? | Multiple paths for redundancy + load balancing |
| IaaS vs PaaS vs SaaS — who patches the OS? | IaaS = customer; PaaS/SaaS = provider |
| 5 cloud characteristics? | On-demand, broad access, pooling, elasticity, measured |
| Hybrid cloud requirement? | Must have orchestration/integration between environments |
| IAM Role vs IAM User? | Role = for services; User = for humans |
| Elasticity vs Scalability? | Elasticity = auto-scales; Scalability = can scale |
| Deployment models? | Public, Private, Hybrid, Community |
