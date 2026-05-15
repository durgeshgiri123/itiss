# Sessions 9–11: Chef, Puppet & Prometheus Monitoring

---

# SESSIONS 9 & 10 – CHEF & PUPPET

---

## 1. Configuration Management Tools Overview

**Problem they solve:** Managing configuration across hundreds/thousands of servers manually is error-prone, slow, and inconsistent.

**Configuration Management tools** automate the enforcement of desired system state.

| Tool | Language | Agent needed? | Push or Pull? | Model |
|------|---------|--------------|--------------|-------|
| **Chef** | Ruby (DSL) | Yes | Pull | Procedural-ish (Ruby code) |
| **Puppet** | Puppet DSL / Ruby | Yes | Pull | Declarative |
| **Ansible** | YAML | No (agentless) | Push | Declarative |
| **SaltStack** | YAML/Python | Optional | Push/Pull | Declarative |

> ⚠️ **Tricky Q:** Chef and Puppet are **pull-based** — agents check in with the server regularly. Ansible is **push-based** — control machine pushes to nodes via SSH.

---

## 2. Chef Architecture

```
Chef Workstation → (uploads cookbooks) → Chef Server → (pulls config) → Chef Nodes
```

### Key Components:

| Component | Description |
|-----------|-------------|
| **Chef Workstation** | Where developer writes cookbooks; runs `knife` commands |
| **Chef Server** | Central hub; stores cookbooks, policies, node data |
| **Chef Node** | Any machine managed by Chef; runs `chef-client` |
| **chef-client** | Agent on node; pulls + applies config from server |
| **knife** | CLI tool to interact with Chef Server |
| **Ohai** | Collects node data (facts) — OS, IP, CPU, etc. |

### Chef Terminology:

| Term | Description | Analogy |
|------|-------------|---------|
| **Recipe** | Set of resources/instructions for one task | A single dish recipe |
| **Cookbook** | Collection of recipes for a service | A recipe book |
| **Resource** | Smallest unit (install package, create file) | Ingredient |
| **Role** | Grouping of recipes for a server type | Chef's specialty |
| **Run List** | Ordered list of recipes/roles to apply to a node | Today's menu |
| **Attribute** | Variable/configuration value | Recipe parameters |
| **Environment** | Separate configs for dev/staging/prod | Kitchen section |
| **Data Bag** | Global variable storage (secrets, configs) | Pantry |

> ⚠️ **Tricky Q:** A **Data Bag** stores sensitive shared data (e.g., passwords) centrally. Use **encrypted data bags** for secrets. Storing passwords in cookbook attributes is insecure.

---

## 3. Chef Idempotency

**Idempotency** = Applying the same configuration multiple times produces the same result — no side effects.

Example: Chef resource `package 'nginx' do action :install end`
- First run: installs nginx
- Second run: nginx already installed → does nothing (doesn't reinstall)

> ⚠️ **Tricky Q:** Chef's built-in resources are idempotent. If you write custom `bash` or `execute` resources, YOU are responsible for making them idempotent using `not_if` or `only_if` guards.

---

## 4. Puppet Architecture

```
Puppet Agent (Node) → (requests catalog) → Puppet Master → (compiles + sends catalog) → Agent applies
```

### Key Components:

| Component | Description |
|-----------|-------------|
| **Puppet Master** | Compiles and serves catalogs |
| **Puppet Agent** | Runs on nodes; applies catalog every 30 minutes |
| **Catalog** | Compiled set of resources to apply to a node |
| **Facter** | Gathers system facts (like Ohai in Chef) |
| **PuppetDB** | Stores facts, catalogs, and reports |
| **Hiera** | Hierarchical data lookup system for variables |

### Puppet Terminology:

| Term | Description |
|------|-------------|
| **Manifest** | `.pp` file containing Puppet code |
| **Module** | Collection of manifests, templates, and files |
| **Class** | Named block of Puppet code (like a function) |
| **Resource** | Individual configurable item (file, package, service) |
| **Node Definition** | Assigns classes to specific nodes |
| **Hiera** | External data store for variables (separates data from code) |

> ⚠️ **Tricky Q:** Puppet uses a **declarative model** — you describe *what* the state should be, not *how* to get there. Puppet figures out the steps.

---

## 5. Chef vs Puppet – Key Differences

| Feature | Chef | Puppet |
|---------|------|--------|
| **Language** | Ruby DSL | Puppet DSL (Ruby-based) |
| **Learning curve** | Higher (Ruby knowledge helps) | Moderate |
| **Approach** | More procedural flexibility | Purely declarative |
| **Community** | Large, DevOps-focused | Large, enterprise-focused |
| **Execution order** | Ordered (run list) | Dependency-resolved (graph) |

> ⚠️ **Tricky Q:** In Puppet, resources are applied in **dependency order** (not the order they appear in code). In Chef, the run list is executed **sequentially** in order.

---

## 6. Desired State Configuration (DSC)

**Desired State Configuration** = Define the desired state of a system; the tool enforces it continuously.

- If someone manually changes a server (drift), the tool detects and corrects it
- This is also called **configuration drift correction**

**Chef uses:** `converge` (chef-client run)
**Puppet uses:** `catalog application` (puppet agent run)

> ⚠️ **Tricky Q:** DSC is a concept, not just a Microsoft technology (though Microsoft also has a tool called DSC for Windows). Chef and Puppet both implement DSC principles.

---

## 7. Infrastructure Provisioning

- Chef and Puppet handle **configuration** of existing servers
- For **provisioning** (creating servers), they integrate with:
  - **Terraform** (cloud provisioning)
  - **Vagrant** (local VM provisioning)
  - **cloud APIs** (AWS, Azure, GCP)

**Workflow:**
```
Terraform provisions EC2 → Chef/Puppet configures it → App deployed
```

---

# SESSION 11 – PROMETHEUS MONITORING

---

## 1. What is Prometheus?

**Prometheus** = Open-source monitoring and alerting system originally built by SoundCloud, now a **CNCF** project.

**Key characteristics:**
- **Pull-based** monitoring (scrapes metrics from targets)
- **Time-series database** built-in
- **Multi-dimensional data model** (labels)
- **PromQL** — powerful query language
- **No external dependencies** — standalone binary

> ⚠️ **Tricky Q:** Prometheus is **pull-based** (it scrapes targets). This is the opposite of traditional monitoring tools like Nagios (push) or Graphite (push). Exception: **Pushgateway** allows push for short-lived jobs.

---

## 2. Prometheus Architecture

```
Target Servers (exporters) ← scrape ← Prometheus Server → AlertManager → Notifications
                                                        ↓
                                                    Grafana (visualization)
```

### Components:

| Component | Role |
|-----------|------|
| **Prometheus Server** | Scrapes, stores, and queries metrics |
| **Exporters** | Expose metrics in Prometheus format |
| **Pushgateway** | Accepts pushed metrics (for short-lived jobs) |
| **AlertManager** | Handles alerts (routing, deduplication, silencing) |
| **Grafana** | Visualization dashboards (separate tool) |

---

## 3. Key Concepts

### Metrics Types:

| Type | Description | Example |
|------|-------------|---------|
| **Counter** | Only increases (resets on restart) | HTTP requests total |
| **Gauge** | Can go up or down | Current CPU usage, memory |
| **Histogram** | Samples observations in buckets | Request duration distribution |
| **Summary** | Similar to histogram, calculates quantiles | 95th percentile latency |

> ⚠️ **Tricky Q:** **Counter** never decreases (except on restart). **Gauge** can go up or down. Use `rate()` on Counters, NOT on Gauges.

### Labels:
- Labels add dimensions to metrics
- Example: `http_requests_total{method="GET", status="200"}`
- Labels allow filtering and grouping in queries

> ⚠️ **Tricky Q:** High-cardinality labels (e.g., user_id with millions of users) cause performance problems — avoid them!

---

## 4. Prometheus Installation

**Basic setup:**
```bash
# Download Prometheus binary
wget https://github.com/prometheus/prometheus/releases/download/v2.x.x/prometheus-2.x.x.linux-amd64.tar.gz
tar xvf prometheus-*.tar.gz
cd prometheus-*

# Start Prometheus
./prometheus --config.file=prometheus.yml
```

**prometheus.yml structure:**
```yaml
global:
  scrape_interval: 15s          # How often to scrape targets

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

> ⚠️ **Tricky Q:** Default Prometheus web UI port = **9090**. Node Exporter default port = **9100**. Windows Exporter default = **9182**.

---

## 5. Node Exporter (Linux)

**Node Exporter** = Collects hardware and OS metrics from Linux/Unix systems.

**Key metrics collected:**

| Category | Metrics |
|----------|---------|
| **CPU** | `node_cpu_seconds_total` |
| **Memory** | `node_memory_MemAvailable_bytes` |
| **Disk** | `node_disk_io_time_seconds_total` |
| **Network** | `node_network_receive_bytes_total` |
| **Filesystem** | `node_filesystem_avail_bytes` |
| **Load** | `node_load1`, `node_load5`, `node_load15` |

**Install Node Exporter:**
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.x.x/node_exporter-1.x.x.linux-amd64.tar.gz
tar xvf node_exporter-*.tar.gz
./node_exporter &
```

---

## 6. Windows Exporter

**Windows Exporter** (formerly **WMI Exporter**) = Collects Windows OS metrics.

- Exposes metrics at `http://localhost:9182/metrics`
- Collects CPU, memory, disk, network, IIS, .NET, etc.

---

## 7. PromQL – Prometheus Query Language

**Key PromQL queries:**

```promql
# CPU usage percentage (all cores)
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Available memory in GB
node_memory_MemAvailable_bytes / 1024 / 1024 / 1024

# Disk usage %
100 - (node_filesystem_avail_bytes / node_filesystem_size_bytes * 100)

# Network bytes received per second
rate(node_network_receive_bytes_total[5m])

# HTTP request rate
rate(http_requests_total[5m])
```

**PromQL functions:**

| Function | Use |
|----------|-----|
| `rate()` | Per-second rate of change (for counters over time range) |
| `irate()` | Instantaneous rate (last two data points) |
| `increase()` | Total increase over time range |
| `avg_over_time()` | Average value over time range |
| `histogram_quantile()` | Calculate percentiles from histograms |

> ⚠️ **Tricky Q:** Use `rate()` not `irate()` for alerting — `irate()` is too spiky. Use `irate()` for graphing where you want to see sudden spikes.

---

## 8. AlertManager

**AlertManager** handles alerts fired by Prometheus.

**Key features:**
- **Routing** – Send alerts to different channels based on labels
- **Grouping** – Batch related alerts into one notification
- **Inhibition** – Suppress alerts when higher-severity alert is firing
- **Silencing** – Temporarily mute alerts during maintenance

**Notification channels:** Email, Slack, PagerDuty, OpsGenie, Webhook

**Alert rule example:**
```yaml
groups:
  - name: node_alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High CPU usage detected on {{ $labels.instance }}"
```

> ⚠️ **Tricky Q:** The `for` field means the condition must be *continuously true* for that duration before firing. This prevents false alerts from momentary spikes.

---

## 9. Metrics Visualization with Grafana

**Grafana** = Popular open-source visualization tool.
- Supports Prometheus as a data source
- Create dashboards with panels (graphs, gauges, tables)
- Default port: **3000**

> ⚠️ **Tricky Q:** Grafana does NOT store data — it only *visualizes* data from Prometheus (or other data sources). Prometheus is the database; Grafana is the frontend.

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Chef is pull or push? | Pull (chef-client pulls from Chef Server) |
| Ansible is pull or push? | Push (control machine SSHes to nodes) |
| Chef's system fact collector? | Ohai |
| Puppet's system fact collector? | Facter |
| Chef recipe vs cookbook? | Recipe = single task; Cookbook = collection of recipes |
| Puppet execution order? | Dependency order (not code order) |
| Chef execution order? | Sequential (run list order) |
| What is idempotency? | Applying config multiple times = same result |
| Prometheus is pull or push? | Pull (scrapes targets) |
| Node Exporter default port? | 9100 |
| Prometheus default port? | 9090 |
| Windows Exporter port? | 9182 |
| Counter vs Gauge? | Counter only increases; Gauge can go up/down |
| `rate()` vs `irate()`? | rate() for alerts; irate() for graphs |
| AlertManager `for` field? | Condition must be true for X duration before alerting |
| Grafana stores data? | No — it only visualizes from Prometheus |
| Data Bag in Chef? | Global variable/secret storage |
| Hiera in Puppet? | Hierarchical data store for variables |
| DSC in Chef/Puppet? | Desired State Configuration — enforce desired state continuously |
