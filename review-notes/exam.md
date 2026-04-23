# CSYE 6225 Final Exam — 押题预测

> 考试信息：100分 · 60分钟 · 50%客观题 + 50%主观题
> 题型：True/False · Multiple Choice · Fill-in-the-blank · Numeric · 简答

---

## 零、样题解析（老师给的3道原题）

> 这3道题直接揭示了出题风格和考点方向，必须吃透。

**样题1 — True/False（答案：FALSE）**
> "One of the benefits of using a Virtual Machine is the sharing of root volume and the operating system with other Virtual Machines as long as they use the same operating system, making them lightweight."

**FALSE。** 这描述的是**Container（容器）**，不是VM。
- **VM**：每台都有自己独立的OS、独立的root volume，彼此完全隔离，**重量级**
- **Container**：共享宿主机OS内核，不需要独立OS，所以**轻量级**

⚠️ 这道题是经典陷阱：把Container的特性说成是VM的特性。考试极可能再出VM vs Container对比题。

---

**样题2 — Fill-in-the-blank（答案：0）**
> "Every Linux command executed by the shell script or user has an exit status. A command that is executed successfully without any errors will exit with the status code of _________."

**答案：0**
Linux/Unix约定：exit code **0 = 成功**，**非0 = 失败**（1, 2, 126, 127, 128+N等各有含义）
```bash
$ ls /tmp
$ echo $?    # 输出 0 — 上个命令成功
$ ls /nonexistent
$ echo $?    # 输出 2 — 失败
```
⚠️ 说明考试**会考Linux/Shell基础**，不只是云服务概念。

---

**样题3 — Fill-in-the-blank（答案：cooldown）**
> "In auto scaling, a ____________ period is a configurable period of time that prevents the auto scaler from launching or terminating additional instances immediately after a scaling activity completes..."

**答案：cooldown（冷却期）**
- 默认值：**300秒（5分钟）**
- 作用：防止频繁扩缩容，等新实例稳定后再判断是否需要下一次扩缩容
- 与warm-up的区别：cooldown是扩缩容后等待；warm-up是新实例启动后预热期

---

## 样题揭示的出题规律

| 发现 | 影响 |
|---|---|
| VM vs Container 是陷阱题 | **VM/Container对比必考** |
| 考Linux exit code | **Shell脚本基础会考** |
| 考cooldown精确术语 | **Fill-in-the-blank要求精确词汇** |
| True/False用"正确描述贴在错误对象上"的方式出陷阱 | **看清主语！VM还是Container？** |

---

## 一、考试结构预测（已根据样题修正）

| 题型 | 预计占比 | 分值估算 | 特点 |
|---|---|---|---|
| True/False | 15-20% | ~15-20分 | 考陷阱，答案非对即错 |
| Multiple Choice | 20-25% | ~20-25分 | 单选或多选，看题干 |
| Fill-in-the-blank | 10-15% | ~10-15分 | 考精确术语，不能模糊 |
| Numeric | 5-10% | ~5-10分 | SLO/Error Budget计算，可用性计算 |
| 简答/主观 | ~50% | ~50分 | 场景题、解释原理，简洁为王 |

---

## 二、高概率考点 TOP 30（押题核心）

### 🔥 新增：样题直接暴露的必考点

---

**押题 0A — True/False：Container vs VM（样题同款陷阱）**

> Containers share the host operating system kernel with other containers, making them more lightweight than Virtual Machines.

**答案：TRUE**

核心对比表：

| | VM | Container |
|---|---|---|
| OS | 每个VM有**独立完整OS** | 共享**宿主机OS内核** |
| 体积 | 重量级（GB级镜像） | 轻量级（MB级镜像） |
| 启动时间 | 分钟级 | 秒级 |
| 隔离性 | 强（硬件级隔离） | 弱（进程级隔离） |
| 虚拟化层 | Hypervisor（Type 1/2） | Container Runtime（Docker） |
| 适合 | 不同OS、强隔离需求 | 微服务、快速部署 |

⚠️ **考试陷阱**：把Container特性（共享OS/轻量级）说成是VM的特性 → FALSE

---

**押题 0B — Fill-in-the-blank：Linux Exit Code（样题同款）**

> A Linux command that completes successfully exits with status code _____.

**答案：0**

其他常见exit code：
- `1` = 通用错误
- `2` = 命令使用错误（如参数错误）
- `126` = 命令不可执行（权限问题）
- `127` = 命令未找到（command not found）
- `128+N` = 被信号N终止（如`kill -9`→137）

用`$?`查看上一个命令的exit code。

---

**押题 0C — Fill-in-the-blank：Cooldown（样题原题）**

> In auto scaling, a ____________ period prevents the auto scaler from immediately triggering another scaling action after one completes.

**答案：cooldown**（默认300秒）

---

### 🔥 极高概率（必考）

---

**押题 1 — True/False：Security Group vs NACL**

> Security Groups are stateless and can have both allow and deny rules.

**答案：FALSE**
SG是**Stateful（有状态）**的，而且只有Allow规则。NACL才是Stateless，才有Allow+Deny。

---

**押题 2 — Fill-in-the-blank：Error Budget计算**

> If a service has an SLO of 99.9%, what is its monthly error budget in minutes?

**答案：43.2分钟**
计算：`(1 - 0.999) × 30 × 24 × 60 = 43.2 min`

---

**押题 3 — True/False：CNAME限制**

> A CNAME record can be used at the apex (root) of a domain such as example.com.

**答案：FALSE**
CNAME不能用在根域名，因为根域名必须有SOA和NS记录，CNAME与它们不能共存。AWS Route 53的ALIAS是非标准扩展，可以用在根域名。

---

**押题 4 — Multiple Choice：持续交付 vs 持续部署**

> Which statement correctly distinguishes Continuous Delivery from Continuous Deployment?

A. Continuous Delivery deploys automatically; Continuous Deployment requires human approval
B. Continuous Delivery requires human approval; Continuous Deployment deploys automatically ✅
C. They are the same thing
D. Continuous Deployment is a subset of Continuous Delivery

**答案：B**

---

**押题 5 — Numeric：可用性计算**

> A system had 2 hours of downtime last month (30 days). What is its availability percentage?

**答案：99.72%**
计算：`Uptime = 30×24×60 - 120 = 43080 min`
`Availability = 43080 / 43200 = 99.72%`

---

**押题 6 — True/False：Git存储机制**

> Git stores file changes as deltas (differences) from the previous version.

**答案：FALSE**
Git基于**快照(snapshot)**，不是差异(delta)。每次commit存所有文件的完整快照。

---

**押题 7 — Fill-in-the-blank：Lambda限制**

> The maximum execution timeout for an AWS Lambda function is _____.

**答案：15 minutes（15分钟）**

---

**押题 8 — True/False：GCP SUDs**

> GCP Sustained Use Discounts (SUDs) require customers to make an upfront commitment.

**答案：FALSE**
SUDs是**自动**给的，不需要承诺。运行超过当月25%时间自动触发，最高折扣30%。CUDs才需要主动承诺。

---

**押题 9 — True/False：Read Replica vs Multi-AZ**

> AWS RDS Multi-AZ deployments can be used to improve read performance by routing read queries to the standby instance.

**答案：FALSE**
Multi-AZ的Standby实例**不处理任何流量**，纯粹用于故障转移(failover)。提升读性能要用Read Replica。

---

**押题 10 — Fill-in-the-blank：SLI/SLO/SLA**

> The quantitative measure used to evaluate service performance (e.g., request success rate) is called a(n) _____.

**答案：SLI（Service Level Indicator）**

---

### 🔥 高概率（很可能考）

---

**押题 11 — True/False：Instance Store**

> Data stored on an EC2 instance store volume persists after the instance is stopped.

**答案：FALSE**
Instance Store数据在实例**停止(stop)或终止(terminate)后永久丢失**。EBS-backed才持久。

---

**押题 12 — Multiple Choice：SPF转发**

> Which email authentication mechanism fails when email is forwarded?

A. DKIM
B. DMARC
C. SPF ✅
D. TLS

**答案：C**
SPF基于IP验证，转发时发件IP变了，不在原始SPF记录里，导致验证失败。

---

**押题 13 — Fill-in-the-blank：Cloud-Init**

> Cloud-Init user data scripts must begin with the header _____ to be interpreted as a cloud configuration file.

**答案：`#cloud-config`**

---

**押题 14 — True/False：Cloud-Init运行次数**

> Cloud-Init runs every time a cloud instance is rebooted.

**答案：FALSE**
Cloud-Init只在**首次启动(first boot)**时运行一次。

---

**押题 15 — Multiple Choice：CAP定理**

> In a distributed system experiencing a network partition, which two properties can a system guarantee simultaneously?

A. Consistency and Availability
B. Consistency and Partition Tolerance ✅
C. Availability and Partition Tolerance ✅
D. All three simultaneously

**答案：B 或 C（二选一，不能同时选）**
CAP三选二，P（分区容错）在分布式系统中必须保留，所以选CP或AP。

---

**押题 16 — Fill-in-the-blank：Terraform**

> In Terraform, the command used to preview infrastructure changes without applying them is _____.

**答案：`terraform plan`**

---

**押题 17 — True/False：Microservices数据库**

> In a microservices architecture, services should share a common database to simplify data consistency.

**答案：FALSE**
共享数据库是**反模式(anti-pattern)**，破坏服务间的松耦合。正确做法是每个服务有独立的数据库。

---

**押题 18 — Numeric：SQS Visibility Timeout**

> What is the default visibility timeout for an AWS SQS message?

**答案：30 seconds**

---

**押题 19 — Multiple Choice：Lambda冷启动**

> Which runtime has the SLOWEST cold start time in AWS Lambda?

A. Go
B. Python
C. Node.js
D. Java ✅

**答案：D**
Java/Spring因为JVM启动+框架初始化时间最长。Go最快。

---

**押题 20 — True/False：ALB跨区负载均衡**

> AWS Network Load Balancer (NLB) has cross-zone load balancing enabled by default.

**答案：FALSE**
NLB跨区负载均衡**默认关闭**。ALB跨区负载均衡**默认开启**。

---

**押题 21 — Fill-in-the-blank：12-Factor**

> According to the 12-Factor App methodology, application configuration such as database URLs and API keys should be stored in _____.

**答案：environment variables（环境变量）**

---

**押题 22 — Multiple Choice：Toil**

> Which of the following is NOT a characteristic of toil?

A. Manual
B. Repetitive
C. Provides long-term strategic value ✅
D. Automatable

**答案：C**
Toil的特征是"无持久价值(no enduring value)"。提供长期战略价值的工作不是Toil。

---

**押题 23 — True/False：AWS RDS加密**

> AWS RDS instances are encrypted at rest by default without any additional configuration.

**答案：FALSE**
AWS RDS**不是默认加密**，必须在**创建时**手动启用加密，且创建后无法更改。GCP和Azure默认加密。

---

**押题 24 — Fill-in-the-blank：DNS**

> The DNS record type used to map a domain name to an IPv4 address is called an _____ record.

**答案：A record**

---

**押题 25 — Multiple Choice：部署策略**

> Which deployment strategy requires maintaining two full production environments and allows the fastest rollback?

A. Rolling
B. Blue-Green ✅
C. Canary
D. Immutable

**答案：B**
Blue-Green维护两套完整环境，切换LB即可回滚，速度最快，但费用是2倍。

---

### 🔥 中高概率（有可能考）

---

**押题 26 — Numeric：SQS FIFO吞吐量**

> What is the maximum throughput (messages per second) for an AWS SQS FIFO queue without batching?

**答案：300 messages/second**

---

**押题 27 — True/False：Hypervisor**

> Cloud providers typically use Type 2 (hosted) hypervisors because they run on top of a host operating system.

**答案：FALSE**
云服务商使用**Type 1（Bare-metal）Hypervisor**，直接运行在物理硬件上，性能更好。Type 2运行在宿主OS上，用于本地开发（如VirtualBox）。

---

**押题 28 — Fill-in-the-blank：Packer**

> The three Packer commands used to build a machine image, in order, are: _____, _____, _____.

**答案：`packer init` → `packer validate` → `packer build`**

---

**押题 29 — Multiple Choice：OWASP Top 10**

> According to OWASP Top 10, what is the #1 web application security risk?

A. Injection
B. Cryptographic Failures
C. Broken Access Control ✅
D. Security Misconfiguration

**答案：C**（2021版）

---

**押题 30 — True/False：CDN**

> A CDN is appropriate for caching personalized user-specific content such as account dashboards.

**答案：FALSE**
CDN适合静态、公共可缓存内容（图片/JS/CSS）。个性化内容(personalized)每个用户不同，缓存命中率为0，不适合CDN。

---

## 三、主观题押题（简答方向）

> 这类题考"能不能用自己的话解释清楚"，不需要写很多，2-4句话到位即可。

### 最可能出现的简答题方向：

**A. 解释SLO、SLI、SLA的关系和Error Budget**
> 答题要点：SLI是测量值（如请求成功率99.5%）；SLO是内部目标（如≥99.9%）；SLA是与客户的合同（违约有赔偿）。Error Budget = 1 - SLO，当Error Budget耗尽时应暂停新功能发布，优先保障稳定性。

**B. 解释为什么微服务不应该共享数据库**
> 答题要点：共享数据库导致服务间强耦合——一个服务的schema变更会影响所有其他服务，无法独立部署，违背微服务"松耦合(loose coupling)"原则。正确做法是每个服务拥有自己的数据库，通过API或事件通信。

**C. 描述Blue-Green vs Canary部署策略的区别**
> 答题要点：Blue-Green维护两套完整环境，一次性全量切换，回滚快但成本2倍；Canary先将少量流量（如5%）引流到新版本，监控指标后逐步推广，更精细但更复杂，能发现真实问题后再全量。

**D. 解释Shared Responsibility Model**
> 答题要点：云服务商负责"云的安全"（物理设施/网络/虚拟化/硬件）；客户负责"云中的安全"（OS补丁/应用代码/IAM/加密/网络配置）。服务模型越高（SaaS），客户责任越少。

**E. 解释SPF、DKIM、DMARC各自作用**
> 答题要点：SPF通过DNS TXT记录白名单哪些IP可以发邮件（IP验证，转发会断）；DKIM通过非对称加密签名证明邮件未被篡改（转发后仍有效）；DMARC是策略层，要求SPF或DKIM至少一个对齐，并规定不合规邮件的处理方式（none/quarantine/reject）。

**F. 解释CAP定理并给出实际例子**
> 答题要点：分布式系统无法同时保证一致性(C)、可用性(A)、分区容错(P)三者。网络分区不可避免，所以P必须保留。选CP（如关系型数据库集群）牺牲可用性；选AP（如DynamoDB默认配置）牺牲一致性，接受最终一致性。

**G. 解释Event-Driven Architecture相比同步调用的优势**
> 答题要点：同步调用产生时间耦合(temporal coupling)，下游服务不可用会导致级联失败。EDA中Producer发布事件后不等待，Broker保存消息，Consumer独立处理，实现时间解耦、弹性伸缩和故障隔离。

---

## 四、Fill-in-the-blank 高频词汇表

> 这些词必须能精确拼写，不能写模糊

| 考点 | 精确答案 |
|---|---|
| 云计算5大特性之一（弹性） | Rapid Elasticity |
| Git存储机制 | Snapshot（快照） |
| IaC幂等性 | Idempotency |
| Terraform状态文件 | terraform.tfstate |
| Lambda最长运行时间 | 15 minutes |
| SQS默认可见性超时 | 30 seconds |
| SQS FIFO最大吞吐 | 300 msg/s |
| Cloud-Init首行 | #cloud-config |
| AWS IMDS地址 | 169.254.169.254 |
| SRE提出者 | Ben Treynor（Google） |
| DORA指标之一（恢复） | MTTR（Mean Time to Recover） |
| 邮件投诉率警告阈值 | 0.1% |
| AMI推荐虚拟化类型 | HVM |
| ALB操作层级 | Layer 7 |
| NLB操作层级 | Layer 4 |
| Error Budget公式 | 1 - SLO |
| Linux成功退出码 | 0 |
| Linux命令未找到退出码 | 127 |
| 查看上一个命令退出码 | `$?` |
| Auto Scaling冷却时间默认 | 300 seconds（cooldown） |
| Container共享的是 | 宿主机OS内核（host OS kernel） |
| VM独立拥有的是 | 完整OS + 独立root volume |

---

## 五、子网计算专项（Subnetting）

> 考试最可能出 Numeric 或 Fill-in-the-blank 题，给你一个 CIDR，问地址数/可用主机数/网络地址/广播地址。

---

### 核心公式

```
给定 CIDR：X.X.X.X/n

总地址数        = 2^(32 - n)
可用主机数      = 2^(32 - n) - 2      ← 减去 网络地址 + 广播地址
网络地址        = CIDR 中的 X.X.X.X   ← 主机位全0
广播地址        = 主机位全1
第一个可用IP    = 网络地址 + 1
最后一个可用IP  = 广播地址 - 1
```

⚠️ **AWS 额外保留 5 个 IP**（每个子网）：

| 保留IP | 用途 |
|---|---|
| .0 | 网络地址 |
| .1 | VPC 路由器 |
| .2 | DNS 服务器 |
| .3 | AWS 预留 |
| .255（最后一个） | 广播地址 |

所以 **AWS 子网可用 IP = 2^(32-n) - 5**

---

### 常用 CIDR 速查表（背熟）

| CIDR | 总地址数 | 标准可用主机 | AWS可用IP |
|---|---|---|---|
| /16 | 65,536 | 65,534 | 65,531 |
| /20 | 4,096 | 4,094 | 4,091 |
| /24 | 256 | 254 | **251** |
| /25 | 128 | 126 | 123 |
| /26 | 64 | 62 | 59 |
| /27 | 32 | 30 | 27 |
| /28 | 16 | 14 | **11** |
| /29 | 8 | 6 | 3 |
| /30 | 4 | 2 | — (太小，AWS不推荐) |

---

### 子网掩码对照表

| CIDR前缀 | 子网掩码 |
|---|---|
| /8  | 255.0.0.0 |
| /16 | 255.255.0.0 |
| /24 | **255.255.255.0** ← 最常见 |
| /25 | 255.255.255.128 |
| /26 | 255.255.255.192 |
| /27 | 255.255.255.224 |
| /28 | 255.255.255.240 |

计算方法：/26 = 前26位全1 = `11111111.11111111.11111111.11000000` = 255.255.255.192

---

### 典型考题示例

**题型1 — Numeric**
> A VPC is configured with CIDR block 10.0.0.0/16. A subnet is created with CIDR 10.0.1.0/24. How many IP addresses are available for EC2 instances in this subnet on AWS?

**答案：251**
计算：2^(32-24) - 5 = 256 - 5 = **251**

---

**题型2 — Fill-in-the-blank**
> For a subnet 192.168.10.0/24, the network address is _____, the broadcast address is _____, and the first usable host IP is _____.

**答案：**
- 网络地址：`192.168.10.0`
- 广播地址：`192.168.10.255`
- 第一个可用IP：`192.168.10.1`

---

**题型3 — Numeric（拆分子网）**
> You have the CIDR block 10.0.0.0/24 and need to create 4 equal subnets. What prefix length should each subnet use, and how many usable hosts does each subnet have?

**答案：**
- 4个子网需要借2位（2² = 4）→ 前缀变为 /24 + 2 = **/26**
- 每个子网：2^(32-26) - 2 = 64 - 2 = **62个可用主机**（标准），AWS下为59个

4个子网分别是：
```
10.0.0.0/26    → 10.0.0.0   ~ 10.0.0.63
10.0.0.64/26   → 10.0.0.64  ~ 10.0.0.127
10.0.0.128/26  → 10.0.0.128 ~ 10.0.0.191
10.0.0.192/26  → 10.0.0.192 ~ 10.0.0.255
```

---

**题型4 — True/False（陷阱）**
> A /28 subnet in AWS VPC provides 14 usable IP addresses for EC2 instances.

**答案：FALSE**
/28 总地址 = 2^4 = 16，AWS保留5个，所以只有 **16 - 5 = 11** 个可用IP，不是14。
（14是标准计算 16-2=14，但AWS要多减3个）

---

**题型5 — Numeric（给定子网，问属于哪个子网）**
> A company uses CIDR 10.0.0.0/26 for its first subnet. What is the network address of the second /26 subnet in the 10.0.0.0/24 block?

**答案：10.0.0.64**
/26 = 64个地址，第一个子网占 0-63，第二个从 **64** 开始。

---

### 快速心算技巧

**记住"魔法数字"（256 - 子网掩码最后一段）：**

| 掩码最后段 | 每个子网大小 | 子网起始地址间隔 |
|---|---|---|
| 128 (/25) | 128 | 0, 128 |
| 192 (/26) | 64 | 0, 64, 128, 192 |
| 224 (/27) | 32 | 0, 32, 64, 96... |
| 240 (/28) | 16 | 0, 16, 32, 48... |
| 248 (/29) | 8  | 0, 8, 16, 24... |

口诀：**子网大小 = 256 - 掩码最后段的值**

---

## 五、最后冲刺 — 10条背熟口诀

1. **VM有独立OS/Container共享宿主OS内核** — 样题已考！Container轻量VM重量
2. **Linux exit 0=成功/非0=失败**；`$?`查看；127=命令不存在；126=无权限
3. **SG有状态/NACL无状态**；SG实例级/NACL子网级；SG只allow/NACL可deny
4. **Multi-AZ=高可用/Read Replica=读性能**；两者都不提升写性能
5. **CNAME不能在根域名**，不能指向IP，只能指向另一个域名
6. **SPF转发断/DKIM转发活**；DMARC是总策略层
7. **Lambda最长15分钟**；冷启动Java最慢/Go最快
8. **Git是快照不是差异**；三个状态：Working→Staging→Repo
9. **Error Budget = 1 - SLO**；99.9%→43.2分钟/月
10. **cooldown默认300秒**；防止频繁扩缩容；GCP SUDs自动/CUDs需承诺

---

*今晚六点，加油！You've got this! 🎯*
