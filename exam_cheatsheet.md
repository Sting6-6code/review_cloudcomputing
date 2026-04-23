# CSYE 6225 Cloud Computing — Final Exam Cheatsheet

> English main content · Chinese memory tips [in brackets] · ⭐ exam-likely · ⚠️ trap · 🔄 confusing pair

---

## Table of Contents
1. [Cloud Fundamentals](#1-cloud-fundamentals)
2. [Networking Basics](#2-networking-basics)
3. [SDLC / DevOps / 12-Factor App](#3-sdlc--devops--12-factor-app)
4. [IAM, Pricing & Virtualization](#4-iam-pricing--virtualization)
5. [Git & Version Control](#5-git--version-control)
6. [IaC & Terraform](#6-iac--terraform)
7. [Machine Images & Cloud-Init](#7-machine-images--cloud-init)
8. [Storage & Databases](#8-storage--databases)
9. [DNS](#9-dns)
10. [CDN](#10-cdn)
11. [SRE](#11-sre)
12. [Observability](#12-observability)
13. [Email (SMTP / SPF / DKIM / DMARC)](#13-email)
14. [Load Balancing & Auto Scaling](#14-load-balancing--auto-scaling)
15. [Microservices](#15-microservices)
16. [Event-Driven Architecture](#16-event-driven-architecture)
17. [Serverless](#17-serverless)
18. [CD & Deployment Strategies](#18-cd--deployment-strategies)
19. [Cloud Security](#19-cloud-security)
20. [Architecting for Cloud](#20-architecting-for-cloud)
21. [全局串联 — 10 Critical Facts](#21-全局串联--10-critical-facts)
22. [Predicted Exam Questions](#22-predicted-exam-questions)

---

## 1. Cloud Fundamentals

### NIST 5 Essential Characteristics ⭐
| Characteristic | Key Idea |
|---|---|
| On-demand self-service | Provision without human interaction [自助服务] |
| Broad network access | Access over network with standard mechanisms |
| Resource pooling | Multi-tenant, location independence [多租户] |
| Rapid elasticity | Scale up/down quickly or automatically |
| Measured service | Pay for what you use, transparent metering |

### Service Models ⭐
| Model | Provider manages | Customer manages | Examples |
|---|---|---|---|
| IaaS | Physical, hypervisor, network | OS, app, data, IAM, firewall | EC2, GCE, Azure VM |
| PaaS | Above + OS + runtime | App, data | App Engine, Elastic Beanstalk |
| SaaS | Everything | Just use it | Gmail, Salesforce |

### Deployment Models
| Model | Description |
|---|---|
| Public | Shared infrastructure, owned by provider |
| Private | Dedicated, org-owned or managed |
| Hybrid | Mix of public + private |
| Community | Shared among organizations with common concerns |

⚠️ **Trap**: "Multi-cloud" is NOT one of the 4 NIST deployment models.

### 快速总结
- NIST = 5特性 + 3服务模型 + 4部署模型
- IaaS客户管OS以上全部；SaaS客户只管使用
- 考试最爱考: 给场景判断是哪种服务模型

---

## 2. Networking Basics

### OSI Model (7 Layers) ⭐
```
Layer 7 Application  — HTTP, SMTP, DNS
Layer 6 Presentation — TLS/SSL, encryption
Layer 5 Session      — session management
Layer 4 Transport    — TCP (reliable), UDP (fast)
Layer 3 Network      — IP, routing
Layer 2 Data Link    — MAC, Ethernet
Layer 1 Physical     — cables, bits
```
[记忆口诀: All People Seem To Need Data Processing]

### TCP vs UDP 🔄
| TCP | UDP |
|---|---|
| Connection-oriented (3-way handshake) | Connectionless |
| Reliable, ordered, error-checked | No guarantee |
| Slower | Faster |
| HTTP, SMTP, FTP | DNS, video streaming, gaming |

⚠️ **Trap**: DNS uses UDP by default (port 53), falls back to TCP for large responses.

### VPC Concepts ⭐
| Concept | Description |
|---|---|
| VPC | Virtual Private Cloud — isolated network |
| Subnet | Range of IPs within VPC; Public vs Private |
| Internet Gateway | Allows VPC ↔ Internet |
| NAT Gateway | Private subnet → Internet (outbound only) [单向出口] |
| Security Group | Stateful, instance-level, allow-only rules |
| NACL | Stateless, subnet-level, allow + deny rules |

🔄 **Security Group vs NACL**:
- SG = stateful (return traffic auto allowed)
- NACL = stateless (must add rules for both directions)
- SG = instance level; NACL = subnet level

### 快速总结
- OSI 7层从下到上: 物数网传会表应
- TCP有保障UDP快; DNS用UDP
- SG有状态(stateful)常考; NACL无状态(stateless)需双向规则

---

## 3. SDLC / DevOps / 12-Factor App

### SDLC Models
| Model | Flow | Best For |
|---|---|---|
| Waterfall | Linear sequential | Fixed requirements, regulated industries |
| Agile | Iterative sprints | Changing requirements |
| DevOps | Continuous integration/delivery | Speed + reliability |

### DevOps Three Ways ⭐
1. **Flow** — fast left-to-right delivery (CI/CD pipelines)
2. **Feedback** — amplify feedback loops (monitoring, testing)
3. **Continual Learning** — experimentation, blameless postmortems

### CI/CD ⭐
- **CI** = Continuous Integration: merge frequently, automated build + test
- **CD** = Continuous Delivery/Deployment (see section 18)
- Shift-left testing = catch bugs earlier in SDLC [越早发现越便宜]

### DORA Metrics ⭐
| Metric | Measures |
|---|---|
| Deployment Frequency | How often deploy to production |
| Lead Time for Changes | Commit → production time |
| Change Failure Rate | % deploys causing failure |
| Mean Time to Recover (MTTR) | How fast recover from failure |

### 12-Factor App ⭐
```
I. Codebase         — one codebase, many deploys
II. Dependencies    — explicitly declare & isolate
III. Config         — store in environment (NOT code)
IV. Backing Services — treat as attached resources
V. Build/Release/Run — strictly separate stages
VI. Processes       — stateless, share-nothing
VII. Port Binding   — self-contained, export via port
VIII. Concurrency   — scale out via process model
IX. Disposability   — fast startup, graceful shutdown
X. Dev/Prod Parity  — keep environments as similar as possible
XI. Logs            — treat as event streams
XII. Admin Processes — run as one-off processes
```

⭐ **Most tested**: Factor III (config in env), Factor VI (stateless), Factor XI (logs as streams)

### 快速总结
- DevOps三种方式: Flow流动 + Feedback反馈 + Learning学习
- DORA 4指标衡量DevOps成熟度
- 12-Factor: 配置放环境变量/进程无状态/日志是流

---

## 4. IAM, Pricing & Virtualization

### IAM Comparison ⭐
| Cloud | Mechanism | Format |
|---|---|---|
| AWS | JSON Policy attached to principal | Effect/Action/Resource |
| GCP | Role Binding (who + role + resource) | IAM Policy Binding |
| Azure | RBAC (Role-Based Access Control) | Role Assignment |

### AWS IAM Policy Structure
```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject"],
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

### EC2 Pricing Models ⭐
| Type | Commitment | Discount | Use Case |
|---|---|---|---|
| On-Demand | None | 0% | Unpredictable, short-term |
| Reserved | 1-3 year | up to 72% | Steady, predictable baseline |
| Spot | None | up to 90% | Fault-tolerant, flexible [可被中断] |
| Dedicated Host | By host | Varies | Compliance, licensing |

### GCP Pricing ⭐
| Type | Details |
|---|---|
| Spot VMs | Like AWS Spot, preemptible, cheapest |
| CUDs (Committed Use Discounts) | 1 or 3 year, up to **70%** off [主动承诺] |
| SUDs (Sustained Use Discounts) | **Automatic** ≤30% if VM runs >25% of month [自动给折扣] |

⚠️ **Trap**: SUDs are automatic; CUDs require commitment. GCP CUDs can save up to 70%, not 57%.

### Hypervisors 🔄
| Type | Description | Examples |
|---|---|---|
| Type 1 (Bare-metal) | Runs directly on hardware [直接跑硬件] | VMware ESXi, KVM, Hyper-V, Xen |
| Type 2 (Hosted) | Runs on host OS | VirtualBox, VMware Workstation |

⭐ Cloud providers use **Type 1** hypervisors.

### CPU Vulnerabilities ⚠️
- **Meltdown**: Breaks isolation between user/kernel; patch = KPTI (kernel page-table isolation)
- **Spectre**: Breaks isolation between processes; harder to fix, requires microcode updates
- Both exploit speculative execution

### 快速总结
- EC2: 按需/预留/竞价/专用主机 — 竞价最便宜但会被中断
- GCP SUDs自动给/CUDs需承诺; 最高70%折扣
- Type 1虚拟化直接在硬件上跑=云计算用这个

---

## 5. Git & Version Control

### VCS Types ⭐
| Type | Example | Key Feature |
|---|---|---|
| Local | RCS | Single machine only |
| Centralized (CVCS) | SVN, CVS | Single server, network dependency |
| Distributed (DVCS) | **Git** | Full copy on every client [每人都有完整副本] |

### Git Key Concepts ⭐
- **Snapshot-based**, NOT delta-based ⚠️ [快照不是差异]
- **SHA-1 hash** for integrity (every commit has a hash)
- **3 states**: Working Directory → Staging Area (Index) → Repository

```
Working Directory  →[git add]→  Staging Area  →[git commit]→  Repository
    (modified)                   (staged)                      (committed)
```

### Essential Git Commands
```bash
git init / git clone
git add <file>          # stage changes
git commit -m "msg"     # commit to local repo
git push / git pull
git branch / git checkout -b
git merge / git rebase
git status / git log
```

### 快速总结
- Git是DVCS: 每个人有完整历史
- Git基于快照(snapshot)不是差异(delta) — 常考陷阱
- 3个状态: Working→Staging→Repository

---

## 6. IaC & Terraform

### IaC Principles ⭐
| Principle | Meaning |
|---|---|
| **Idempotency** | Apply same config multiple times = same result [多次执行同样结果] |
| **Declarative** | Describe desired state, not steps |
| Config Drift | Infrastructure drifts from defined state over time ⚠️ |
| Pets vs Cattle | Pets=irreplaceable servers; Cattle=disposable, replaceable [牛vs宠物] |

### Terraform Workflow ⭐
```bash
terraform init      # download providers
terraform plan      # preview changes (dry run)
terraform apply     # create/update resources
terraform destroy   # tear down resources
```

### Terraform Key Concepts
| Concept | Description |
|---|---|
| HCL | HashiCorp Configuration Language (.tf files) |
| Provider | Plugin to interact with cloud API (aws, google, azurerm) |
| State | `terraform.tfstate` — tracks real-world resources |
| Resource | Declared infrastructure object |
| Module | Reusable group of resources |

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

⚠️ **Trap**: `terraform.tfstate` contains sensitive data — never commit to git!

### IaC Tools Comparison
| Tool | Type | Cloud |
|---|---|---|
| Terraform | Declarative, multi-cloud | Any |
| CloudFormation | Declarative | AWS only |
| Pulumi | Imperative (real code) | Any |
| Ansible | Procedural, config mgmt | Any |

### 快速总结
- IaC核心: 幂等性(idempotency) + 声明式(declarative)
- Terraform: init→plan→apply→destroy
- state文件记录真实状态，不要提交到git

---

## 7. Machine Images & Cloud-Init

### Machine Image Types ⭐
| Cloud | Name | Notes |
|---|---|---|
| AWS | AMI (Amazon Machine Image) | EBS-backed or Instance Store |
| GCP | Compute Engine Image | Custom, public, marketplace |
| Azure | Managed Image / Gallery | |

### AMI Types 🔄
| Type | Persistence | Speed | Recommended |
|---|---|---|---|
| EBS-backed | Persistent [持久化] | Slower start | ✅ Yes |
| Instance Store | **Ephemeral** [临时！重启丢数据] | Faster start | Legacy only |

⚠️ **Trap**: Instance Store data is LOST on stop/terminate. EBS data persists.

### AMI Virtualization 🔄
| Type | Description | Status |
|---|---|---|
| HVM (Hardware Virtual Machine) | Full hardware virtualization | ✅ Recommended |
| PV (Paravirtual) | Modified guest kernel | Legacy, slower |

### AMI Lifecycle
```
Create → Register → Copy (cross-region) → Share → Deprecate → Disable → Deregister
```

### Golden Image Layering ⭐
| Layer | Contents |
|---|---|
| L0 | Base OS image |
| L1 | Security hardening, agents |
| L2 | Language runtime (Java, Python) |
| L3 | Application code |

### Packer Workflow ⭐
```bash
packer init    # initialize plugins
packer validate # syntax check
packer build   # create image
```

### Cloud-Init ⭐
- **Purpose**: Automate first-boot configuration of cloud instances
- **Header required**: `#cloud-config` at top of user data
- **Runs ONCE** on first boot (not on subsequent reboots) ⚠️
- **Log file**: `/var/log/cloud-init.log`

### Cloud-Init 5 Stages ⭐
```
1. Generator  — determine if cloud-init should run
2. Local      — find and apply local config (no network)
3. Network    — apply network-dependent config
4. Config     — run config modules
5. Final      — run "final" modules (scripts, packages)
```

### IMDS (Instance Metadata Service) ⭐
| Cloud | URL |
|---|---|
| AWS | `http://169.254.169.254` |
| GCP | `http://metadata.google.internal` |

### 快速总结
- AMI: EBS持久/Instance Store临时(重启丢失!)
- HVM推荐/PV遗留
- Cloud-Init: 首次启动运行/需要#cloud-config头
- Packer: init→validate→build

---

## 8. Storage & Databases

### Storage Types ⭐
| Type | Description | Examples | Use Case |
|---|---|---|---|
| Block | Raw disk, OS manages filesystem | EBS, instance store | Databases, OS volumes |
| File | Hierarchical, shared filesystem | EFS, NFS | Shared home dirs, CMS |
| Object | Flat namespace, key-value+metadata | S3, GCS | Images, backups, static web |
| Relational | Tables + SQL + ACID | RDS, Cloud SQL | Structured transactional data |
| NoSQL | Schema-flexible, horizontally scalable | DynamoDB, Firestore | High-scale, unstructured |

### S3 Storage Classes ⭐ (cost: expensive→cheap)
| Class | Access | Latency | Use Case |
|---|---|---|---|
| Standard | Frequent | Milliseconds | Active data |
| Standard-IA | Infrequent | Milliseconds | Backups [少用但需快速] |
| Glacier Instant Retrieval | Rare | Milliseconds | Archives needing immediate access |
| Glacier Flexible Retrieval | Rare | Minutes-hours | Archives |
| Glacier Deep Archive | Very rare | 12-48 hours | Long-term compliance |
| Intelligent-Tiering | Unknown | Milliseconds | Unpredictable access patterns |

### ACID Properties ⭐
| Property | Meaning |
|---|---|
| Atomicity | All or nothing [要么全做要么不做] |
| Consistency | Data always valid state |
| Isolation | Concurrent transactions don't interfere |
| Durability | Committed data persists even after crash |

### CAP Theorem ⭐
- **Consistency**: all nodes see same data
- **Availability**: every request gets a response
- **Partition Tolerance**: system works despite network splits

⚠️ **Key Rule**: You can only guarantee 2 of 3. In real distributed systems, P is required → choose CA or CP.

### NoSQL Types 🔄
| Type | Key Feature | Examples |
|---|---|---|
| Key-Value | Simple get/set by key | DynamoDB, Redis |
| Document | JSON/BSON documents | MongoDB, Firestore |
| Column-Family | Wide columns, time-series | Cassandra, HBase, Bigtable |
| Graph | Nodes + edges, relationships | Neo4j, Neptune |

### RDS HA Options 🔄
| Option | Purpose | Sync | Writes |
|---|---|---|---|
| Multi-AZ | **Availability** [高可用] | Synchronous | Primary only |
| Read Replicas | **Performance** [读性能] | Asynchronous | Primary only; replica=read |

⚠️ **Trap**: Read Replicas do NOT improve write performance. Multi-AZ is for failover, not performance.

### Polyglot Persistence ⭐
Use different databases for different parts of the app based on data needs. [不同数据用最合适的数据库]

### 快速总结
- 存储: 块(EBS)→文件(EFS)→对象(S3) 各有用途
- S3从Standard到Glacier Deep Archive越来越便宜但越来越慢
- ACID = 事务四要素
- CAP定理: 三选二，分区容错P必须有
- Multi-AZ=高可用; Read Replica=读性能

---

## 9. DNS

### DNS Role ⭐
"Phonebook of the Internet" — translates domain names to IP addresses [把域名翻译成IP地址]

### DNS Hierarchy ⭐
```
Root (.)
  └── TLD (.com, .org, .edu)
       └── Authoritative NS (example.com)
            └── Records (www.example.com → 1.2.3.4)
```
- **13 logical root server clusters** (using Anycast — hundreds of physical servers)
- Root servers managed by ICANN and partner organizations

### DNS 5 Key Properties ⭐
1. **Distributed** — no single point of failure
2. **Scalable** — hierarchy allows infinite growth
3. **Reliable** — redundant servers
4. **Dynamic** — records can be updated
5. **Loosely Coherent** — eventual consistency via TTL

### DNS Record Types ⭐⭐
| Record | Purpose | Key Note |
|---|---|---|
| **A** | IPv4 address | `www → 93.184.216.34` |
| **AAAA** | IPv6 address | |
| **CNAME** | Alias → another domain name | ⚠️ NOT an IP; cannot be on apex/root domain |
| **MX** | Mail server with priority | Lower number = higher priority |
| **NS** | Authoritative name server | Used for zone delegation |
| **TXT** | Text data | SPF, DKIM, domain verification |
| **SOA** | Zone metadata | Serial, refresh, retry, expire |
| **CAA** | Which CAs can issue certs | Security |
| **PTR** | Reverse DNS (IP → name) | Used in email |

⚠️ **CNAME trap**: CNAME cannot point to an IP address. CNAME at apex domain (e.g., example.com) is not allowed per DNS spec.

### DNS Query Types 🔄
| Type | Who does the work |
|---|---|
| Recursive | Resolver does all the work for client [全包办] |
| Iterative | Each server gives next referral, client follows [自己跑腿] |

### DNS 8-Step Lookup ⭐
```
1. Browser cache check
2. OS/local resolver cache check
3. Query to recursive resolver (ISP/8.8.8.8)
4. Resolver → Root server (gives TLD NS)
5. Resolver → TLD server (gives authoritative NS)
6. Resolver → Authoritative NS (gets actual record)
7. Resolver caches + returns to client
8. Browser connects to IP
```

### TTL Strategy
- Short TTL (60s): more queries, fresher data, good for migrations
- Long TTL (86400s): fewer queries, cached longer, cheaper

### Special Concepts ⭐
- **Glue records**: A record for nameserver in same zone to break circular dependency
- **Zone delegation**: NS records delegate subdomain to different nameserver
- **Route 53 ALIAS**: Like CNAME but works at apex domain (AWS-specific, not standard DNS)
- **Private hosted zone**: DNS only within VPC

### 快速总结
- DNS层次: 根→TLD→权威NS
- CNAME不能用IP/不能在根域名 — 必考陷阱
- 8步查询流程: 浏览器→本地→递归解析器→根→TLD→权威
- Route 53 ALIAS可以在根域名用(但不是标准DNS)

---

## 10. CDN

### CDN Basics ⭐
- **CDN** = Content Delivery Network
- **Edge Server / PoP** (Point of Presence): servers near users [靠近用户的服务器]
- **Origin Server**: the real server where content lives
- **Cache Hit**: content served from edge (fast, cheap) ✅
- **Cache Miss**: edge fetches from origin (slower) — response still cached for future

### Anycast vs Unicast 🔄
| | Description |
|---|---|
| **Anycast** | Same IP routes to nearest server [一个IP最近的服务器回应] |
| **Unicast** | One IP → one specific server |

CDNs use Anycast for automatic geographic routing.

### Cache TTL ⭐
- Controlled by `Cache-Control: max-age=<seconds>` header
- **Short TTL**: fresher content, more origin load
- **Long TTL**: more cache hits, risk of stale content
- **Cache invalidation**: force-expire cached content before TTL
- **Cache busting**: append version hash to URL (e.g., `style.abc123.css`) [版本hash避免缓存问题]

### CDN: When to Use / NOT Use ⭐
| Use CDN for | Do NOT use CDN for |
|---|---|
| Static assets (images, JS, CSS) | Personalized content |
| Public downloadable files | Real-time data |
| Video streaming | Internal/private content |
| Geographic distribution | |

### AWS CloudFront ⭐
- 400+ PoPs globally
- **Two-tier**: Edge Locations + Regional Edge Caches [二级缓存]
- **Lambda@Edge**: run code at edge locations
- Integrates with WAF, ACM (SSL certs), S3, ALB

### GCP Cloud CDN ⭐
- Requires **External HTTP(S) Load Balancer** to enable
- Leverages Google's private fiber network
- **Negative caching**: 404 cached for 120s; 302/307 not cached by default
- Cache key = URL by default (can customize)

### 快速总结
- CDN = 边缘服务器缓存内容，减少延迟和Origin负载
- Anycast: 同一IP就近路由
- Cache-Control max-age控制TTL
- 不适合个性化/实时/内部内容

---

## 11. SRE

### SRE Definition ⭐
"What happens when you ask a software engineer to design an operations function" — Ben Treynor (Google)

### SRE Tenets
Availability, Latency, Performance, Efficiency, Change Management, Monitoring, Emergency Response, Capacity Planning

### SLI / SLO / SLA ⭐⭐
| Term | Definition | Example |
|---|---|---|
| **SLI** | Service Level Indicator — quantitative measure | Request success rate = 99.5% |
| **SLO** | Service Level Objective — target range | Success rate ≥ 99.9% |
| **SLA** | Service Level Agreement — contract with consequences | If <99.9%, customer gets refund |

```
SLA → SLO → SLI (SLA is the contract; SLO is internal target; SLI is actual measurement)
```

### Error Budget ⭐
```
Error Budget = 1 - SLO
Example: SLO = 99.9% → Error Budget = 0.1% = 43.8 min/month
```
- When error budget is exhausted → freeze non-critical changes [用完了就停止变更]
- Balances reliability vs feature velocity

### Availability Calculation ⭐
```
Availability = Uptime / (Uptime + Downtime)
            OR = Successful Requests / Total Requests
```

| Availability | Downtime/year |
|---|---|
| 99% ("two nines") | ~87.6 hours |
| 99.9% ("three nines") | ~8.7 hours |
| 99.99% ("four nines") | ~52 minutes |
| 99.999% ("five nines") | ~5 minutes |

### Toil ⭐
**Toil** = work that is:
- Manual
- Repetitive
- Automatable
- Tactical (reactive, not strategic)
- Scales linearly with service growth
- Provides no enduring value

⚠️ SREs should keep toil < 50% of their time. Excess toil = problem.

### Postmortems ⭐
- **Blameless**: no finger-pointing
- **Collaborative**: cross-team participation
- **Must be reviewed**: no unreviewed postmortems
- Focus: what failed in systems/processes, not who failed

### 快速总结
- SLI(测量值)→SLO(目标)→SLA(合同) — 层层包含
- Error Budget = 1 - SLO，用完了停止变更
- Toil = 手动重复可自动化的工作 < 50%
- Postmortem必须无责(blameless)

---

## 12. Observability

### Log Levels ⭐ (ascending severity)
```
ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < OFF
```
- **Production minimum**: INFO
- **DEBUG**: NEVER in production ⚠️ (performance + security risk)
- **Guarded logging**: wrap DEBUG in `if log.isDebugEnabled()` check

### Logging Best Practices ⭐
- Structured format: **JSON** (machine-readable)
- Timestamps in **UTC** (not local time)
- **Never log PII** (passwords, SSNs, credit cards)
- Include: timestamp, level, service name, correlation/trace ID, message

### Metric Types ⭐
| Type | Description | Example |
|---|---|---|
| **Counter** | Monotonically increasing, never decreases | Total requests, errors |
| **Gauge** | Arbitrary value, can go up or down | CPU usage, queue depth |
| **Timer/Histogram** | Measure durations, percentiles | Response times, p99 latency |

### Alerting Severity ⭐
| Severity | Action |
|---|---|
| Low | Record only, review periodically |
| Moderate | Send email notification |
| High | Immediate human intervention, SLA impacted |

### AWS CloudWatch ⭐
- Centralized logging, metrics, and alarms
- Log groups → Log streams
- CloudWatch Logs Insights for querying
- Integration with Lambda, EC2, ECS, etc.

### 快速总结
- 日志级别: ALL<TRACE<DEBUG<INFO<WARN<ERROR<FATAL<OFF
- 生产环境最低INFO级别，DEBUG绝不在生产用
- 指标三类: Counter单调增 / Gauge任意值 / Timer时间
- 永远不要记录PII/密码

---

## 13. Email

### Email Protocols
| Protocol | Port | Purpose |
|---|---|---|
| SMTP | 25 / 587 / 465 | Sending mail (no built-in security, designed 1982) |
| IMAP | 993 | Receiving mail (synced across devices) |
| POP3 | 995 | Receiving mail (download and delete) |

### Email Delivery Lifecycle ⭐
```
App → Email Service (SPF/DKIM signing)
    → Sending SMTP Server (DNS/MX lookup)
    → Recipient Server (SPF/DKIM/DMARC checks)
    → Inbox / Spam / Bounce
```

### SPF / DKIM / DMARC ⭐⭐
| Protocol | Method | Survives Forwarding | Purpose |
|---|---|---|---|
| **SPF** | IP-based authorization in DNS TXT | ❌ Breaks on forward [转发会失效] | Authorize which IPs can send |
| **DKIM** | Asymmetric crypto signature in header | ✅ Survives [能过转发] | Prove email not tampered |
| **DMARC** | Policy layer, requires SPF or DKIM alignment | — | p=none/quarantine/reject |

⚠️ **SPF Limits**: max 10 DNS lookups; breaks on email forwarding.
⚠️ **DMARC requires alignment**: either SPF or DKIM domain must align with From header.

### Bounce Types 🔄
| Type | Meaning | Action |
|---|---|---|
| **Hard Bounce** | Permanent failure (bad address) | Never retry! Remove immediately |
| **Soft Bounce** | Temporary failure (mailbox full) | Retry with backoff |

### Email Reputation Thresholds ⭐
| Metric | Warning | Pause/Block |
|---|---|---|
| Bounce rate (AWS SES) | > 5% | > 10% |
| Complaint rate | > 0.1% | > 0.5% |

### Spam Traps
| Type | Description |
|---|---|
| Pristine | Never valid email, only in trap lists |
| Recycled | Old abandoned address repurposed |
| Typo | Common misspellings (gmal.com) |

### Best Practices ⭐
- Separate **transactional** (receipts) from **marketing** emails — use subdomains
- Never use `noreply@` — kills deliverability signals
- Warm up new IPs gradually
- Implement SPF + DKIM + DMARC (in that order)

### 快速总结
- SPF=IP白名单/转发会断; DKIM=签名/能过转发; DMARC=策略层
- 硬退回(hard bounce)=永久失败，永不重试
- AWS SES: 退回率>5%警告/>10%暂停
- 不要用noreply@

---

## 14. Load Balancing & Auto Scaling

### AWS Regions & AZs ⭐
- **Region**: Independent geographic area (us-east-1, eu-west-1)
- **AZ** (Availability Zone): Physically separate data centers within a region
- Each region has 2-6 AZs, connected by low-latency private network
- ⚠️ **AZ IDs are randomized per account** (us-east-1a ≠ same physical AZ for everyone)

### Load Balancer Layers 🔄
| Layer | What it sees | Routing based on |
|---|---|---|
| **Layer 4** | IP + TCP/UDP port only | Source IP, port |
| **Layer 7** | Full HTTP: URL, headers, cookies, body | Path, host, headers |

### AWS Load Balancers ⭐
| LB | Layer | Scope | Key Features |
|---|---|---|---|
| **ALB** | 7 | Regional | Path/host routing, WAF, Cognito auth, WebSocket |
| **NLB** | 4 | Regional | Static IP per AZ, millions req/s, source IP preserved |
| **GWLB** | 3 | Regional | Network appliances (firewalls, IDS) |
| **CLB** | 4+7 | Regional | Legacy, avoid |

⚠️ **Cross-zone load balancing**: ALB = enabled by default; NLB = disabled by default.

### GCP Load Balancers ⭐
| LB | Layer | Scope |
|---|---|---|
| HTTP(S) LB | 7 | **GLOBAL** (anycast) |
| Network LB | 4 | Regional |

GCP HTTP(S) LB uses: Forwarding Rule → Target Proxy → URL Map → Backend Service → MIG

### ALB Components ⭐
```
Listener → Rules → Target Groups → Targets (EC2, Lambda, containers)
```

### Health Checks ⭐
- Dedicated endpoint: `/healthz` or `/health`
- Should check real dependencies (DB connection, etc.)
- Separate from app traffic
- LB removes unhealthy targets from rotation

### SSL Termination ⭐
- LB handles TLS; backends receive plain HTTP [LB负责加密解密]
- Reduces CPU load on app servers
- Certificates managed at LB level (ACM on AWS)

### Sticky Sessions ⭐
- Bind client to specific backend (cookie-based or IP hash)
- ⚠️ Breaks stateless principle — avoid if possible [打破无状态原则]

### Connection Draining ⭐
- Default: **300 seconds** (5 minutes)
- In-flight requests complete before instance removed from rotation

### Auto Scaling ⭐
| AWS | GCP | Azure |
|---|---|---|
| ASG (Auto Scaling Group) | MIG (Managed Instance Group) | VMSS (VM Scale Set) |
| Launch Template | Instance Template | |

### Scaling Policy Types ⭐
| Type | Description |
|---|---|
| Manual | Human changes desired capacity |
| Scheduled | Scale at specific times (Black Friday prep) |
| Target Tracking | Keep metric at target (CPU = 70%) [最常用] |
| Step Scaling | Different amounts based on alarm thresholds |
| Simple Scaling | Fixed change on single alarm |
| Predictive | ML-based, scale before demand hits |

### Scaling Timers ⭐
- **Cooldown**: Default **300s** — wait before next scale action
- **Warm-up**: Time for new instance to be ready before receiving traffic [预热时间]
- ⚠️ Warm-up prevents premature scaling decisions

### 快速总结
- ALB=Layer7路径路由/NLB=Layer4静态IP
- ALB跨区默认开/NLB跨区默认关
- ASG: Launch Template→ASG→Scaling Policy
- 冷却时间(cooldown)=300s默认; 预热(warm-up)=新实例准备好才算流量

---

## 15. Microservices

### Monolith vs Microservices ⭐
| | Monolith | Microservices |
|---|---|---|
| Deployment | Single artifact | Independent per service |
| Database | Shared | **Database per service** |
| Scaling | Scale all or nothing | Scale individual services |
| Failure | One failure can crash all | Fault isolation |
| Complexity | Simple to develop/test | Distributed system complexity |
| Best for | Small teams, MVPs, simple domains | Large teams, complex domains |

### Microservice Principles ⭐
- **Loose coupling**: changes in one service don't cascade to others [改一个不影响其他]
- **High cohesion**: related logic together in one service
- **Database per service**: NEVER share a database (anti-pattern) ⚠️

### Communication Patterns 🔄
| Type | Examples | Pros | Cons |
|---|---|---|---|
| Synchronous | REST, gRPC | Simple, immediate response | Temporal coupling, cascading failures |
| Asynchronous | Queues, events | Temporal decoupling, resilience | Eventual consistency, complexity |

### Saga Pattern ⭐ (for distributed transactions)
| Type | How | |
|---|---|---|
| Choreography | Services emit events, others react | Decentralized, no coordinator |
| Orchestration | Central coordinator calls services | More control, single point of failure |
- **Compensating transactions**: undo completed steps on failure [补偿事务=分布式回滚]

### API Gateway ⭐
- Single entry point for all client requests
- Handles: routing, auth, rate limiting, SSL termination, logging
- Hides internal service topology from clients

### When NOT to Use Microservices ⚠️
- Team < 5-10 developers
- Domain not well understood (uncertain requirements)
- MVP / early-stage product
- Simple application

> "Distributed Monolith" = microservices deployed together with shared DB = worst of both worlds ⚠️

### Migration: Strangler Fig Pattern ⭐
- Gradually replace monolith functionality with microservices
- Old and new run in parallel until monolith is fully replaced
- Named after strangler fig trees that grow around host

### 快速总结
- 微服务核心: 松耦合+高内聚+每服务独立数据库
- 同步=REST/gRPC时间耦合; 异步=队列事件时间解耦
- Saga模式处理分布式事务(编排 vs 协调)
- 分布式单体=最糟糕的结果
- 绞杀者模式渐进式迁移

---

## 16. Event-Driven Architecture

### Why EDA? ⭐
Synchronous problems solved by EDA:
- Temporal coupling (caller must be available)
- Cascading failures
- Scalability bottlenecks
- Tight dependency between services

### EDA Core Concepts ⭐
- **Event** = fact that something happened (NOT a command) [事件=发生了什么，不是命令]
- **Producer** = publishes events
- **Consumer** = subscribes to and processes events
- **Channel/Topic** = where events flow
- **Broker** = intermediary (SQS, SNS, Kafka)

### Message Queue vs Pub/Sub 🔄
| | Message Queue | Pub/Sub |
|---|---|---|
| Model | Point-to-point | One-to-many |
| Consumers | **One** consumer gets message | **All** subscribers get copy |
| AWS | SQS | SNS |
| Retention | Yes | No (push immediately) |

### AWS SQS ⭐
| Feature | Detail |
|---|---|
| Type | Pull-based (consumers poll) |
| Standard | At-least-once delivery, best-effort ordering |
| FIFO | Exactly-once, strict ordering, max **300 msg/s** |
| Visibility Timeout | Default **30s** — message hidden during processing |
| DLQ | Dead Letter Queue — catches messages that fail repeatedly |
| Long Polling | WaitTimeSeconds > 0 — reduces empty responses [减少空轮询] |

### AWS SNS ⭐
| Feature | Detail |
|---|---|
| Type | Push-based |
| Retention | No persistence |
| Types | Standard (unordered) and FIFO |
| Subscriptions | SQS, Lambda, HTTP, email, SMS |
| Filters | Subscription filter policies [过滤只接收感兴趣的消息] |

### Fan-Out Pattern ⭐
```
SNS Topic → SQS Queue A (service A)
          → SQS Queue B (service B)
          → Lambda C
```
[SNS广播到多个SQS队列=fan-out]

### Delivery Guarantees 🔄
| Guarantee | Description | Risk |
|---|---|---|
| At-most-once | May lose messages | Loss |
| At-least-once | May deliver duplicates [默认] | Duplicates |
| Exactly-once | Delivered exactly once | Complex/expensive |

### Idempotency ⭐
Key strategies when using at-least-once delivery:
- **Idempotency keys**: store processed message IDs
- **Conditional writes**: check before inserting
- **Natural idempotency**: operations that are safe to repeat (e.g., set value, don't increment)

### Backoff & Retry ⭐
```
Exponential Backoff + Jitter = prevents thundering herd
Wait = min(cap, base * 2^attempt) + random_jitter
```
⚠️ **Thundering herd**: all retries hit at same time = overwhelm service

### DLQ Monitoring ⭐
- Alert when DLQ has **any** messages (> 0) [DLQ有消息就报警]
- Indicates repeated processing failures

### GCP Pub/Sub
- Equivalent to SNS + SQS combined
- Pull or push delivery modes

### EventBridge ⭐
- **Content-based routing**: route events based on event content rules
- Event bus can receive from AWS services, SaaS apps, custom apps

### 快速总结
- SQS=拉取/队列(一个消费者); SNS=推送/广播(多个订阅者)
- SQS FIFO=精确一次/顺序/300条/s上限
- Visibility timeout默认30s
- DLQ>0条消息就应该报警
- 幂等性=重复处理也没问题

---

## 17. Serverless

### FaaS Characteristics ⭐
| Property | Detail |
|---|---|
| No server management | Provider handles all infrastructure |
| Event-driven | Triggered by events (HTTP, S3, SQS, etc.) |
| Auto-scaling | Scales to zero when idle [空闲时缩到零] |
| Pay-per-use | Billed per invocation + duration (ms) |
| Ephemeral | No persistent local storage [无本地持久存储] |
| Stateless | No state between invocations |

### Cold Start ⭐
```
Cold Start = Download code + Start runtime + Initialize code
           → adds latency (especially bad for Java/Spring)
```
| Language | Cold Start Speed |
|---|---|
| Go, Rust | Fastest |
| Node.js, Python | Medium |
| Java, C# (.NET) | Slowest [Java冷启动最慢] |

**Mitigation**: Provisioned Concurrency (pre-warm Lambda), smaller packages, avoid heavy frameworks

### AWS Lambda Limits ⭐
| Resource | Limit |
|---|---|
| Memory | 128 MB – 10,240 MB |
| Timeout | max **15 minutes** |
| Concurrency | 1,000 per account (default) |
| ZIP deployment | 50 MB compressed / 250 MB uncompressed |
| Container image | 10 GB |
| Layers | max 5 layers |

### Lambda Pricing ⭐
- **Free tier**: 1 million requests/month free
- Beyond free: $0.20 per million requests
- Duration: billed per **1 ms** [按毫秒计费]

### Lambda Patterns ⭐
```
REST API:    API Gateway → Lambda → DynamoDB
Event pipe: S3 → Lambda → SQS → Lambda → DynamoDB
Scheduled:  EventBridge Rule → Lambda (cron jobs)
```

### Lambda Best Practices ⭐
- Initialize clients (DB, SDK) in **global scope** (outside handler) [在全局作用域初始化连接]
- Use environment variables (not hardcoded config)
- Design stateless
- Minimize package size (reduce cold start)
- Use long polling for SQS triggers
- Least-privilege IAM per function

### When to Use / Not Use Serverless ⭐
| Good Fit | Poor Fit |
|---|---|
| Event-driven workloads | Long-running > 15 min |
| Sporadic / unpredictable traffic | WebSocket connections |
| Short duration tasks | GPU workloads |
| Spiky traffic | Steady, high-throughput workloads |
| | Cold-start sensitive apps |

### 快速总结
- Lambda最长15分钟/内存128MB-10GB
- 冷启动: Go最快/Java最慢; 用Provisioned Concurrency预热
- 全局作用域初始化DB连接(不在handler里)
- 按毫秒计费/每月100万次免费
- 不适合: 长时间运行/WebSocket/GPU/高并发稳定负载

---

## 18. CD & Deployment Strategies

### Continuous Delivery vs Continuous Deployment 🔄 ⭐
| | Continuous Delivery | Continuous Deployment |
|---|---|---|
| Every commit | CAN be deployed | IS deployed automatically |
| Human gate | ✅ Yes (approval step) | ❌ No |
| Risk | Lower | Higher |

⚠️ **Key principle**: The artifact tested IS the artifact deployed. Never rebuild between staging and production.

### CD Pipeline ⭐
```
Code → Build → Test → Artifact → Staging → Acceptance Tests
     → [Approval Gate] → Production → Post-Deploy Validation
     → [Rollback if needed]
```

### Deployment Strategies ⭐⭐
| Strategy | How | Rollback | Cost | Risk |
|---|---|---|---|---|
| **Rolling** | Replace instances one-at-a-time behind LB | Slow | Low | Both versions briefly coexist |
| **Blue-Green** | Two full environments, switch DNS/LB | Fast (flip back) | **2x cost** | Clean switch |
| **Canary** | Send 5% traffic to new version, monitor, then graduate | Fast (redirect traffic) | Medium | Most sophisticated |
| **Immutable** | Always deploy fresh instances, never patch | Very fast | Higher | Best for cattle model |

### Blue-Green Details ⭐
```
Blue (current production) ←→ Load Balancer ←→ Users
Green (new version) — deployed but not receiving traffic
→ Switch LB → Green becomes production
→ Blue kept for fast rollback
```

### Canary Deployment ⭐
```
95% → Stable (Blue)
 5% → Canary (Green)
Monitor error rates, latency, business metrics
→ Gradually increase to 100% or rollback
```

### Feature Flags ⭐
- **Decouple deployment from release** [部署和发布解耦]
- Deploy dark (code in production but feature off)
- Enable for % of users, specific users, or conditions
- Allows instant disable without redeployment

### Immutable Infrastructure ⭐
- Never patch or update running servers
- Always replace with new instance from updated image
- Ensures consistency, eliminates configuration drift

### Database Migrations ⭐
- Hardest part of CD [数据库迁移最难]
- Must be backward compatible during rolling deploys
- Blue-green: run migration against both if needed
- Avoid breaking schema changes

### 快速总结
- Continuous Delivery=人工审批; Deployment=全自动
- 四种策略: Rolling(滚动)/Blue-Green(蓝绿)/Canary(金丝雀)/Immutable(不可变)
- Blue-Green=费用翻倍但回滚最快
- Canary=最精细但最复杂
- Feature flags=部署和发布解耦

---

## 19. Cloud Security

### CIA Triad ⭐
| Property | Description | Controls |
|---|---|---|
| **Confidentiality** | Only authorized access | Encryption, access controls |
| **Integrity** | Data not tampered with | Hashing, digital signatures |
| **Availability** | System accessible when needed | DDoS protection, redundancy |
| (+**Authenticity**) | Prove identity | Digital signatures, certificates |

### Risk Formula
```
Risk = Threat × Vulnerability × Impact
```

### Shared Responsibility Model ⭐⭐
| Service | Provider Responsible for | Customer Responsible for |
|---|---|---|
| IaaS | Physical, network, hypervisor | OS, app, data, IAM, network config, firewall, encryption |
| PaaS | Above + OS + runtime + platform | App, data, IAM |
| SaaS | Everything | Data + access controls only |

> Provider = "Security **OF** the cloud" | Customer = "Security **IN** the cloud" ⭐

### Encryption ⭐
| Type | Key | Speed | Algorithm | Use |
|---|---|---|---|---|
| Symmetric | Same key encrypt+decrypt | Fast | AES-128, AES-256 | Data at rest, bulk |
| Asymmetric | Public/private key pair | 100-1000x slower | RSA, ECDSA | TLS handshake, signing |

### TLS Handshake (Simplified) ⭐
```
1. Client → CLIENT_HELLO (cipher suites)
2. Server → SERVER_HELLO + Certificate
3. Client verifies cert via trusted CA
4. Client → PRE_MASTER SECRET (encrypted with server's public key)
5. Both derive symmetric session key from pre-master
6. Encrypted data exchange begins
```
[非对称加密只在握手时用，之后用对称加密速度快]

### Hashing ⭐
- One-way: cannot reverse
- Deterministic: same input = same output
- Avalanche effect: tiny change = completely different hash
- **SHA-256** for general use; **bcrypt/Argon2** for passwords
- **Never store plain text passwords** ⚠️

### KMS (Key Management Service) ⭐
| Feature | AWS KMS | GCP Cloud KMS | Azure Key Vault |
|---|---|---|---|
| CMK | ✅ | ✅ | ✅ |
| BYOK | ✅ | ✅ | ✅ |
| Auto rotation | ✅ | ✅ | ✅ |

⭐ **Encryption defaults**:
- GCP: All data encrypted at rest by default
- Azure: All data encrypted at rest by default
- AWS: Most services encrypt by default; **RDS must enable at creation** ⚠️

### Security Groups vs NACLs 🔄 ⭐
| | Security Group | NACL |
|---|---|---|
| Level | Instance | Subnet |
| State | **Stateful** [有状态] | **Stateless** [无状态] |
| Rules | Allow only | Allow + Deny |
| Order | All rules evaluated | Rules evaluated in order |
| Default | Deny all inbound | Allow all |

### WAF (Web Application Firewall) ⭐
- Protects against: SQL injection, XSS, CSRF
- AWS WAF attaches to: ALB, CloudFront, API Gateway
- GCP Cloud Armor attaches to: HTTP(S) Load Balancer

### SQL Injection Defense ⭐
```python
# WRONG ⚠️
query = f"SELECT * FROM users WHERE name = '{user_input}'"

# CORRECT ✅ — Parameterized queries
cursor.execute("SELECT * FROM users WHERE name = ?", (user_input,))
```

### OWASP Top 10 (Key ones) ⭐
| Rank | Vulnerability |
|---|---|
| #1 | Broken Access Control |
| #2 | Cryptographic Failures |
| #3 | Injection (SQL, NoSQL, command) |
| #7 | Identification/Authentication Failures |
| #10 | Server-Side Request Forgery (SSRF) |

### AWS Security Services ⭐
| Service | Purpose |
|---|---|
| **CloudTrail** | API audit log — who did what, when (90-day default) |
| **GuardDuty** | ML-based threat detection, anomaly detection |
| **IAM** | Identity and access management |
| **Secrets Manager** | Store and rotate secrets |
| **KMS** | Key management |

### 快速总结
- CIA三元组: 机密性+完整性+可用性
- 共同责任: Provider管云的安全/Customer管云中的安全
- 对称=快(AES)/非对称=慢但安全(RSA/ECDSA); TLS握手用非对称换对称密钥
- SG有状态(stateful)/NACL无状态(stateless) — 必考
- SQL注入防御=参数化查询
- AWS RDS默认不加密，创建时必须开启 ⚠️

---

## 20. Architecting for Cloud

### Migration Approaches 🔄
| Approach | Description |
|---|---|
| Lift & Shift | Move as-is, no code changes |
| Re-platform | Minor optimizations for cloud |
| Cloud-native | Redesign for elasticity and managed services |

### Scaling Strategies 🔄 ⭐
| Type | Description | Ceiling |
|---|---|---|
| Vertical | Bigger instance (more CPU/RAM) | Hardware limit ⚠️ |
| Horizontal | More instances | Practically unlimited |

⭐ Cloud design principle: **prefer horizontal scaling**.

### Stateless vs Stateful ⭐
| | Stateless | Stateful |
|---|---|---|
| State location | External (DB, cache, S3) | In-process |
| Scaling | Any instance serves any request ✅ | Requires session affinity ⚠️ |
| Resilience | Any instance can replace failed one | Sticky sessions or replication |

**Design for stateless**: store session in Redis, ElastiCache, or DynamoDB.

### Instance Bootstrapping Strategies ⭐
| Strategy | How | Startup Time |
|---|---|---|
| Bootstrapping | User data/cloud-init runs scripts at launch | Slow (install at boot) |
| Golden Image | Pre-baked image with everything installed | Fast (already installed) |
| Hybrid | Golden image + cloud-init for dynamic config | Balanced [最常用] |

### Loose Coupling ⭐
- Use **ELB hostname** or **Route 53 private zones** for service discovery (not IPs)
- Use **async integration** (SQS/SNS) between services
- Avoid hardcoded IP addresses

### Services Not Servers ⭐
Use managed services instead of self-managed:
- S3 instead of self-managed NFS
- RDS instead of EC2 + MySQL
- SQS instead of self-managed RabbitMQ
- Reduces operational overhead

### Database Scaling ⭐
| Approach | How |
|---|---|
| Vertical scaling | Bigger DB instance (limited ceiling) |
| Read replicas | Add read replicas (async replication, replication lag) |
| Multi-AZ | Synchronous standby, automatic failover (not for scaling) |
| NoSQL | Horizontal scaling by design |

### Redundancy Types 🔄
| Type | Description | Use For |
|---|---|---|
| Standby redundancy | Stateful, failover time required | Databases, stateful services |
| Active redundancy | Stateless, absorbs load instantly | Web servers, app tier |

### Caching ⭐
- Store frequently accessed DB query results in memory (ElastiCache/Memorystore)
- Reduces DB load, improves latency
- Invalidation strategy is the hard part

### CloudFormation ⭐
- AWS-native IaC (JSON or YAML)
- Stack = deployed set of resources
- Change set = preview of changes before applying

### 快速总结
- 优先水平扩展(horizontal)而非垂直扩展
- 无状态设计=任何实例都能响应任何请求
- 松耦合: 用ELB hostname而非IP/用异步集成
- 用托管服务而非自建(Services not Servers)
- 缓存减少数据库压力

---

## 21. 全局串联 — 10 Critical Facts

> [这10条是整门课最核心、最容易考的知识点]

1. **共同责任模型**: Provider = "云的安全"(物理/虚拟化/硬件); Customer = "云中的安全"(OS/应用/IAM/加密/网络). IaaS客户负责最多，SaaS客户负责最少。

2. **SLI→SLO→SLA三层关系**: SLI是测量值，SLO是内部目标，SLA是对外合同。Error Budget = 1 - SLO。用完Error Budget就应该停止非关键变更。

3. **SG vs NACL**: Security Group=有状态(stateful)+实例级别+仅允许规则; NACL=无状态(stateless)+子网级别+允许和拒绝规则。这是网络安全必考对比。

4. **CNAME不能在根域名使用**: CNAME只能指向域名(不能是IP); 不能用在apex/裸域名(example.com)。AWS Route 53 ALIAS是非标准扩展，可以在根域名使用。

5. **CAP定理**: 分布式系统中一致性(C)、可用性(A)、分区容错性(P)三选二。实际系统P必须有，所以要在CA之间权衡。CP牺牲可用性(如强一致性数据库)，AP牺牲一致性(如最终一致性系统)。

6. **SPF vs DKIM**: SPF基于IP白名单，转发时会断; DKIM用非对称签名，能过转发。DMARC要求两者至少一个对齐。邮件认证三件套缺一不可。

7. **持续交付vs持续部署**: Continuous Delivery=每次提交可以部署(但有人工审批门); Continuous Deployment=每次提交自动部署到生产。部署策略: Rolling最低成本/Blue-Green最快回滚但2倍费用/Canary最精细。

8. **Lambda限制**: 最长15分钟/最大内存10GB/并发1000(默认)/按毫秒计费。冷启动Java最慢/Go最快。全局作用域初始化连接(不在handler里)。

9. **微服务反模式**: 共享数据库=ANTI-PATTERN。"分布式单体"(服务分开但共享DB且一起部署)是最糟糕的结果。正确做法: 每个服务有独立数据库。

10. **12-Factor中最常考**: Factor III(配置放环境变量不放代码)/Factor VI(进程无状态)/Factor XI(日志作为流). Cloud-Init首次启动运行/需要#cloud-config头/日志在/var/log/cloud-init.log。

---

## 22. Predicted Exam Questions

> All questions follow exactly one of three formats: **Multiple Select** | **True/False** | **Scenario**

---

### Multiple Select Questions

**Q1.** Which of the following are NIST essential characteristics of cloud computing? *(Select all that apply)*

- A. On-demand self-service
- B. Vendor lock-in prevention
- C. Resource pooling
- D. Rapid elasticity
- E. Measured service

**Answer: A, C, D, E**
Vendor lock-in prevention is not a NIST characteristic. The 5 are: on-demand self-service, broad network access, resource pooling, rapid elasticity, measured service.

---

**Q2.** Which of the following correctly describe the difference between AWS Security Groups and NACLs? *(Select all that apply)*

- A. Security Groups are stateful; NACLs are stateless
- B. Security Groups operate at the subnet level
- C. NACLs can have both allow and deny rules
- D. Security Groups evaluate all rules before deciding
- E. NACLs operate at the instance level

**Answer: A, C, D**
Security Groups operate at the instance level (not subnet), and NACLs operate at the subnet level (not instance). Both B and E are reversed.

---

**Q3.** Which of the following are valid AWS EC2 purchasing options? *(Select all that apply)*

- A. On-Demand
- B. Reserved Instances
- C. Spot Instances
- D. Burst Instances
- E. Dedicated Hosts

**Answer: A, B, C, E**
"Burst Instances" is not a purchasing option. T-series instances can burst CPU performance, but that's not a purchasing model.

---

**Q4.** Which of the following correctly describe DKIM email authentication? *(Select all that apply)*

- A. Uses asymmetric cryptography (public/private key)
- B. Verifies the IP address of the sending mail server
- C. Survives email forwarding
- D. Requires a TXT record in DNS
- E. Is an IP-based authorization mechanism

**Answer: A, C, D**
B and E describe SPF, not DKIM. DKIM uses cryptographic signatures, not IP-based checks.

---

**Q5.** Which of the following are characteristics of serverless (FaaS) functions? *(Select all that apply)*

- A. Auto-scaling to zero when idle
- B. Persistent local file storage between invocations
- C. Event-driven invocation
- D. Pay-per-use billing based on invocations and duration
- E. No server infrastructure to manage

**Answer: A, C, D, E**
FaaS is ephemeral — there is NO persistent local storage between invocations. /tmp is available but not persistent.

---

**Q6.** Which of the following deployment strategies require maintaining two full production environments simultaneously? *(Select all that apply)*

- A. Rolling deployment
- B. Blue-Green deployment
- C. Canary deployment
- D. Immutable infrastructure deployment
- E. Feature flag deployment

**Answer: B**
Only Blue-Green requires two full environments (hence the 2x cost). Rolling replaces instances gradually; Canary splits traffic but doesn't require a full second environment.

---

**Q7.** Which of the following are valid NoSQL database types? *(Select all that apply)*

- A. Key-Value
- B. Document
- C. Relational
- D. Column-Family
- E. Graph

**Answer: A, B, D, E**
Relational (RDBMS) is NOT a NoSQL type. The four NoSQL types are: Key-Value, Document, Column-Family, and Graph.

---

**Q8.** According to the 12-Factor App methodology, which of the following are correct practices? *(Select all that apply)*

- A. Store configuration in environment variables
- B. Store secrets directly in the application source code
- C. Treat logs as event streams
- D. Keep stateless processes that share nothing
- E. Use different codebases for different deployment environments

**Answer: A, C, D**
Factor III says config goes in environment variables (NOT source code). Factor XI says logs are streams. Factor VI says stateless processes. Factor I says one codebase with multiple deploys.

---

### True/False Questions

**Q9.** True or False: In AWS, a CNAME record can be used at the apex (root) of a domain (e.g., example.com).

**Answer: FALSE**
Per DNS specification, CNAME records cannot be placed at the apex/root domain because a CNAME cannot coexist with other records (like SOA and NS records that must exist at the root). AWS Route 53 provides a proprietary "ALIAS" record type that behaves similarly to CNAME but works at the apex domain — however, this is AWS-specific and not standard DNS.

---

**Q10.** True or False: AWS Multi-AZ RDS deployments improve read performance by distributing read queries across the standby replica.

**Answer: FALSE**
Multi-AZ is for **high availability and failover**, not performance. The standby replica is synchronous but does NOT serve read traffic — it is purely on standby for automatic failover. To improve read performance, you use **Read Replicas** (which are asynchronous).

---

**Q11.** True or False: GCP Sustained Use Discounts (SUDs) require customers to make an upfront commitment to receive the discount.

**Answer: FALSE**
SUDs are applied **automatically** with no commitment required. If a VM runs for more than 25% of a billing month, GCP automatically applies up to 30% discount. In contrast, Committed Use Discounts (CUDs) require an explicit 1- or 3-year commitment but offer deeper discounts (up to 70%).

---

**Q12.** True or False: In the CAP theorem, it is possible for a distributed system to simultaneously guarantee Consistency, Availability, and Partition Tolerance.

**Answer: FALSE**
The CAP theorem states that a distributed system can only guarantee two of the three properties simultaneously. Since network partitions (P) are unavoidable in distributed systems, real systems must choose between CP (sacrifice availability) or AP (sacrifice consistency).

---

**Q13.** True or False: SPF email authentication survives email forwarding.

**Answer: FALSE**
SPF checks the IP address of the sending server against the domain's SPF record. When email is forwarded, the forwarding server sends from its own IP, which is not listed in the original sender's SPF record, causing SPF to fail. DKIM (which uses cryptographic signatures) survives forwarding.

---

**Q14.** True or False: Git stores file changes as deltas (diffs) from the previous version.

**Answer: FALSE**
Git is **snapshot-based**, not delta-based. Every commit stores a snapshot of all files at that point in time. If a file hasn't changed, Git stores a link to the previously stored identical file (for efficiency), but conceptually Git thinks in snapshots. This is a key distinction from centralized VCS like SVN.

---

**Q15.** True or False: AWS Lambda functions support execution durations of up to 60 minutes.

**Answer: FALSE**
AWS Lambda has a maximum execution timeout of **15 minutes**. If your workload requires longer execution, you should consider alternatives like AWS Step Functions, ECS/Fargate tasks, or EC2 instances.

---

**Q16.** True or False: In a microservices architecture, services should share a common database to simplify data access and reduce infrastructure costs.

**Answer: FALSE**
Sharing a database between microservices is an **anti-pattern** that violates the principle of loose coupling. When services share a database, schema changes in one service can break others, creating tight coupling. Each microservice should own its own database (database-per-service principle), accepting eventual consistency as a trade-off.

---

**Q17.** True or False: Cloud-Init runs every time a cloud instance is rebooted.

**Answer: FALSE**
Cloud-Init runs **only once** — during the first boot of a cloud instance. On subsequent reboots, Cloud-Init detects that initialization has already occurred and skips execution. To re-run Cloud-Init, you would need to clear the Cloud-Init cache manually.

---

### Scenario Questions

**Q18.** Sarah is the CTO of a fintech startup that processes credit card transactions. Her application currently runs as a monolith on a single EC2 instance. During peak hours (9 AM - 5 PM), CPU usage hits 95% causing timeouts, but at night usage drops to 15%. She wants to ensure 99.99% availability and handle traffic spikes automatically. Which combination of AWS services and architecture decisions should Sarah implement?

- A. Upgrade to a larger EC2 instance (vertical scaling) and add a secondary instance for failover
- B. Deploy behind an Application Load Balancer, use Auto Scaling Groups with target tracking policy (CPU at 70%), deploy across multiple AZs, and implement health checks at `/healthz`
- C. Use Amazon SQS to queue all transaction requests and process them asynchronously
- D. Migrate to AWS Lambda to eliminate server management

**Answer: B**
Option B correctly addresses all requirements: ALB distributes traffic, ASG with target tracking automatically scales in/out based on CPU utilization, multi-AZ provides high availability (reduces single points of failure), and proper health checks ensure traffic only goes to healthy instances. Option A (vertical scaling) has a ceiling and doesn't auto-scale. Option C doesn't address the latency requirements for payment processing. Option D is problematic for financial transactions due to Lambda's 15-minute timeout and cold start issues.

---

**Q19.** An e-commerce company experiences 10x traffic spikes during Black Friday. Their marketing team wants to send promotional emails to 5 million customers, their inventory service needs to update stock in real-time as orders come in, and their recommendation engine uses heavy ML models for personalized product suggestions. The team is considering a CDN for all three workloads. For which of these workloads is a CDN appropriate?

- A. Marketing emails
- B. Inventory updates
- C. Personalized product recommendations
- D. None of the above
- E. All of the above

**Answer: D — None of the above**
CDNs are appropriate for static, publicly cacheable content. Marketing emails are not web content at all. Inventory updates are real-time transactional data that cannot be cached (staleness would cause overselling). Personalized recommendations are unique per user and cannot be cached (the cache key would be unique for every user, resulting in 0% cache hit rate). CDNs excel at static assets like product images, CSS, JS files, and downloadable content.

---

**Q20.** Marcus is building a payment processing microservice that handles credit card charges. His team uses AWS SQS to receive payment requests. Occasionally, due to a downstream bank API outage, messages fail to process and are retried multiple times. Marcus wants to ensure that: (1) failed messages don't get lost, (2) successful payments are never charged twice, and (3) the team is notified immediately when persistent failures occur. What should Marcus implement?

**Answer:**
Marcus should implement three complementary solutions:
1. **Dead Letter Queue (DLQ)**: Configure a DLQ on the SQS queue with a `maxReceiveCount` (e.g., 3-5 attempts). After repeated failures, messages are moved to DLQ instead of being lost. This satisfies requirement (1).
2. **Idempotency keys**: Each payment request should include a unique idempotency key (e.g., UUID). Before processing, check if this key already exists in DynamoDB; if it does, the payment was already charged — return success without re-charging. This satisfies requirement (2).
3. **CloudWatch Alarm on DLQ depth**: Set a CloudWatch alarm that triggers an SNS notification to the team whenever the DLQ has > 0 messages. This satisfies requirement (3) with immediate notification. Exponential backoff with jitter should also be implemented to avoid thundering herd during the bank API recovery.

---

**Q21.** Priya is the Lead DevOps Engineer at a healthcare company. She needs to deploy a configuration management update to 500 EC2 instances running across multiple regions. The change must result in exactly the same configuration on every instance regardless of how many times the update is applied. A previous incident occurred because engineers manually SSH'd into servers and made different changes on different machines. What principle and tooling should Priya adopt?

**Answer: Idempotency and Infrastructure as Code (IaC)**
The root cause was **configuration drift** — servers diverged from the desired state due to manual changes (the "pets" anti-pattern). Priya should:
1. Adopt **IaC using Terraform or AWS CloudFormation** to declare desired infrastructure state. Running `terraform apply` multiple times should produce the same result (idempotency).
2. Treat servers as **cattle, not pets** — no manual SSH changes; all changes go through code and CI/CD pipelines.
3. Use **Packer to create golden images** with the configuration baked in, then deploy new instances from the image instead of patching running servers (immutable infrastructure).
4. Enforce policy: no manual changes; all modifications must be made via IaC and peer-reviewed in git.

---

**Q22.** An online gaming platform is designing their notification system. When a player achieves a new rank, they want to: (1) send a push notification to the mobile app, (2) update the leaderboard service, (3) send a congratulatory email, and (4) award achievement badges. The engineering team is debating between direct service-to-service API calls vs an event-driven approach. What would you recommend, and why?

**Answer: Event-Driven Architecture with SNS Fan-Out**
The team should use SNS + SQS fan-out:
```
Achievement Event → SNS Topic
  → SQS Queue → Push Notification Service
  → SQS Queue → Leaderboard Service
  → SQS Queue → Email Service
  → SQS Queue → Badge Service
```
**Why**: Direct API calls create **temporal coupling** — if any downstream service is down, the entire achievement flow fails. With EDA: (1) The achievement event is published once to SNS; all 4 services receive it independently, (2) if Email Service is temporarily down, its SQS queue retains the message until the service recovers, (3) services can scale independently, (4) adding a 5th downstream action (e.g., analytics) requires no changes to the achievement service. Each consumer queue should also have a DLQ configured so failures are captured rather than lost.

---

**Q23.** TechCorp's SRE team has set an SLO of 99.9% availability for their API service. Last month, the service experienced three incidents totaling 35 minutes of downtime. The development team wants to ship a major new feature that historically causes a 2-minute disruption during deployment. Should the SRE team approve the deployment this month?

**Answer: No — the error budget is exhausted.**
Calculation:
- Monthly error budget = (1 - 0.999) × 30 days × 24 hours × 60 min = **43.2 minutes**
- Downtime consumed: 35 minutes
- Remaining budget: 43.2 - 35 = **8.2 minutes**
- Deployment risk: 2 minutes (cuts budget to 6.2 minutes remaining)

While 8.2 minutes remain and the deployment only risks 2 minutes, the SRE team must consider that the month isn't over and any additional incidents would exhaust the remaining 6.2 minutes. More importantly, if the month has seen 35 minutes of downtime (close to budget exhaustion), the system is clearly unstable. SRE best practice: when error budget is nearly exhausted, prioritize **reliability improvements** over new features. The SRE team should deny the feature deployment and require the dev team to focus on fixing the root causes of the three incidents first.

---

**Q24.** GlobalShop, a retail company, uses a content delivery network for their product catalog website. Their catalog contains 2 million products with images updated daily. A product manager reports that customers sometimes see outdated product images even after the catalog is updated. The engineering team currently uses a TTL of 30 days for all cached content. What is the correct solution?

**Answer: Cache Busting with Version Hashing**
The 30-day TTL means cached images won't expire for a month after updates. Two approaches:
1. **Cache busting (recommended)**: Append a content hash to image URLs (e.g., `product-123.a7f3c2.jpg`). When the image changes, the hash changes, creating a new cache key — edge servers automatically fetch the new image. Old images expire naturally. This allows long TTLs (better cache hit rate) while ensuring fresh content on change.
2. **Cache invalidation**: Explicitly tell the CDN to expire specific cached objects when they change. AWS CloudFront supports invalidation APIs but incurs costs and complexity.

**Why not short TTL?** Reducing TTL to 1 day would mean 95%+ of requests miss cache during peak hours (each of 2M images needs re-fetching daily), dramatically increasing origin load and costs.

The best architecture: use cache busting (unique URLs per version) + long TTL (30 days is fine) + CDN invalidation only for emergency corrections.

---

**Q25.** Diana is architecting a new e-commerce application on AWS. The app needs: user authentication, product catalog storage, order processing, and a recommendation engine. Her team debates whether to build a microservices architecture or a monolith. The team has 4 engineers, the business requirements are still unclear, and they need to launch in 6 weeks. What architectural approach should Diana recommend?

**Answer: Monolith (with clean internal boundaries)**
Despite the microservices trend, Diana should recommend a **monolith** for these specific reasons:
1. **Team size**: 4 engineers are below the 5-10 developer threshold where microservices overhead becomes worthwhile
2. **Unclear requirements**: Microservices require well-understood domain boundaries. Premature decomposition leads to "distributed monolith" — the worst outcome
3. **6-week deadline**: Microservices add operational complexity (service discovery, distributed tracing, network latency, multiple deployments) that would make the deadline nearly impossible
4. **Martin Fowler's advice**: Start with a monolith; you can always decompose later using the **Strangler Fig pattern** when pain points become clear

Diana should build the monolith with **clean internal module boundaries** (auth, catalog, orders, recommendations as separate modules/packages) — this allows future extraction into microservices when the team grows and domain is better understood. The rule: domain complexity + team size must justify the operational overhead of microservices.

---

**Q26.** A company's DevOps team is setting up their email infrastructure using AWS SES to send both transactional emails (order confirmations, password resets) and marketing newsletters to 500,000 subscribers. Over the past week, their bounce rate jumped to 6% and their complaint rate reached 0.3%. What immediate actions should the team take, and what long-term practices should they implement?

**Answer:**

**Immediate Actions (crisis response)**:
- AWS SES will **warn at 5% bounce rate** and **pause sending at 10%** — at 6%, sending may already be suspended. Check SES console immediately.
- **Purge invalid addresses**: Remove all hard bounces from the mailing list immediately and permanently. Hard bounces (invalid addresses) should never be retried.
- **Suppress complaint sources**: Remove any addresses that marked mail as spam from future marketing sends.
- Contact AWS support to explain remediation steps and potentially restore sending.

**Long-term Infrastructure**:
1. **Separate transactional from marketing**: Use different subdomains (`mail.company.com` for transactional, `news.company.com` for marketing). Protect transactional reputation from marketing reputation.
2. **Implement SPF + DKIM + DMARC** on both subdomains to improve deliverability and prevent spoofing.
3. **List hygiene**: Implement double opt-in for marketing, regular list cleaning, and real-time bounce processing via SES SNS notifications.
4. **Monitor metrics**: Set CloudWatch alarms on SES bounce rate (>2% = investigate) and complaint rate (>0.08% = investigate) — catch problems before they hit AWS thresholds.
5. **Never use noreply@**: Use a monitored reply address to catch auto-replies and complaints.

---

---

## 23. 30条最重要最容易考的知识点

> 按模块归类，标注考点类型：⭐概念辨析 / ⚠️陷阱 / 🔄易混淆对比

---

### 云基础 & 服务模型

**1. ⭐ NIST 5大特性必须全背**
On-demand self-service / Broad network access / Resource pooling / Rapid elasticity / Measured service。考试会给"特性"让你判断哪个不属于，常见干扰项："vendor lock-in prevention"、"multi-cloud support"。

**2. 🔄 IaaS / PaaS / SaaS 客户责任边界**
- IaaS：客户管 OS + App + 数据 + IAM + 网络配置 + 防火墙 + 加密
- PaaS：客户管 App + 数据
- SaaS：客户只管数据 + 访问控制
记忆口诀：**越往上(SaaS)，客户管的越少**。

**3. ⚠️ "Security OF the cloud" vs "Security IN the cloud"**
Provider = Security OF the cloud（物理机房/虚拟化/硬件）。Customer = Security IN the cloud（OS补丁/应用代码/IAM策略/加密配置）。IaaS客户责任最重，SaaS最轻。

---

### 网络

**4. 🔄 Security Group vs NACL（必考对比）**
| | SG | NACL |
|---|---|---|
| 状态 | **Stateful**（有状态，回包自动放行） | **Stateless**（无状态，双向都要配规则） |
| 层级 | 实例级别 | 子网级别 |
| 规则 | 仅允许(Allow only) | 允许 + 拒绝(Allow + Deny) |

**5. ⭐ NAT Gateway 的方向**
NAT Gateway 只允许**私有子网主动访问外网**（出站），外网无法主动发起连接到私有子网（无入站）。Internet Gateway 双向通。

---

### DevOps & 12-Factor

**6. ⭐ 12-Factor 三大高频考点**
- **Factor III（Config）**：配置放环境变量，绝不硬编码在代码里
- **Factor VI（Processes）**：进程无状态(stateless)，不在本地保存会话
- **Factor XI（Logs）**：日志是流(event stream)，不写文件，输出到 stdout

**7. 🔄 Continuous Delivery vs Continuous Deployment**
- Continuous **Delivery** = 每次提交**可以**部署，但有**人工审批**门控
- Continuous **Deployment** = 每次提交**自动**部署到生产，无人工介入
⚠️ 两者名字极像，考试爱混！

**8. ⭐ DORA 4指标**
Deployment Frequency / Lead Time for Changes / Change Failure Rate / **MTTR**（Mean Time to Recover）。衡量DevOps成熟度。

---

### IAM & 定价

**9. ⭐ EC2定价4种模型对比**
| 类型 | 承诺 | 折扣 | 适合 |
|---|---|---|---|
| On-Demand | 无 | 0 | 短期/不可预测 |
| Reserved | 1-3年 | 最高72% | 稳定基线负载 |
| Spot | 无 | 最高90% | 容错/批处理 **[可被中断]** |
| Dedicated Host | 按主机 | 按合同 | 合规/软件许可 |

**10. ⚠️ GCP SUDs vs CUDs**
- SUDs（Sustained Use）= **自动**给折扣，VM当月运行>25%即触发，最高**30%**
- CUDs（Committed Use）= 需**主动承诺**1或3年，最高**70%**
⚠️ SUDs不需要承诺，CUDs需要承诺——考试最爱考这个区别。

**11. 🔄 Type 1 vs Type 2 Hypervisor**
- Type 1（Bare-metal）= 直接运行在物理硬件上（VMware ESXi / KVM / Xen）→ 云计算用这个
- Type 2（Hosted）= 运行在宿主OS上（VirtualBox / VMware Workstation）→ 本地开发用

---

### Git & IaC

**12. ⚠️ Git是快照(Snapshot)，不是差异(Delta)**
Git每次提交存的是所有文件的完整快照，不是与上次的diff。这是与SVN等CVCS的核心区别。

**13. ⭐ Terraform工作流**
`init` → `plan`（预览，不执行）→ `apply`（实际创建/修改）→ `destroy`
`terraform.tfstate` 文件记录真实资源状态，**绝不能提交到git**（含敏感数据）。

**14. ⭐ IaC幂等性(Idempotency)**
无论执行多少次`apply`，结果都一样。这是IaC区别于手动操作的核心价值。**Config Drift（配置漂移）**= 实际状态偏离定义状态，IaC能发现并修正。

---

### 机器镜像 & Cloud-Init

**15. ⚠️ Instance Store vs EBS-backed AMI**
- EBS-backed：停机/终止后数据**持久化**保留 ✅
- Instance Store：停机/终止后数据**永久丢失** ⚠️（重启也丢失）
⚠️ 考试常问"哪种存储在实例终止后数据会丢失"。

**16. ⭐ Cloud-Init只运行一次**
Cloud-Init在**首次启动**时运行，后续重启不再执行。User Data必须以`#cloud-config`开头。IMDS地址：AWS = `169.254.169.254`，GCP = `metadata.google.internal`。

---

### 存储 & 数据库

**17. 🔄 Multi-AZ vs Read Replica**
| | Multi-AZ | Read Replica |
|---|---|---|
| 目的 | **高可用(HA)**，故障转移 | **读性能**，分担读压力 |
| 同步方式 | 同步(Synchronous) | 异步(Asynchronous) |
| 能接受写请求吗？ | ❌ Standby不处理流量 | ❌ 只读 |
⚠️ Multi-AZ不能提升性能，只是为了可用性和自动故障转移。

**18. ⭐ CAP定理三选二**
C（一致性）+ A（可用性）+ P（分区容错）三选二。实际分布式系统P是必须有的（网络分区不可避免），所以只能在**CP**（牺牲可用性）和**AP**（牺牲一致性）之间选。

**19. ⭐ S3存储类别记忆**
Standard → Standard-IA → Glacier Instant → Glacier Flexible → Glacier Deep Archive（从贵到便宜，从快到慢）。Intelligent-Tiering = 不知道访问模式时用。

---

### DNS & CDN

**20. ⚠️ CNAME三大限制**
1. CNAME只能指向**域名**，不能直接指向IP地址
2. CNAME**不能用在根域名**（apex domain，如 example.com）——因为根域名必须有SOA和NS记录
3. AWS Route 53的ALIAS记录是**非标准扩展**，可以用在根域名，但不是标准DNS

**21. ⭐ DNS记录类型高频考点**
A=IPv4 / AAAA=IPv6 / CNAME=别名指向域名 / MX=邮件服务器(有优先级) / TXT=SPF/DKIM/验证 / PTR=反向DNS(IP→域名) / NS=权威名称服务器

**22. 🔄 Anycast vs Unicast**
- Anycast = 多台服务器共享同一个IP，请求路由到**最近的**那台（CDN/DNS用）
- Unicast = 一个IP对应一台特定服务器
CDN用Anycast实现就近访问。

---

### SRE & 可观测性

**23. ⭐ SLI / SLO / SLA三层关系**
SLI（测量值）→ SLO（内部目标）→ SLA（对外合同，违约有赔偿）。
**Error Budget = 1 - SLO**。例：SLO=99.9% → 每月Error Budget = 43.2分钟。Error Budget耗尽 = 停止发布新功能，优先修复稳定性。

**24. ⭐ Toil的定义**
Toil = 手动的 + 重复的 + 可自动化的 + 战术性的 + 随业务线性增长的 + 无持久价值的工作。SRE应将Toil控制在工作量的**50%以下**。

**25. ⭐ 日志级别顺序**
`ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < OFF`
生产环境最低用**INFO**级别。**DEBUG绝不在生产使用**（性能+安全风险）。

---

### 邮件

**26. 🔄 SPF vs DKIM vs DMARC**
| | SPF | DKIM | DMARC |
|---|---|---|---|
| 验证方式 | IP白名单 | 非对称加密签名 | 策略层 |
| 转发后有效？ | ❌ 断 | ✅ 有效 | 取决于对齐 |
| 作用 | 哪些IP可以发邮件 | 邮件未被篡改 | 违规邮件如何处理(none/quarantine/reject) |

**27. ⚠️ 硬退回(Hard Bounce)必须立即删除**
Hard Bounce = 地址永久无效，**永不重试，立即从列表删除**。AWS SES：退回率>5%警告，>10%暂停发送。投诉率>0.1%警告，>0.5%暂停。

---

### 负载均衡 & 弹性伸缩

**28. 🔄 ALB vs NLB**
| | ALB（Application） | NLB（Network） |
|---|---|---|
| 层级 | Layer 7（HTTP/HTTPS） | Layer 4（TCP/UDP） |
| 路由方式 | 路径/Host/Header/Cookie | IP+端口 |
| 特点 | WAF集成/Cognito认证 | **静态IP每AZ**，百万级req/s |
| 跨区负载 | **默认开启** | **默认关闭** ⚠️ |

**29. ⭐ Auto Scaling关键参数**
- **Cooldown（冷却）**：默认300秒，上次扩缩容后等待时间，防止频繁操作
- **Warm-up（预热）**：新实例准备好之前不计入指标，防止过早触发下次扩容
- 最常用策略：**Target Tracking**（保持指标在目标值，如CPU=70%）

---

### 微服务 & 事件驱动 & Serverless

**30. ⭐ 微服务三大反模式（考试爱考"什么是错误做法"）**
1. **共享数据库** = 最常见反模式，破坏松耦合，正确做法：每服务独立数据库
2. **分布式单体（Distributed Monolith）** = 服务拆分了但依然共享DB/强依赖，同步部署 → 同时拥有两种架构的缺点
3. **同步调用链过长** = A→B→C→D，任一失败级联崩溃，应用异步+事件驱动解耦

---

*End of Cheatsheet — Good luck on your CSYE 6225 final exam! 加油！*
