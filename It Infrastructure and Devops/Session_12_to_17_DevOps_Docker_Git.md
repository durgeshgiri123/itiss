# Sessions 12–17: DevOps, Docker, Agile & Git/GitHub

---

# SESSIONS 12–15 – DevOps & Docker

---

## 1. DevOps Introduction

**DevOps** = Cultural movement + practices that unify software **Dev**elopment and IT **Op**eration**s** to shorten the development lifecycle.

**Core Goals:**
- Faster delivery of software
- Higher quality and reliability
- Better collaboration between teams
- Continuous improvement

**Key Principles (CALMS):**

| Letter | Principle |
|--------|-----------|
| **C** | Culture (collaboration over silos) |
| **A** | Automation (automate repetitive tasks) |
| **L** | Lean (reduce waste, small batches) |
| **M** | Measurement (metrics-driven decisions) |
| **S** | Sharing (knowledge, tools, responsibility) |

> ⚠️ **Tricky Q:** DevOps is **NOT** a tool or a role — it's a culture and methodology. Having a "DevOps Engineer" doesn't mean you've adopted DevOps.

---

## 2. CI/CD Pipelines

### Continuous Integration (CI):
- Developers merge code to a shared branch frequently (multiple times/day)
- Every merge triggers automated: build → test → report
- Goal: Catch bugs early, reduce integration hell

### Continuous Delivery (CD):
- Code is always in a deployable state
- Deployment to production is **manual** (requires approval)

### Continuous Deployment (CD):
- Every successful build is **automatically deployed** to production
- No human approval needed

> ⚠️ **Tricky Q:** **Continuous Delivery** ≠ **Continuous Deployment**. Delivery = deployable but manual trigger. Deployment = fully automatic push to prod.

**CI/CD Pipeline Stages:**
```
Code Commit → Build → Unit Tests → Integration Tests → Staging Deploy → Acceptance Tests → Prod Deploy
```

---

## 3. Docker Introduction

**Docker** = Platform for developing, shipping, and running applications in **containers**.

**Container vs. Virtual Machine:**

| Feature | Container | Virtual Machine |
|---------|-----------|----------------|
| **Isolation** | Process-level (shares OS kernel) | Full OS isolation |
| **Size** | MBs | GBs |
| **Startup time** | Seconds | Minutes |
| **Resource usage** | Low | High |
| **Portability** | Very high | Moderate |
| **Security isolation** | Lower | Higher |

> ⚠️ **Tricky Q:** Containers share the **host OS kernel** — they do NOT have their own OS. This is why containers are lighter than VMs but offer less isolation.

---

## 4. Docker Core Concepts

| Concept | Description |
|---------|-------------|
| **Image** | Read-only template for creating containers |
| **Container** | Running instance of an image |
| **Dockerfile** | Script to build a custom image |
| **Docker Hub** | Public image registry |
| **Registry** | Stores Docker images (public or private) |
| **Layer** | Each instruction in Dockerfile creates a layer |
| **Volume** | Persistent storage for containers |
| **Network** | Communication between containers |

> ⚠️ **Tricky Q:** Images are **read-only**. When you run a container, Docker adds a thin **writable layer** on top. When the container is deleted, this writable layer is lost (unless you use volumes).

---

## 5. Dockerfile

```dockerfile
# Base image
FROM ubuntu:22.04

# Set metadata
LABEL maintainer="you@example.com"

# Run commands during build
RUN apt-get update && apt-get install -y nginx

# Copy files from host to container
COPY ./html /var/www/html

# Set environment variables
ENV APP_ENV=production

# Expose port
EXPOSE 80

# Command to run when container starts
CMD ["nginx", "-g", "daemon off;"]
```

**Dockerfile Instructions:**

| Instruction | Purpose |
|-------------|---------|
| `FROM` | Base image (must be first) |
| `RUN` | Execute command during BUILD |
| `CMD` | Default command when container STARTS |
| `ENTRYPOINT` | Main process (cannot be overridden easily) |
| `COPY` | Copy files from host to image |
| `ADD` | Like COPY but can extract archives and fetch URLs |
| `ENV` | Set environment variable |
| `EXPOSE` | Document the port (does NOT actually open it) |
| `VOLUME` | Create mount point |
| `WORKDIR` | Set working directory |
| `ARG` | Build-time variable |
| `USER` | Set user for subsequent instructions |

> ⚠️ **Tricky Q:** `EXPOSE` only **documents** the port — it does NOT publish it. To actually make it accessible, use `-p` flag when running: `docker run -p 8080:80`.

> ⚠️ **Tricky Q:** `CMD` vs `ENTRYPOINT`:
- `CMD` = default arguments, easily overridden at runtime
- `ENTRYPOINT` = the main process, harder to override
- Best practice: use `ENTRYPOINT` for the executable, `CMD` for default arguments

---

## 6. Key Docker Commands

```bash
# Image commands
docker build -t myapp:1.0 .         # Build image from Dockerfile
docker images                        # List images
docker pull nginx                    # Pull image from registry
docker push myrepo/myapp:1.0         # Push image to registry
docker rmi nginx                     # Remove image
docker tag myapp:1.0 myrepo/myapp:latest  # Tag an image

# Container commands
docker run -d -p 8080:80 --name web nginx    # Run container (detached, port map, name)
docker run -it ubuntu bash                    # Run interactively
docker ps                                     # List running containers
docker ps -a                                  # List all containers (including stopped)
docker stop web                              # Stop container
docker start web                             # Start stopped container
docker rm web                                # Remove container
docker exec -it web bash                     # Execute command in running container
docker logs web                              # View container logs
docker inspect web                           # Detailed container info

# Volume commands
docker volume create mydata
docker run -v mydata:/app/data myapp
docker run -v /host/path:/container/path myapp   # Bind mount

# Network commands
docker network create mynet
docker run --network mynet myapp
docker network ls
docker network inspect mynet
```

---

## 7. Docker Networking

| Network Type | Description | Use Case |
|-------------|-------------|---------|
| **bridge** | Default — isolated network, containers communicate via IP | Single host, multi-container apps |
| **host** | Container shares host's network stack | Performance-critical apps |
| **none** | No networking | Completely isolated |
| **overlay** | Multi-host network (Docker Swarm) | Distributed applications |
| **macvlan** | Container gets its own MAC/IP on host network | Legacy app integration |

> ⚠️ **Tricky Q:** On the **bridge** network, containers can communicate using **container names** as hostnames (if on a user-defined bridge). On the default bridge network, you must use IP addresses.

---

## 8. Docker Volumes

| Type | Description | Persistent? |
|------|-------------|------------|
| **Volume** | Managed by Docker, stored in /var/lib/docker/volumes | Yes |
| **Bind Mount** | Maps host directory to container | Yes (host dependent) |
| **tmpfs** | Stored in host memory only | No (lost on restart) |

> ⚠️ **Tricky Q:** **Volumes** survive container deletion. **Data written inside a container (not in a volume)** is lost when the container is deleted.

---

## 9. Docker Compose

**Docker Compose** = Tool for defining and running multi-container applications using a YAML file.

**docker-compose.yml example:**
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - db
    networks:
      - appnet

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: myapp
    volumes:
      - dbdata:/var/lib/mysql
    networks:
      - appnet

volumes:
  dbdata:

networks:
  appnet:
```

**Key commands:**
```bash
docker-compose up -d          # Start all services (detached)
docker-compose down           # Stop and remove containers
docker-compose ps             # List services
docker-compose logs           # View logs
docker-compose build          # Build images
docker-compose exec web bash  # Execute command in running service
```

> ⚠️ **Tricky Q:** `depends_on` only waits for the container to **start**, NOT for the application inside to be **ready**. Use health checks for true dependency management.

---

## 10. Tagging and Pushing Images

```bash
# Tag image for Docker Hub
docker tag myapp:1.0 yourusername/myapp:1.0

# Login to Docker Hub
docker login

# Push image
docker push yourusername/myapp:1.0

# Pull and run
docker pull yourusername/myapp:1.0
```

**Image naming format:** `registry/username/repository:tag`
- Default registry: Docker Hub (docker.io)
- Default tag: `latest` (if not specified)

> ⚠️ **Tricky Q:** The `latest` tag is just a convention — it doesn't automatically point to the newest version. You must manually tag with `latest` when pushing.

---

## 11. Agile, Scrum, Kanban

### Agile:
- Iterative, incremental development
- Deliver working software frequently
- Respond to change over following a plan
- Collaboration with customers

**Agile Manifesto:**
```
Individuals and interactions > Processes and tools
Working software > Comprehensive documentation
Customer collaboration > Contract negotiation
Responding to change > Following a plan
```

### Scrum:

| Element | Description |
|---------|-------------|
| **Sprint** | Time-boxed iteration (1-4 weeks) |
| **Product Backlog** | Prioritized list of all requirements |
| **Sprint Backlog** | Items committed to current sprint |
| **Daily Standup** | 15-min sync: what did, will do, blockers |
| **Sprint Review** | Demo to stakeholders |
| **Sprint Retrospective** | Team reflects on process improvements |

**Scrum Roles:**

| Role | Responsibility |
|------|---------------|
| **Product Owner** | Defines and prioritizes backlog |
| **Scrum Master** | Facilitates process, removes blockers |
| **Development Team** | Does the actual work |

> ⚠️ **Tricky Q:** Scrum Master is NOT a project manager — they don't assign tasks. They are a *servant leader* who removes impediments.

### Kanban:

- Visualize workflow on a board (To Do → In Progress → Done)
- Limit **WIP (Work in Progress)** — key difference from Scrum
- No fixed iterations — continuous flow
- Focus on reducing cycle time

> ⚠️ **Tricky Q:** Kanban doesn't have sprints or story points. Scrum has time-boxed sprints. Kanban uses WIP limits to manage flow.

---

## 12. Lean & DevOps

**Lean principles applied to DevOps:**

| Principle | Meaning |
|-----------|---------|
| **Eliminate waste** | Remove non-value-adding activities |
| **Amplify learning** | Fast feedback loops |
| **Decide as late as possible** | Avoid premature decisions |
| **Deliver as fast as possible** | Small batches, frequent releases |
| **Empower the team** | Decentralized decision-making |
| **Build integrity in** | Quality from the start |
| **Optimize the whole** | System thinking, not local optimizations |

---

# SESSIONS 16 & 17 – GIT & GITHUB

---

## 1. Introduction to Git

**Git** = Distributed version control system (VCS).

**Key advantages:**
- Every developer has a full local copy of the repository (distributed)
- Branching and merging is fast and cheap
- Complete history of all changes
- Works offline

> ⚠️ **Tricky Q:** Git is **distributed** — every clone is a full repository. SVN/CVS are **centralized** — only the server has full history.

---

## 2. Core Git Concepts

| Concept | Description |
|---------|-------------|
| **Repository (repo)** | Project directory tracked by Git |
| **Working Directory** | Files you're currently editing |
| **Staging Area (Index)** | Changes prepared for next commit |
| **Commit** | Snapshot of staged changes |
| **Branch** | Independent line of development |
| **HEAD** | Pointer to current commit/branch |
| **Remote** | Copy of repo on another machine (GitHub) |
| **Origin** | Default name for the main remote |

**Git's Three States:**
```
Working Directory → (git add) → Staging Area → (git commit) → Repository
```

> ⚠️ **Tricky Q:** `git add` doesn't save to Git — it only stages changes. `git commit` actually saves the snapshot.

---

## 3. Git Workflow

```bash
# Initialize a new repo
git init

# Check status
git status

# Stage changes
git add filename          # Stage specific file
git add .                 # Stage all changes

# Commit
git commit -m "Add feature X"

# View history
git log
git log --oneline
git log --graph --all

# Branching
git branch                        # List branches
git branch feature-login          # Create branch
git checkout feature-login        # Switch to branch
git checkout -b feature-login     # Create and switch (shortcut)
git switch feature-login          # Modern syntax

# Merging
git checkout main
git merge feature-login           # Merge feature into main

# Remote operations
git remote add origin URL         # Add remote
git push origin main              # Push to remote
git pull origin main              # Pull from remote (fetch + merge)
git fetch origin                  # Download without merging
git clone URL                     # Clone remote repo
```

---

## 4. Important Git Concepts

### Merge vs Rebase:

| | Merge | Rebase |
|--|-------|--------|
| **History** | Preserves all history with merge commit | Linear history (rewrites commits) |
| **When to use** | Public branches | Private/local branches |
| **Safety** | Safe for shared branches | NEVER rebase shared/public branches |

> ⚠️ **Tricky Q:** **Never rebase a public/shared branch** — it rewrites history and breaks others' copies. Rebase is for cleaning up local commits before pushing.

### git pull vs git fetch:
- `git fetch` = download changes but **don't merge** — safe, just updates remote tracking branches
- `git pull` = fetch + **auto-merge** into current branch — can cause conflicts

> ⚠️ **Tricky Q:** `git pull` = `git fetch` + `git merge`. If you want to review changes before merging, use `git fetch` then check `git log origin/main..main`.

### Fast-Forward Merge:
- If the current branch hasn't diverged from the target, Git just moves the pointer
- No merge commit created
- History stays linear

---

## 5. GitHub Collaboration Workflow

**Fork → Clone → Branch → Commit → Push → Pull Request**

| Step | Action |
|------|--------|
| **Fork** | Copy someone's repo to your account |
| **Clone** | Download to your local machine |
| **Branch** | Create feature branch |
| **Commit** | Make changes with commits |
| **Push** | Upload branch to GitHub |
| **Pull Request (PR)** | Request to merge your branch into main |
| **Code Review** | Team reviews the PR |
| **Merge** | PR merged into main |

> ⚠️ **Tricky Q:** A **Pull Request** is a GitHub feature, NOT a Git feature. Git itself has no PRs — they're added by hosting platforms (GitHub, GitLab, Bitbucket).

---

## 6. .gitignore

Tells Git which files to NOT track:
```
# Ignore all log files
*.log

# Ignore node_modules
node_modules/

# Ignore .env files (secrets!)
.env

# Ignore build output
dist/
build/
```

> ⚠️ **Tricky Q:** `.gitignore` only affects untracked files. If a file is already tracked, you must `git rm --cached filename` first before adding to `.gitignore`.

---

## Quick-Fire Exam Q&A

| Question | Answer |
|----------|--------|
| DevOps is a tool/role/culture? | Culture + methodology |
| CI vs CD (Delivery) vs CD (Deployment)? | CI = build+test; Delivery = deployable, manual; Deployment = auto to prod |
| Container vs VM — shares what? | Container shares host OS kernel |
| EXPOSE in Dockerfile does? | Documents port only — doesn't open it |
| CMD vs ENTRYPOINT? | CMD = easily overridden; ENTRYPOINT = main process |
| `docker run -p 8080:80` means? | Host port 8080 maps to container port 80 |
| depends_on in docker-compose? | Container start only, NOT readiness |
| Git is centralized or distributed? | Distributed |
| git add does what? | Stages changes (doesn't commit) |
| `git pull` = ? | git fetch + git merge |
| Rebase on public branch? | NEVER — rewrites history |
| Pull Request is a Git feature? | No — it's a GitHub/hosting platform feature |
| Scrum Master role? | Servant leader, removes blockers (NOT project manager) |
| Kanban vs Scrum key difference? | Kanban has WIP limits and no sprints; Scrum has time-boxed sprints |
| Docker volume vs bind mount? | Volume = Docker-managed; Bind = host path mapped |
| latest tag in Docker? | Just a convention — not automatically the newest |
