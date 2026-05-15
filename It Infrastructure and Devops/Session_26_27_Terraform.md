# Sessions 26–27: Terraform – Infrastructure as Code (IaC)

---

## 1. What is Terraform?

**Terraform** = Open-source Infrastructure as Code (IaC) tool by HashiCorp.

**What it does:** Define, provision, and manage infrastructure across multiple cloud providers using human-readable config files.

**Key advantages:**
- **Cloud-agnostic** — works with AWS, Azure, GCP, and 1000+ providers
- **Declarative** — describe the desired end state
- **State management** — tracks what's been provisioned
- **Idempotent** — safe to run multiple times
- **Plan before apply** — preview changes before making them

> ⚠️ **Tricky Q:** Terraform is **declarative** (you say *what* you want). Tools like Bash scripts are **imperative** (you say *how* to do it). Terraform figures out the steps.

---

## 2. Terraform vs Ansible vs Chef/Puppet

| Tool | Primary Use | Approach | State? |
|------|------------|---------|--------|
| **Terraform** | Provision infrastructure | Declarative IaC | Yes (state file) |
| **Ansible** | Configure servers | Procedural/Declarative | No (stateless) |
| **Chef/Puppet** | Configuration management | Declarative | Yes (server) |

> ⚠️ **Tricky Q:** Terraform **provisions** infrastructure (creates VMs, VPCs, S3 buckets). Ansible **configures** software on existing infrastructure. They complement each other — Terraform first, then Ansible.

---

## 3. Terraform Setup

**Installation:**
```bash
# Ubuntu
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Verify
terraform version
```

**AWS credentials (for Terraform to use):**
```bash
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="ap-south-1"
```

> ⚠️ **Tricky Q:** NEVER put AWS credentials in Terraform files — use environment variables or IAM roles (for EC2/Lambda). Hardcoding credentials is a critical security risk.

---

## 4. Configuration Files

**File extensions:** `.tf` (main) and `.tfvars` (variables)

### Provider Configuration:
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }

  required_version = ">= 1.0"
}

provider "aws" {
  region = "ap-south-1"
}
```

### Variables:
```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "ami_id" {
  description = "AMI ID for the instance"
  type        = string
}

variable "tags" {
  description = "Resource tags"
  type        = map(string)
  default = {
    Environment = "dev"
    Project     = "myapp"
  }
}
```

**Variable types:**

| Type | Example |
|------|---------|
| `string` | `"t3.micro"` |
| `number` | `3` |
| `bool` | `true` |
| `list(string)` | `["us-east-1a", "us-east-1b"]` |
| `map(string)` | `{key = "value"}` |
| `object({...})` | Complex nested type |

---

## 5. Provisioning EC2

```hcl
# main.tf

# Create VPC
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

  tags = {
    Name = "main-vpc"
  }
}

# Create Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main-igw"
  }
}

# Create Public Subnet
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet"
  }
}

# Create Route Table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "public-rt"
  }
}

# Associate Route Table with Subnet
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

# Create Security Group
resource "aws_security_group" "web_sg" {
  name        = "web-sg"
  description = "Allow SSH and HTTP"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"             # All traffic
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-sg"
  }
}

# Create EC2 Instance
resource "aws_instance" "web" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  key_name               = "my-key-pair"

  user_data = <<-EOF
              #!/bin/bash
              apt-get update -y
              apt-get install -y nginx
              systemctl start nginx
              systemctl enable nginx
              EOF

  tags = {
    Name = "web-server"
  }
}
```

---

## 6. Outputs

```hcl
# outputs.tf
output "instance_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.web.public_ip
}

output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.web.id
}

output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}
```

---

## 7. State Management

**Terraform State** = Tracks the current state of deployed infrastructure.

**State file:** `terraform.tfstate` (JSON format)

**What state stores:**
- Resource IDs (e.g., EC2 instance ID)
- Resource attributes
- Dependencies between resources
- Mapping between config and real infrastructure

> ⚠️ **Tricky Q:** The state file may contain **sensitive data** (passwords, keys). NEVER commit `terraform.tfstate` to Git. Add it to `.gitignore`.

> ⚠️ **Tricky Q:** If you manually change infrastructure in AWS console **without** using Terraform, Terraform's state becomes out of sync (called **state drift**). Use `terraform refresh` or `terraform import` to fix.

**Important state commands:**
```bash
terraform state list              # List all resources in state
terraform state show aws_instance.web    # Show details of resource
terraform state rm aws_instance.web      # Remove resource from state (NOT from cloud)
terraform import aws_instance.web i-1234567890abcdef0   # Import existing resource
terraform refresh                  # Sync state with real world
```

---

## 8. Remote Backend (S3)

**Local state** is risky (lost if machine fails, no locking, can't collaborate).

**Remote backend** = Store state file remotely for team collaboration.

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "production/terraform.tfstate"
    region         = "ap-south-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"    # For state locking
  }
}
```

**Why DynamoDB table?**
- Prevents two people from running `terraform apply` simultaneously
- DynamoDB provides **distributed locking**

> ⚠️ **Tricky Q:** Without state locking, two simultaneous `terraform apply` runs could corrupt the state file. The **DynamoDB table** provides locking — this is critical for team environments.

**Setup steps:**
1. Create S3 bucket (with versioning enabled!)
2. Create DynamoDB table (partition key: `LockID`)
3. Add backend config
4. Run `terraform init` to migrate state

---

## 9. Terraform Workflow

```bash
# 1. Initialize (download providers, set up backend)
terraform init

# 2. Validate configuration syntax
terraform validate

# 3. Format code consistently
terraform fmt

# 4. Plan — preview changes without applying
terraform plan
terraform plan -out=tfplan         # Save plan to file

# 5. Apply — create/update infrastructure
terraform apply
terraform apply tfplan              # Apply saved plan (no prompt)
terraform apply -auto-approve       # Skip confirmation

# 6. Destroy — remove all infrastructure
terraform destroy
terraform destroy -auto-approve     # Skip confirmation

# Target specific resource
terraform apply -target=aws_instance.web
terraform destroy -target=aws_instance.web
```

**Workflow diagram:**
```
terraform init → terraform validate → terraform fmt → terraform plan → terraform apply
                                                                              ↓
                                                               terraform destroy (when done)
```

> ⚠️ **Tricky Q:** `terraform plan` does NOT make any changes. It only shows what WOULD happen. Always review the plan before applying.

> ⚠️ **Tricky Q:** `terraform destroy` destroys **ALL** resources managed by that Terraform workspace. Use `-target` to destroy specific resources.

---

## 10. Modules

**Modules** = Reusable, packaged Terraform configurations.

```
modules/
└── ec2/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

**Using a module:**
```hcl
# root main.tf
module "web_server" {
  source        = "./modules/ec2"           # Local module
  instance_type = "t3.micro"
  ami_id        = "ami-0c55b159cbfafe1f0"
  subnet_id     = aws_subnet.public.id
}

# Or from Terraform Registry
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"

  name = "my-vpc"
  cidr = "10.0.0.0/16"
}
```

**Benefits of modules:**
- Reusability across environments (dev/staging/prod)
- Consistent standards
- Abstraction — hide complexity
- Easier testing

---

## 11. Data Sources

**Data sources** = Read existing infrastructure (not managed by Terraform).

```hcl
# Get latest Amazon Linux 2 AMI
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# Use in resource
resource "aws_instance" "web" {
  ami = data.aws_ami.amazon_linux.id
  ...
}
```

> ⚠️ **Tricky Q:** Data sources **read** existing resources — they don't create or manage them. Resources **create and manage** infrastructure.

---

## 12. Terraform Variables Files

```hcl
# terraform.tfvars (auto-loaded)
instance_type = "t3.small"
ami_id        = "ami-0c55b159cbfafe1f0"

tags = {
  Environment = "production"
  Project     = "ecommerce"
}
```

```bash
# Use specific var file
terraform apply -var-file="production.tfvars"

# Override individual variable
terraform apply -var="instance_type=t3.large"
```

**Variable precedence (highest to lowest):**
1. `-var` flag
2. `-var-file` flag
3. `terraform.tfvars`
4. `TF_VAR_` environment variables
5. Default value in `variable` block

---

## 13. Terraform Workspace

**Workspaces** = Multiple state files within same config (dev/staging/prod).

```bash
terraform workspace list              # List workspaces
terraform workspace new staging       # Create new workspace
terraform workspace select staging    # Switch to workspace
terraform workspace show              # Show current workspace
```

```hcl
# Use workspace name in config
resource "aws_instance" "web" {
  instance_type = terraform.workspace == "production" ? "t3.large" : "t3.micro"
  ...
}
```

> ⚠️ **Tricky Q:** Workspaces use **separate state files** but the **same configuration**. For truly separate environments with different configs, use separate directories instead.

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| Terraform is declarative or imperative? | Declarative |
| Terraform vs Ansible — main difference? | Terraform = provision infra; Ansible = configure software |
| What does `terraform init` do? | Downloads providers, sets up backend |
| What does `terraform plan` do? | Previews changes without applying |
| What does `terraform apply` do? | Creates/updates infrastructure |
| What does `terraform destroy` do? | Removes all managed infrastructure |
| State file stores what? | Current state of all managed resources |
| Why use remote backend (S3)? | Team collaboration, prevent loss, state locking |
| Why DynamoDB with S3 backend? | State locking (prevents concurrent apply) |
| State drift? | Infra changed outside Terraform; state is stale |
| Module purpose? | Reusable, packaged Terraform configs |
| Data source vs Resource? | Data = read existing; Resource = create/manage |
| Never put in state file in Git? | Yes — may contain sensitive data |
| Variable precedence top 2? | -var flag, then -var-file flag |
| `terraform fmt` does? | Formats .tf files to canonical style |
| `terraform validate` does? | Checks syntax validity |
| How to import existing resource? | `terraform import resource_type.name resource_id` |
| `terraform state rm` does? | Removes resource from state (NOT from cloud!) |
| `terraform refresh` does? | Syncs state with real-world infrastructure |
| Workspace vs separate directory? | Workspace = same config, separate state; Dir = separate everything |
