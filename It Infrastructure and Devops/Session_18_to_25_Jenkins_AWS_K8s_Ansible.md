# Sessions 18–25: Jenkins, AWS, Kubernetes/Swarm & Ansible

---

# SESSIONS 18 & 19 – JENKINS

---

## 1. What is Jenkins?

**Jenkins** = Open-source automation server for CI/CD pipelines.

**Key features:**
- Automates build, test, and deploy
- Plugin ecosystem (1800+ plugins)
- Supports pipeline-as-code (Jenkinsfile)
- Integrates with Git, Docker, Kubernetes, and more

> ⚠️ **Tricky Q:** Jenkins itself is NOT a version control system — it integrates with Git/GitHub. Jenkins orchestrates the pipeline; Git stores the code.

---

## 2. Jenkins Installation

**Prerequisites:** Java (JDK 11 or 17)

```bash
# On Ubuntu
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

**Default port:** `8080`
**Initial password:** `/var/lib/jenkins/secrets/initialAdminPassword`

---

## 3. Jenkins Architecture

```
Developer pushes code → GitHub webhook → Jenkins master → Jenkins agent → Build/Test/Deploy
```

| Component | Role |
|-----------|------|
| **Jenkins Master** | Orchestrates pipelines, web UI, scheduling |
| **Jenkins Agent (Slave)** | Executes the build jobs |
| **Executor** | Thread on an agent that runs builds |
| **Workspace** | Directory where build happens on agent |
| **Plugin** | Extends Jenkins functionality |

> ⚠️ **Tricky Q:** The master should NOT run builds — only orchestrate. Build work belongs on **agents** for performance and security.

---

## 4. Jenkins Job Types

| Type | Description |
|------|-------------|
| **Freestyle Job** | Simple, GUI-configured build |
| **Pipeline** | Code-defined pipeline (Jenkinsfile) |
| **Multibranch Pipeline** | Auto-creates pipelines for each Git branch |
| **GitHub Organization** | Scans entire GitHub org for Jenkinsfiles |

> ⚠️ **Tricky Q:** **Multibranch Pipeline** automatically creates a pipeline for each branch that has a `Jenkinsfile`. This is the recommended approach for teams using feature branches.

---

## 5. Jenkinsfile (Pipeline as Code)

### Declarative Pipeline:
```groovy
pipeline {
    agent any                        // Run on any available agent

    environment {
        APP_NAME = 'myapp'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/user/repo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t ${APP_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${APP_NAME}:${BUILD_NUMBER} npm test'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker push ${APP_NAME}:${BUILD_NUMBER}'
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            mail to: 'team@example.com', subject: 'Build Failed'
        }
        always {
            cleanWs()                // Clean workspace
        }
    }
}
```

**Key sections:**

| Section | Purpose |
|---------|---------|
| `agent` | Where pipeline runs |
| `environment` | Define environment variables |
| `stages` | Collection of stages |
| `stage` | Individual phase (Build, Test, Deploy) |
| `steps` | Commands within a stage |
| `post` | Actions after pipeline completes |

---

## 6. GitHub Integration

**Webhook setup:**
1. In GitHub repo: Settings → Webhooks → Add webhook
2. URL: `http://jenkins-server:8080/github-webhook/`
3. Content type: `application/json`
4. Events: Push, Pull Request

**Jenkins side:**
- Install **GitHub plugin**
- Configure GitHub credentials
- Enable "GitHub hook trigger for GITScm polling"

> ⚠️ **Tricky Q:** Polling vs Webhook — **polling** (Jenkins checks GitHub every N minutes) wastes resources. **Webhooks** (GitHub calls Jenkins when there's a push) are preferred and more efficient.

---

## 7. Docker Integration with Jenkins

```groovy
stage('Build Docker Image') {
    steps {
        script {
            docker.build("myapp:${env.BUILD_NUMBER}")
        }
    }
}

stage('Push to Registry') {
    steps {
        script {
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                docker.image("myapp:${env.BUILD_NUMBER}").push()
                docker.image("myapp:${env.BUILD_NUMBER}").push('latest')
            }
        }
    }
}
```

---

# SESSIONS 20 & 21 – AWS

---

## 1. AWS Overview

**AWS (Amazon Web Services)** = World's most comprehensive cloud platform (as of 2024).

**AWS Global Infrastructure:**

| Component | Description |
|-----------|-------------|
| **Region** | Geographic area with multiple AZs (e.g., ap-south-1 = Mumbai) |
| **Availability Zone (AZ)** | Isolated data center within a region |
| **Edge Location** | CDN node (CloudFront), closer to users |
| **Local Zone** | Extension of a region for low latency |

> ⚠️ **Tricky Q:** Each **Region** has minimum 2, typically 3+ AZs. AZs are **physically separate** but connected via low-latency private links. Deploying across AZs = High Availability. Deploying across Regions = Disaster Recovery.

---

## 2. EC2 (Elastic Compute Cloud)

**EC2** = Virtual servers in AWS.

### Instance Types:

| Family | Optimized for | Example |
|--------|--------------|---------|
| **t (Burstable)** | General dev/test, variable load | t3.micro |
| **m (General)** | Balanced compute/memory | m5.large |
| **c (Compute)** | CPU-intensive apps | c5.xlarge |
| **r (Memory)** | Memory-intensive (databases) | r5.xlarge |
| **i (Storage)** | High I/O (NVMe SSD) | i3.large |
| **p/g (GPU)** | ML/GPU compute | p3.2xlarge |

### EC2 Pricing Models:

| Model | Description | Discount |
|-------|-------------|---------|
| **On-Demand** | Pay per hour/second, no commitment | None |
| **Reserved Instances (RI)** | 1 or 3 year commitment | Up to 75% |
| **Spot Instances** | Bid for unused capacity | Up to 90% (but can be interrupted) |
| **Savings Plans** | Flexible commitment | Up to 66% |
| **Dedicated Host** | Physical server dedicated to you | Premium pricing |

> ⚠️ **Tricky Q:** **Spot Instances can be terminated** with 2-minute warning when AWS needs the capacity back. Never use Spot for stateful workloads or databases!

### EC2 Key Concepts:

| Concept | Description |
|---------|-------------|
| **AMI** | Amazon Machine Image — template for EC2 instances |
| **Security Group** | Stateful virtual firewall for EC2 |
| **Key Pair** | SSH access (public/private key) |
| **Elastic IP** | Static public IP address |
| **EBS** | Elastic Block Store — persistent disk for EC2 |
| **Instance Store** | Temporary local storage (lost if stopped) |
| **User Data** | Script that runs at instance launch |

> ⚠️ **Tricky Q:** **Security Groups are stateful** — if you allow inbound traffic, the response is automatically allowed outbound (no need for outbound rule). **NACLs are stateless** — you need explicit inbound AND outbound rules.

---

## 3. AWS Lambda

**Lambda** = Serverless compute — run code without managing servers.

**Key characteristics:**
- Event-driven (triggered by S3, API Gateway, DynamoDB, etc.)
- Pay per invocation + duration (milliseconds)
- Auto-scales automatically
- Supports: Python, Node.js, Java, Go, Ruby, .NET, and more

**Lambda limits:**
- **Max execution time:** 15 minutes
- **Memory:** 128 MB – 10 GB
- **Deployment package:** 50 MB (zipped), 250 MB (unzipped)

> ⚠️ **Tricky Q:** Lambda has a **cold start** problem — the first invocation after idle takes longer because AWS initializes the execution environment. Solution: **Provisioned Concurrency** (keeps Lambda warm, but costs more).

---

## 4. S3 (Simple Storage Service)

**S3** = Object storage service — store any file (object) in buckets.

**Key concepts:**

| Concept | Description |
|---------|-------------|
| **Bucket** | Container for objects (globally unique name) |
| **Object** | File + metadata stored in S3 |
| **Key** | Full path of object within bucket |
| **Prefix** | "Folder" in S3 (S3 is flat, no real folders) |

**S3 Storage Classes:**

| Class | Use Case | Min Duration | Cost |
|-------|---------|-------------|------|
| **S3 Standard** | Frequent access | None | Highest |
| **S3-IA (Infrequent Access)** | Monthly access | 30 days | Lower, retrieval fee |
| **S3 One Zone-IA** | Non-critical, IA | 30 days | Lowest IA |
| **S3 Glacier** | Archival | 90 days | Very low, slow retrieval |
| **S3 Glacier Deep Archive** | Long-term archive | 180 days | Lowest |
| **S3 Intelligent-Tiering** | Unknown access pattern | None | Auto-moves tiers |

> ⚠️ **Tricky Q:** S3 is **not a file system** — it's object storage. You access objects by key (path), not by mounting. There are no real directories.

> ⚠️ **Tricky Q:** S3 Standard provides **11 nines of durability** (99.999999999%). This means if you store 10 million objects, you'd expect to lose 1 object per 10,000 years.

---

## 5. VPC (Virtual Private Cloud)

**VPC** = Isolated, logically-defined network in AWS.

### VPC Components:

| Component | Description |
|-----------|-------------|
| **VPC** | Your private network in AWS |
| **Subnet** | Subdivision of VPC within one AZ |
| **Internet Gateway (IGW)** | Enables internet access for public subnets |
| **NAT Gateway** | Allows private subnet outbound internet (no inbound) |
| **Route Table** | Rules for routing traffic |
| **Security Group** | Instance-level stateful firewall |
| **NACL** | Subnet-level stateless firewall |
| **VPC Peering** | Connect two VPCs |
| **VPN Gateway** | Connect on-premises to AWS |

### Public vs Private Subnet:

| | Public Subnet | Private Subnet |
|--|--------------|----------------|
| **Route to** | Internet Gateway | NAT Gateway (outbound only) |
| **Public IP** | Yes (auto-assign) | No |
| **Use for** | Load balancers, web servers | Databases, app servers |

> ⚠️ **Tricky Q:** A **subnet is public** because it has a route to an Internet Gateway — NOT because it's labeled "public." The route table makes it public.

> ⚠️ **Tricky Q:** **NAT Gateway** allows private instances to initiate outbound internet connections (e.g., updates) but **blocks all inbound** connections from internet.

### Default VPC:
- AWS creates a default VPC in each region
- All subnets are public (routes to IGW)
- Not recommended for production

---

# SESSIONS 22 & 23 – CONTAINER ORCHESTRATION

---

## 1. Kubernetes (K8s)

**Kubernetes** = Open-source container orchestration system (originally from Google, now CNCF).

**Why Kubernetes?** Docker alone can't:
- Automatically restart failed containers
- Scale containers based on load
- Distribute containers across multiple hosts
- Perform rolling updates
- Do load balancing across containers

### Kubernetes Architecture:

**Control Plane (Master):**

| Component | Role |
|-----------|------|
| **API Server** | Entry point for all K8s commands |
| **etcd** | Distributed key-value store (cluster state) |
| **Scheduler** | Assigns pods to nodes |
| **Controller Manager** | Ensures desired state (restarts failed pods, etc.) |
| **Cloud Controller** | Integrates with cloud provider APIs |

**Worker Node:**

| Component | Role |
|-----------|------|
| **kubelet** | Agent on each node; runs pods |
| **kube-proxy** | Network rules for pod communication |
| **Container Runtime** | Actually runs containers (containerd, CRI-O, Docker) |

> ⚠️ **Tricky Q:** **etcd** is the brain of Kubernetes — it stores ALL cluster state. If etcd fails, the cluster can't make decisions. Always back up etcd!

---

## 2. Kubernetes Objects

| Object | Description |
|--------|-------------|
| **Pod** | Smallest unit — one or more containers sharing network/storage |
| **ReplicaSet** | Ensures N copies of a pod are running |
| **Deployment** | Manages ReplicaSets, enables rolling updates |
| **Service** | Stable network endpoint for pods |
| **ConfigMap** | Store non-sensitive config |
| **Secret** | Store sensitive data (base64 encoded) |
| **Namespace** | Virtual cluster within a cluster |
| **PersistentVolume (PV)** | Cluster-wide storage resource |
| **PersistentVolumeClaim (PVC)** | Pod's request for storage |
| **StatefulSet** | For stateful apps (databases) — stable network identity |
| **DaemonSet** | Run one pod per node (logging, monitoring) |
| **Job / CronJob** | Run tasks to completion |
| **Ingress** | HTTP routing rules to services |

> ⚠️ **Tricky Q:** **Pods are ephemeral** — they can die and be replaced. The new pod gets a NEW IP. That's why you need a **Service** — it provides a stable virtual IP even as pods come and go.

> ⚠️ **Tricky Q:** **Secrets** in K8s are NOT encrypted by default — they're base64 encoded (not the same as encryption!). Use RBAC and etcd encryption to properly secure Secrets.

---

## 3. Kubernetes Services

| Service Type | Description | Access |
|-------------|-------------|--------|
| **ClusterIP** | Internal only | Within cluster |
| **NodePort** | Exposes on each node's IP + port | External (node IP:port) |
| **LoadBalancer** | Provisions cloud load balancer | External (cloud LB IP) |
| **ExternalName** | Maps to external DNS | External DNS |

> ⚠️ **Tricky Q:** **ClusterIP** is the default service type — it only allows internal cluster access. NodePort is built on top of ClusterIP; LoadBalancer is built on top of NodePort.

---

## 4. Rolling Updates and Rollback

```bash
# Create deployment
kubectl create deployment myapp --image=myapp:1.0

# Update image (triggers rolling update)
kubectl set image deployment/myapp myapp=myapp:2.0

# Check rollout status
kubectl rollout status deployment/myapp

# Rollback
kubectl rollout undo deployment/myapp

# Rollback to specific version
kubectl rollout undo deployment/myapp --to-revision=2
```

**Rolling update strategy (default):**
- Incrementally replaces old pods with new ones
- Zero downtime if configured correctly
- `maxSurge` — extra pods during update
- `maxUnavailable` — how many can be down during update

> ⚠️ **Tricky Q:** **Rolling update** is the default strategy. **Recreate** strategy kills ALL old pods before creating new ones (causes downtime). **Blue-Green** and **Canary** are external patterns, not native K8s strategies.

---

## 5. Scaling

```bash
# Manual scaling
kubectl scale deployment myapp --replicas=5

# Horizontal Pod Autoscaler (HPA)
kubectl autoscale deployment myapp --min=2 --max=10 --cpu-percent=70
```

**HPA (Horizontal Pod Autoscaler):**
- Scales pods based on CPU/memory/custom metrics
- Requires **Metrics Server** installed

**VPA (Vertical Pod Autoscaler):**
- Adjusts CPU/memory requests of individual pods
- Doesn't scale number of pods

---

## 6. Self-Healing

Kubernetes automatically:
- Restarts failed containers
- Replaces pods on failed nodes
- Reschedules pods if a node goes down
- Kills pods that fail health checks

**Health checks:**

| Check Type | What it tests | Failure action |
|-----------|--------------|---------------|
| **Liveness Probe** | Is the app alive? | Restart container |
| **Readiness Probe** | Is the app ready for traffic? | Remove from Service endpoints |
| **Startup Probe** | Has the app started? | Kill container if startup takes too long |

> ⚠️ **Tricky Q:** **Readiness probe** failure = traffic stops going to that pod (but container is NOT restarted). **Liveness probe** failure = container is **restarted**. These are very commonly confused.

---

## 7. Docker Swarm

**Docker Swarm** = Docker's native clustering and orchestration.

| Feature | Docker Swarm | Kubernetes |
|---------|-------------|-----------|
| **Complexity** | Simple | Complex |
| **Features** | Basic | Comprehensive |
| **Setup** | Easy | Harder |
| **Scaling** | Good | Excellent |
| **Community** | Smaller | Massive |
| **Production use** | Small-medium | Large-scale |

**Swarm key terms:**

| Term | Description |
|------|-------------|
| **Node** | Machine in the swarm |
| **Manager Node** | Orchestrates the cluster |
| **Worker Node** | Runs tasks |
| **Service** | Definition of what to run |
| **Task** | Running container on a node |
| **Stack** | Multi-service application (like docker-compose for swarm) |

```bash
# Initialize swarm
docker swarm init

# Add worker node
docker swarm join --token SWMTKN-... manager-ip:2377

# Deploy service
docker service create --name web --replicas 3 -p 80:80 nginx

# Scale service
docker service scale web=5

# Deploy stack
docker stack deploy -c docker-compose.yml mystack
```

---

# SESSIONS 24 & 25 – ANSIBLE

---

## 1. Introduction to Ansible

**Ansible** = Open-source IT automation tool by Red Hat.

**Key features:**
- **Agentless** — uses SSH (no software on managed nodes)
- **Push-based** — control machine pushes to nodes
- **Declarative YAML** — easy to read and write
- **Idempotent** — safe to run multiple times

> ⚠️ **Tricky Q:** Ansible is **agentless** — this is its biggest differentiator from Chef and Puppet which require agents on every node.

---

## 2. Ansible Architecture

```
Control Node (Ansible installed) → SSH → Managed Nodes (just need Python + SSH)
```

| Component | Description |
|-----------|-------------|
| **Control Node** | Machine where Ansible is installed and run from |
| **Managed Node** | Target machine being automated |
| **Inventory** | List of managed nodes |
| **Playbook** | YAML file with automation instructions |
| **Play** | Set of tasks mapped to hosts |
| **Task** | Single unit of work (call a module) |
| **Module** | Pre-built functions (install package, copy file, etc.) |
| **Role** | Reusable set of tasks, handlers, templates |
| **Handler** | Task that runs only when notified |
| **Fact** | System information gathered by Ansible |

---

## 3. Inventory

**Static Inventory (hosts file):**
```ini
[webservers]
web1.example.com
web2.example.com ansible_host=192.168.1.10

[databases]
db1.example.com ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[production:children]
webservers
databases
```

**Dynamic Inventory:**
- Generates inventory from cloud APIs (AWS, GCP, Azure)
- Used in auto-scaling environments

---

## 4. Playbooks

```yaml
---
- name: Configure Web Servers
  hosts: webservers
  become: yes                      # Run as sudo
  vars:
    http_port: 80
    app_name: myapp

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Nginx
      apt:
        name: nginx
        state: present              # Ensure installed (idempotent)

    - name: Copy website files
      copy:
        src: ./html/
        dest: /var/www/html/
        owner: www-data
        mode: '0644'

    - name: Start and enable Nginx
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Open firewall port
      ufw:
        rule: allow
        port: "{{ http_port }}"
        proto: tcp
      notify: Reload UFW           # Trigger handler

  handlers:
    - name: Reload UFW
      service:
        name: ufw
        state: reloaded
```

**`state` values for `apt` module:**

| State | Action |
|-------|--------|
| `present` | Ensure installed |
| `absent` | Ensure removed |
| `latest` | Ensure latest version |

> ⚠️ **Tricky Q:** **Handlers** run **once** at the end of the play (not immediately when notified). If multiple tasks notify the same handler, it still only runs once.

---

## 5. YAML Basics

```yaml
# Scalar values
name: "John"
age: 30
is_active: true

# List
fruits:
  - apple
  - banana
  - cherry

# Dictionary
server:
  host: 192.168.1.1
  port: 22
  user: admin

# List of dictionaries
users:
  - name: alice
    role: admin
  - name: bob
    role: user

# Multi-line string
message: |
  This is
  a multi-line
  string
```

> ⚠️ **Tricky Q:** YAML uses **spaces, not tabs**. Indentation is critical. A tab character in YAML will cause parsing errors.

---

## 6. Ansible Roles

**Role directory structure:**
```
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml        # Main task list
    ├── handlers/
    │   └── main.yml        # Handlers
    ├── templates/
    │   └── nginx.conf.j2   # Jinja2 templates
    ├── files/
    │   └── index.html      # Static files
    ├── vars/
    │   └── main.yml        # Variables
    ├── defaults/
    │   └── main.yml        # Default variables (lowest priority)
    └── meta/
        └── main.yml        # Role metadata, dependencies
```

**Using a role in playbook:**
```yaml
- hosts: webservers
  roles:
    - webserver
    - database
```

---

## 7. Key Ansible Commands

```bash
# Run a playbook
ansible-playbook playbook.yml -i inventory

# Run with specific host
ansible-playbook playbook.yml -i inventory --limit web1

# Dry run (check mode)
ansible-playbook playbook.yml --check

# Run ad-hoc command
ansible webservers -m ping                          # Ping all web servers
ansible all -m shell -a "uptime"                   # Run shell command
ansible webservers -m apt -a "name=nginx state=present" --become   # Install package

# Check inventory
ansible-inventory -i inventory --list

# View facts
ansible web1 -m setup
```

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Jenkins default port? | 8080 |
| Declarative vs Scripted Pipeline? | Declarative is structured, simpler; Scripted is Groovy, flexible |
| Webhook vs Polling? | Webhook preferred (event-driven, efficient) |
| AWS Region vs AZ? | Region = geographic area; AZ = isolated DC within region |
| Spot Instance risk? | Can be terminated by AWS with 2-min warning |
| Security Group vs NACL? | SG = stateful, instance-level; NACL = stateless, subnet-level |
| S3 is file system? | No — object storage, flat namespace |
| S3 durability? | 11 nines (99.999999999%) |
| Lambda max execution time? | 15 minutes |
| What is cold start in Lambda? | Delay on first invocation after idle period |
| etcd in Kubernetes? | Stores all cluster state |
| Pod vs Deployment? | Pod = runs containers; Deployment = manages pod replicas |
| Liveness vs Readiness probe? | Liveness = restart; Readiness = remove from traffic |
| Service type for external access in AWS? | LoadBalancer |
| K8s Secrets are encrypted? | No — base64 encoded only (not encrypted by default) |
| Ansible agentless means? | Uses SSH, no software needed on managed nodes |
| Handler in Ansible? | Runs once at end of play, only if notified |
| `become: yes` in Ansible? | Run tasks as sudo/root |
| Ansible check mode does? | Dry run — shows what would change without changing |
| Docker Swarm vs K8s? | Swarm = simpler; K8s = more features, production scale |
