# MASTER QUICK REFERENCE – ITIM & DevOps Competition Exam

> One-stop cheat sheet covering all sessions. Review before exams.

---

## SECTION 1: ITIL (Sessions 1–5)

### Service Lifecycle (in order)
```
Strategy → Design → Transition → Operation → CSI
```

### Critical Distinctions Table

| Pair Often Confused | Distinction |
|---------------------|-------------|
| ITIL vs ISO/IEC 20000 | ITIL = framework; ISO = certifiable standard |
| Utility vs Warranty | Utility = fitness for purpose; Warranty = fitness for use |
| Service Portfolio vs Catalog | Portfolio = all services (pipeline + live + retired); Catalog = live only |
| SLA vs OLA vs UC | SLA = customer; OLA = internal team; UC = external vendor |
| Incident vs Problem | Incident = restore fast; Problem = find root cause |
| Known Error vs Problem | Known Error = problem with identified root cause |
| CMDB vs CMS | CMDB = database of CIs; CMS = system containing CMDB |
| Demand vs Capacity Mgmt | Demand = influence behavior; Capacity = ensure infra |
| CI (Continuous Improvement) vs CSI | Both are improvement; CSI is ITIL-specific term |

### ITIL Roles (must know)

| Role | Key Point |
|------|-----------|
| Process Owner | Sets policy — accountable for process design |
| Process Manager | Runs process day-to-day |
| Service Owner | Accountable for service end-to-end |
| CSI Manager | Drives all improvement initiatives |
| CAB | Approves normal changes |
| ECAB | Approves emergency changes (smaller group) |

### Change Types
| Type | Approval | Example |
|------|----------|---------|
| Standard | Pre-approved | Routine password reset |
| Normal | CAB | New server deployment |
| Emergency | ECAB | Security patch |

### ITSCM Recovery Options (cheapest → most expensive)
```
Manual Workarounds → Reciprocal → Cold Standby → Warm Standby → Hot Standby → Mirror
(Slowest)                                                                    (Fastest)
```

### Data Center Tiers
| Tier | Uptime | Key Feature |
|------|--------|-------------|
| I | 99.671% | Basic |
| II | 99.741% | Partial redundancy |
| III | 99.982% | Concurrently maintainable (N+1) |
| IV | 99.995% | Fault-tolerant (2N) |

---

## SECTION 2: DevOps Core (Sessions 6–27)

### Tool Comparison Matrix

| Tool | Type | Push/Pull | Agent? | Language |
|------|------|-----------|--------|---------|
| Chef | Config mgmt | Pull | Yes | Ruby DSL |
| Puppet | Config mgmt | Pull | Yes | Puppet DSL |
| Ansible | Config mgmt | Push | No (SSH) | YAML |
| Terraform | IaC | N/A | No | HCL |
| Jenkins | CI/CD | Event | N/A | Groovy/YAML |
| Docker | Containers | N/A | N/A | N/A |
| Kubernetes | Orchestration | N/A | N/A | YAML |

### Virtualization Quick Facts

| Type | Hypervisors | Guest OS Modified? |
|------|-------------|-------------------|
| Type 1 (Bare Metal) | ESXi, Hyper-V, KVM | No |
| Type 2 (Hosted) | VirtualBox, VMware Workstation | No |
| Para-Virtualization | Xen | Yes (makes hypercalls) |

> KVM = Type 1 (Linux kernel-based)
> Para-virt = Windows cannot be para-virtualized

### Storage Quick Reference
| | SAN | NAS | DAS |
|--|-----|-----|-----|
| Level | Block | File | Block |
| Protocol | FC / iSCSI | NFS / SMB | SATA/SAS |
| Shared | Yes | Yes | No |

### Cloud Service Models (SPI) – Who manages what?
```
SaaS: Provider manages everything. User manages: data + access.
PaaS: User manages: data + access + applications.
IaaS: User manages: data + access + apps + OS + middleware.
```

### Cloud Deployment Models
```
Public  → Anyone, provider-owned (AWS, Azure, GCP)
Private → One org, org-controlled (on-prem VMware)
Hybrid  → Mix with integration/orchestration required
Community → Shared by orgs with common interest
```

### AWS Key Services Summary
| Service | Type | Key Points |
|---------|------|-----------|
| EC2 | Compute | VMs; On-demand/RI/Spot pricing |
| Lambda | Serverless | Event-driven; max 15 min; cold start issue |
| S3 | Object Storage | 11 nines durability; not a file system |
| VPC | Networking | Private network; public/private subnets |
| IAM | Identity | Users, Groups, Roles, Policies |
| EBS | Block Storage | Persistent disk for EC2 |

### AWS Critical Distinctions
| Pair | Distinction |
|------|-------------|
| Security Group vs NACL | SG = stateful, instance; NACL = stateless, subnet |
| Public vs Private Subnet | Defined by route to IGW (not label) |
| NAT Gateway | Outbound only for private subnets |
| Spot Instance | Can be terminated by AWS with 2-min warning |
| IAM Role vs User | Role = for services; User = for humans |

### Docker Quick Reference

| Concept | Key Point |
|---------|-----------|
| Container vs VM | Container shares host OS kernel |
| EXPOSE in Dockerfile | Documents port only — doesn't open it |
| CMD vs ENTRYPOINT | CMD = overridable default; ENTRYPOINT = main process |
| Volume vs Bind Mount | Volume = Docker-managed; Bind = host path |
| latest tag | Just a convention, not automatic |
| depends_on | Waits for container START, not app readiness |

**Dockerfile instruction order matters — each instruction = new layer**

### Kubernetes Quick Reference

```
Control Plane: API Server + etcd + Scheduler + Controller Manager
Worker Node: kubelet + kube-proxy + container runtime
```

| K8s Object | Use |
|-----------|-----|
| Pod | Smallest unit (1+ containers) |
| Deployment | Manages ReplicaSets, rolling updates |
| Service (ClusterIP) | Internal access only |
| Service (NodePort) | External via node IP:port |
| Service (LoadBalancer) | External via cloud LB |
| ConfigMap | Non-sensitive config |
| Secret | Sensitive data (base64, NOT encrypted by default) |
| StatefulSet | Stateful apps (databases) |
| DaemonSet | One pod per node |
| HPA | Auto-scale based on metrics |

**Probe Types:**
```
Liveness → Fails → Restart container
Readiness → Fails → Remove from Service (no restart)
Startup → Fails → Kill container (for slow-starting apps)
```

### Terraform Workflow
```
init → validate → fmt → plan → apply → destroy
```

| Command | What it does |
|---------|-------------|
| `terraform init` | Download providers, set up backend |
| `terraform plan` | Preview changes (no changes made) |
| `terraform apply` | Create/update infrastructure |
| `terraform destroy` | Remove all infrastructure |
| `terraform state list` | List all managed resources |
| `terraform import` | Import existing resource into state |
| `terraform refresh` | Sync state with real world |

**Critical Terraform Rules:**
- NEVER commit `terraform.tfstate` to Git
- NEVER hardcode credentials in `.tf` files
- Use remote backend (S3 + DynamoDB) for teams
- DynamoDB = state locking (prevents concurrent apply)
- `terraform state rm` = removes from state only (NOT from cloud)

### Prometheus Quick Reference

| Component | Port | Role |
|-----------|------|------|
| Prometheus | 9090 | Scrapes, stores, queries |
| Node Exporter | 9100 | Linux metrics |
| Windows Exporter | 9182 | Windows metrics |
| Grafana | 3000 | Visualization (NOT storage) |

**Metric Types:**
```
Counter → Only increases (use rate())
Gauge → Up or down (current value)
Histogram → Observations in buckets
Summary → Quantiles
```

**PromQL rules:**
- `rate()` → for counters, use for alerting
- `irate()` → for counters, use for graphing (spiky)
- High cardinality labels = performance problems

### Git Quick Reference

```
Working Directory → (git add) → Staging Area → (git commit) → Repository
```

| Command | Action |
|---------|--------|
| `git init` | Create new repo |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Save snapshot |
| `git push origin main` | Upload to remote |
| `git pull origin main` | Fetch + merge |
| `git fetch origin` | Download only (no merge) |
| `git checkout -b branch` | Create and switch |
| `git merge branch` | Merge branch into current |
| `git rebase` | NEVER on public branches |

**Pull Request = GitHub feature, NOT Git feature**

---

## SECTION 3: Agile & DevOps Concepts

### Scrum vs Kanban

| | Scrum | Kanban |
|--|-------|--------|
| Iterations | Sprints (1-4 weeks) | Continuous flow |
| Planning | Sprint planning | As needed |
| Estimation | Story points | Optional |
| WIP Limits | No | Yes (key feature) |
| Roles | PO, SM, Dev team | Optional |

### CI/CD Ladder
```
Continuous Integration → Continuous Delivery → Continuous Deployment
     (always builds)     (deployable, manual)    (auto to prod)
```

### DevOps CALMS Framework
```
Culture → Automation → Lean → Measurement → Sharing
```

---

## TOP 30 EXAM TRAPS – MUST MEMORIZE

1. ITIL = framework, NOT a standard
2. Utility + Warranty BOTH needed for value
3. Service Catalog ≠ Service Portfolio
4. Charging is OPTIONAL in ITIL
5. SLA is NOT a legal contract (UC with vendors is)
6. Process Owner ≠ Process Manager
7. CMDB ≠ CMS (CMDB is inside CMS)
8. Known Error = problem with root cause identified (not resolved)
9. Incident = restore fast; Problem = find root cause
10. Snapshot ≠ Backup (snapshots chain up, degrade performance)
11. EXPOSE doesn't open port (use -p flag)
12. CMD = overridable; ENTRYPOINT = main process
13. depends_on = container start, NOT app readiness
14. Container shares host OS kernel (not own OS)
15. latest tag is just a convention
16. KVM = Type 1 hypervisor
17. Para-virt needs modified guest OS (no Windows)
18. SAN = block; NAS = file (very common exam Q)
19. iSCSI runs over TCP/IP (not Fibre Channel)
20. K8s Secrets = base64 (NOT encrypted by default)
21. Liveness probe fail = restart; Readiness fail = remove from traffic
22. etcd = brain of Kubernetes (stores all cluster state)
23. Spot Instance can be terminated with 2-min warning
24. Security Group = stateful; NACL = stateless
25. Subnet is public because it routes to IGW (not because it's labeled)
26. Lambda max execution = 15 minutes
27. terraform plan = preview only (no changes)
28. terraform state rm = removes from state only (NOT from cloud)
29. NEVER commit terraform.tfstate to Git
30. Pull Request = GitHub feature, NOT Git feature
