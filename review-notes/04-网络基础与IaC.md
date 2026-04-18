# L05 — 网络基础、基础设施即代码与 Terraform

## 模块总览

本模块分三层递进：**网络基础**（TCP/UDP、IP、VPC、安全组）为云基础设施提供通信底座；**基础设施即代码（IaC）**将运维从手工脚本升级为可版本化的工程实践；**Terraform** 则是 IaC 的具体实现工具，以声明式 HCL 驱动多云资源管理。

---

## 核心概念

### 一、网络基础（Networking Fundamentals）

#### 1. OSI 7 层模型 vs TCP/IP 5 层模型

| 层次 | OSI 7 层 | TCP/IP 5 层 | 数据单元 | 典型协议/设备 |
|------|----------|-------------|----------|--------------|
| 7 | 应用层（Application） | 应用层 | APDU | HTTP, FTP, SMTP, DNS |
| 6 | 表示层（Presentation） | （合并入应用层） | PPDU | PNG, JPEG, 编解码 |
| 5 | 会话层（Session） | （合并入应用层） | SPDU | 用户会话管理 |
| 4 | 传输层（Transport） | 传输层 | TPDU/Segment | **TCP, UDP** |
| 3 | 网络层（Network） | 网络层 | Packet（数据包） | **IP**, 路由器 |
| 2 | 数据链路层（Data Link） | 数据链路层 | Frame（帧） | 交换机, MAC 地址 |
| 1 | 物理层（Physical） | 物理层 | Bit（位） | 网线, 光纤, 无线 |

> TCP/IP 中没有独立的表示层和会话层，合并进应用层。

#### 2. TCP vs UDP（高频考点）

| 维度 | TCP | UDP |
|------|-----|-----|
| 全称 | Transmission Control Protocol | User Datagram Protocol |
| 连接 | **有连接**（三次握手） | **无连接** |
| 可靠性 | ✅ 可靠（ACK确认、重传机制） | ❌ 不保证 |
| 顺序 | ✅ 有序传输 | ❌ 无序 |
| 错误检测 | ✅ 有 | ❌ 基本无 |
| 拥塞控制 | ✅ 有 | ❌ 无 |
| 速度 | 较慢（开销大） | **更快**（开销小） |
| 典型应用 | HTTP/HTTPS, FTP, SMTP, SSH | **DNS, DHCP**, 视频流, 游戏 |

#### 3. IP 地址

**IPv4**：
- 32 位，点分十进制（如 `192.168.1.1`）
- 最大约 **2³² ≈ 40 亿**个地址（**2011 年已耗尽**）
- 约 1800 万个私有地址

**IPv6**：
- **128 位**，冒号分隔的 16 进制（如 `2001:0db8::1`），零可省略
- 2¹²⁸ 个地址，**不需要 NAT**

**CIDR（Classless Inter-Domain Routing）表示法**：
```
192.168.1.0/24
│            └── 24 位网络前缀
│                8 位主机位 → 最多 256 个地址（可用 254 个）
└── 网络地址
```

#### 4. NAT（Network Address Translation，网络地址转换）

将私有 IP 地址转换为公网 IP，允许多个内部设备共享一个公网 IP。
- IoT 设备爆炸式增长推动了 NAT 需求
- NAT 不兼容 P2P 应用（双方都在 NAT 后面时难以建立连接）
- IPv6 普及后 NAT 的需求减少（地址足够多）

#### 5. VPC（Virtual Private Cloud，虚拟私有云）

AWS 中逻辑隔离的虚拟网络，可配置：
- **子网（Subnet）**：将 VPC 地址范围分割为更小的网段
- **路由表（Route Table）**：定义流量如何路由
- **互联网网关（Internet Gateway, IGW）**：允许 VPC 与互联网通信（水平扩展、高可用）
- **防火墙/安全组（Security Group）**：实例级虚拟防火墙

**公有子网 vs 私有子网**：
```
互联网
    ↓
互联网网关（IGW）
    ↓
公有子网（Public Subnet）→ Web 服务器（有公网 IP）
    ↓（通过 NAT 网关）
私有子网（Private Subnet）→ 数据库（无公网 IP）
```

**路由表关键规则**：
- 每个子网只能关联**一个**路由表
- 多个子网可以共享同一个路由表
- 本地（Local）路由允许 VPC 内部通信

#### 6. 安全组（Security Group）

实例级虚拟防火墙，控制入站（Inbound）和出站（Outbound）流量：

| 协议 | 端口 | 用途 |
|------|------|------|
| TCP | 80 | HTTP |
| TCP | 443 | HTTPS |
| TCP | 22 | SSH |
| TCP | 3389 | RDP（Windows 远程桌面） |
| TCP | 3306 | MySQL |
| TCP | 1433 | SQL Server |

- 每个实例最多关联 **5 个**安全组
- 安全组是**有状态的（Stateful）**：允许入站的响应流量自动允许出站

#### 7. 弹性 IP（Elastic IP）

静态公有 IPv4 地址，可以绑定到任意实例：
- 通过重新绑定**掩盖实例故障**（不需要更新 DNS）
- 不支持 IPv6
- 未使用时仍然计费（鼓励释放不用的 IP）

---

### 二、基础设施即代码（Infrastructure as Code, IaC）

#### 1. 定义

将基础设施配置以**代码形式**表达，使用软件开发实践（版本控制、测试、CI/CD）来管理基础设施。

核心思想：基础设施 = 软件 + 数据，而不是手工操作。

#### 2. 为什么需要 IaC？（IaC 解决的问题）

| 问题 | 描述 | 后果 |
|------|------|------|
| **服务器蔓延（Server Sprawl）** | 快速配置能力 → 大量无法追踪的服务器 | 资源浪费、安全风险 |
| **配置漂移（Configuration Drift）** | 服务器配置随时间偏离基准：热修复、版本不匹配、个人配置 | 环境不一致，"在我机器上能跑" |
| **雪花服务器（Snowflake Server）** | 每台服务器都独一无二，无法重现和替换 | 脆弱、不可维护 |
| **脆弱基础设施（Fragile Infrastructure）** | 雪花问题在组合层面放大 | 牵一发而动全身 |

#### 3. IaC 五大原则

| 原则 | 英文 | 说明 |
|------|------|------|
| 可再现 | Reproducible | 相同代码在任何环境产生相同结果 |
| 可丢弃 | Disposable | 任何基础设施都可以被销毁并重建 |
| **牲畜不是宠物** | **Cattle, not Pets** | 服务器是可替换的牲畜，而非需要精心照料的宠物 |
| 一致 | Consistent | Dev/Test/Prod 环境配置一致（对应 12-Factor Factor X） |
| 可重复 | Repeatable | 部署过程可以无限重复执行 |
| 为变化设计 | Design for Change | 假设基础设施总是在变化 |

> **"Cattle, not Pets"** 是 IaC 最重要的哲学：
> - 宠物（Pet）：有名字、需要照料、出问题要救治 → 传统服务器
> - 牲畜（Cattle）：编号管理、可批量替换、出问题直接换新的 → 云原生服务器

#### 4. 幂等性（Idempotency）

无论执行多少次，结果总是相同的。IaC 工具的核心属性：
- 第一次运行：创建资源
- 再次运行相同配置：无变化（因为已处于期望状态）
- 修改配置后运行：只更新有差异的部分

#### 5. Stack 与 Environment

- **Stack（栈）**：一组共同部署和管理的基础设施资源集合
- **Environment（环境）**：同一 Stack 的不同实例

```
同一套 Terraform 代码（Stack）
    ├── dev environment（开发环境）
    ├── test environment（测试环境）
    ├── preprod environment（预生产环境）
    └── prod environment（生产环境）
```

#### 6. VCS 在 IaC 中的作用

**应该存入版本控制**：
- 配置定义（Terraform .tf 文件、Ansible Playbooks）
- 配置模板
- 测试代码
- CI/CD 定义
- 文档

**不应该存入版本控制**：
- 软件构件（用制品库，如 ECR、Nexus）
- 可重建的构件
- 应用数据/日志/备份
- **密码和安全机密**（用 AWS Secrets Manager、HashiCorp Vault）

---

### 三、Terraform

#### 1. 核心概念

| 概念 | 说明 |
|------|------|
| **Provider（提供商）** | 连接云平台的插件（如 aws、gcp、azure），负责与 API 交互 |
| **Resource（资源）** | 基础设施的基本单元（EC2 实例、S3 存储桶、VPC 等）|
| **State（状态）** | `terraform.tfstate` 文件，记录真实资源与配置的映射关系 |
| **Module（模块）** | 一组资源的集合，用于复用和组织；有 root module 和 child modules |
| **Variable（变量）** | 参数化配置，避免硬编码；用 `var.<NAME>` 引用 |

#### 2. Terraform 工作流（核心命令）

```
terraform init → terraform plan → terraform apply → terraform destroy
```

| 命令 | 作用 | 是否修改真实资源 | 幂等？ |
|------|------|----------------|--------|
| `terraform init` | 下载 provider 插件到 `.terraform/` 目录 | ❌ 否 | ✅ 是 |
| `terraform plan` | 预览将要发生的变更（diff：代码 vs 现实） | ❌ **否**（只读） | ✅ 是 |
| `terraform apply` | 应用变更，达到期望状态 | ✅ 是 | ✅ 是 |
| `terraform destroy` | 销毁所有受管基础设施 | ✅ 是 | — |

#### 3. State 文件（`terraform.tfstate`）

- 记录 Terraform 管理的每个资源的当前状态
- 映射配置代码 → 真实云资源（如 `aws_instance.web` → `i-0abc123`）
- 每次 `plan` 和 `apply` 前自动刷新
- **`plan` 的输出**就是配置代码与现实状态的 diff

**State 文件的安全规范**：
```gitignore
# .gitignore 中必须排除：
.terraform/          # provider 插件（体积大，可重新 init）
*.tfstate            # 状态文件（含敏感信息！）
*.tfstate.backup     # 备份状态文件
```

**团队协作**：使用**远程状态（Remote State）**，如 S3 + DynamoDB 加锁，而非本地 tfstate 文件。

---

## 概念间关系

```
传统运维困境：
    手动配置 → 配置漂移 → 雪花服务器 → 脆弱基础设施
         ↓
IaC 解决方案：
    基础设施代码化 → 版本控制（Git）→ 可重现、可丢弃、幂等
         ↓
Terraform 实现：
    声明式（你说"要什么"）→ Provider 翻译成 API 调用 → 实际创建/修改/删除资源
    State 文件追踪现实状态 → plan 计算 diff → apply 执行最小变更

网络层为 IaC 管理的资源提供通信基础：
VPC（隔离）→ Subnet（分区）→ Security Group（过滤）→ Route Table（路由）→ IGW（出口）
    ↕
这些都可以用 Terraform HCL 声明式定义！

部署流水线集成：
Git push → CI 运行 terraform plan → 人工审查 diff → terraform apply → 基础设施更新
（对应 DevOps 的 GitOps 实践）
```

---

## 对比表格

### 1. 声明式 vs 命令式 IaC

| 维度 | 声明式（Declarative） | 命令式（Imperative） |
|------|----------------------|---------------------|
| 核心思路 | 你描述**想要什么（WHAT）** | 你描述**如何做（HOW）** |
| 幂等性 | ✅ 天然幂等 | ❌ 需要手动保证 |
| 状态感知 | ✅ 工具管理状态 | ❌ 需要自己追踪 |
| 代表工具 | **Terraform**, CloudFormation, Kubernetes YAML | Bash 脚本, Ansible（部分） |
| 示例思路 | "我需要 3 台 t3.micro" | "先创建实例1，再创建实例2，再创建实例3" |
| 适合场景 | 基础设施生命周期管理 | 一次性脚本、顺序性任务 |

### 2. Terraform vs CloudFormation

| 维度 | Terraform | AWS CloudFormation |
|------|-----------|-------------------|
| 开发者 | HashiCorp（开源） | AWS（原生） |
| 云平台支持 | **多云**（AWS/GCP/Azure/+） | **仅 AWS** |
| 语言 | HCL（HashiCorp Configuration Language）| JSON / YAML |
| 状态管理 | 自行管理（本地/远程 .tfstate）| AWS 原生管理 |
| 社区生态 | 极大（Terraform Registry）| 较小 |
| AWS 新服务支持速度 | 稍慢（等社区 provider 更新）| **更快**（AWS 自己维护） |
| 适合场景 | 多云/混合云、团队已用 Terraform | 纯 AWS 环境、AWS 深度集成 |

### 3. 可变基础设施 vs 不可变基础设施

| 维度 | 可变（Mutable Infrastructure） | 不可变（Immutable Infrastructure） |
|------|-------------------------------|----------------------------------|
| 核心思路 | 更新/修改现有服务器 | 销毁旧服务器，用新镜像替换 |
| 配置漂移风险 | ❌ 高（每次变更积累差异） | ✅ 低（每次部署全新实例） |
| 回滚方式 | 复杂（手动反向操作） | 简单（启动旧版本镜像） |
| 代表工具 | Ansible, Chef, Puppet（配置管理）| Packer（构建 AMI）+ Terraform |
| IaC 对应 | 原地修改（in-place update）| 蓝绿部署（Blue-Green）|
| 云原生首选 | ❌ | ✅ |

### 4. TCP vs UDP（简表，重点记忆）

| 场景 | 选 TCP 还是 UDP？ | 原因 |
|------|-----------------|------|
| 网页浏览（HTTP/HTTPS） | TCP | 需要完整、有序的数据 |
| 文件传输（FTP） | TCP | 数据完整性关键 |
| DNS 查询 | UDP | 单次小查询，速度优先 |
| 视频直播 | UDP | 丢包可接受，延迟不可接受 |
| 在线游戏 | UDP | 实时性优先 |
| 邮件（SMTP） | TCP | 不能丢消息 |

### 5. 公有子网 vs 私有子网

| 维度 | 公有子网（Public Subnet） | 私有子网（Private Subnet） |
|------|--------------------------|--------------------------|
| 是否有互联网路由 | ✅ 路由表指向 IGW | ❌ 无直接互联网路由 |
| 实例是否可直接访问互联网 | ✅ 是（有公网 IP）| ❌ 需通过 NAT 网关 |
| 常见部署 | Web 服务器、Load Balancer | 数据库、应用服务器 |
| 安全性 | 较低 | 更高 |

---

## 关键命令与配置

### Terraform CLI

```bash
# 初始化（下载 provider 插件）
terraform init

# 预览变更（不修改任何资源！）
terraform plan

# 应用变更
terraform apply

# 应用前自动审批（CI/CD 中使用）
terraform apply -auto-approve

# 销毁所有资源
terraform destroy

# 查看当前状态
terraform show

# 列出状态中的资源
terraform state list

# 格式化代码
terraform fmt

# 验证配置语法
terraform validate
```

### HCL 语法示例

**配置 Provider**
```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}
```

**定义 Resource**
```hcl
# 语法：resource "<PROVIDER>_<TYPE>" "<本地名称>" { ... }
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name = "WebServer"
    Environment = "production"
  }
}

# 引用其他资源属性
# 语法：<PROVIDER>_<TYPE>.<本地名称>.<属性>
resource "aws_eip" "web_ip" {
  instance = aws_instance.web_server.id   # 引用上面实例的 ID
}
```

**定义 VPC 和子网**
```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
  tags = { Name = "main-vpc" }
}

resource "aws_subnet" "public" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}
```

**定义安全组**
```hcl
resource "aws_security_group" "web" {
  name   = "web-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]   # 允许所有 IP 访问 HTTPS
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"             # 允许所有出站
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

**定义变量（Variables）**
```hcl
# variables.tf
variable "region" {
  type    = string
  default = "us-east-1"
}

variable "allowed_ports" {
  type    = list(number)
  default = [80, 443, 22]
}

variable "instance_config" {
  type = list(object({
    name          = string
    instance_type = string
  }))
  default = [
    { name = "web", instance_type = "t3.micro" },
    { name = "api", instance_type = "t3.small" }
  ]
}

# 使用变量
resource "aws_instance" "example" {
  instance_type = var.instance_type   # 引用语法：var.<NAME>
}
```

**定义 Module（模块）**
```hcl
# 调用子模块
module "vpc" {
  source = "./modules/vpc"      # 模块路径

  cidr_block = "10.0.0.0/16"
  name       = "production-vpc"
}

# 引用模块输出
resource "aws_instance" "web" {
  subnet_id = module.vpc.public_subnet_id
}
```

**AWS 认证配置**
```bash
# 方式1：环境变量（推荐 CI/CD 场景）
export AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
export AWS_DEFAULT_REGION="us-east-1"

# 方式2：配置文件（本地开发）
# $HOME/.aws/credentials
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

**必须加入 .gitignore**
```gitignore
# Terraform
.terraform/          # provider 插件目录（运行 init 自动生成）
*.tfstate            # 状态文件（含资源 ID、可能含敏感信息）
*.tfstate.backup     # 状态备份
.terraform.lock.hcl  # 可选：lock 文件建议提交，确保版本一致
```

---

## ⭐ 易考点

**1. `terraform plan` 不修改任何真实资源**
`plan` 只是预览——它读取当前状态、计算配置与现实的 diff，但不执行任何变更。`apply` 才真正修改资源。`init` 也不修改真实资源（只下载插件）。

**2. `terraform.tfstate` 不能提交到 Git**
State 文件包含资源 ID、IP 地址，可能包含敏感信息（密码、密钥）。团队协作时应使用**远程状态（Remote State）**（如 S3 + DynamoDB 锁定），不能依赖本地 tfstate。

**3. 配置漂移（Configuration Drift）是 IaC 要解决的核心问题**
手动修改运行中的服务器导致实际状态偏离期望状态，时间越长差异越大，最终产生"雪花服务器"，无法重现，难以维护。IaC + 不可变基础设施从根本上消除漂移。

**4. 声明式 IaC 的幂等性**
Terraform 的 `apply` 是幂等的——无论运行多少次，如果配置未改变，结果相同（不会重复创建资源）。这与 bash 脚本（命令式）的"每次运行可能创建重复资源"有本质区别。

**5. 安全组 vs 路由表**
- **安全组（Security Group）**：实例级别，控制哪些**流量**可以进出（防火墙规则）
- **路由表（Route Table）**：子网级别，控制流量**去哪里**（路由规则）
- 两者协同工作：路由表决定"能到达"，安全组决定"能进入"

**6. TCP 一定要记住的协议**
DNS 用 UDP（速度优先，单次小查询），HTTP/HTTPS/SSH 用 TCP（可靠性优先）。考试常见"为什么 DNS 用 UDP"。

---

## 自测题

### 题目一（多选题）

以下关于 Terraform 工作流和核心概念的说法，哪些是**正确的**？

A. `terraform plan` 命令会修改真实的云资源，以便验证配置是否正确  
B. `terraform.tfstate` 文件记录了 Terraform 管理的所有资源的当前状态，不应提交到 Git 仓库  
C. `terraform init` 是幂等的，可以在同一个目录中安全地重复运行  
D. 在 Terraform 中，Module（模块）是一组资源的集合，用于代码复用和组织  
E. Terraform 只支持 AWS，不支持其他云平台  

<details>
<summary>查看答案与解析</summary>

**正确答案：B、C、D**

- **A ❌**：`terraform plan` **不修改**任何真实资源。它只是读取当前状态文件，与配置代码对比，输出将要发生的变更预览（diff）。真正执行变更需要 `terraform apply`。这是 plan 最重要的特性——"安全预览"。

- **B ✅**：`terraform.tfstate` 包含资源的实际 ID、IP 地址等元数据，可能还含有敏感信息（如数据库密码）。必须从 Git 排除（加入 `.gitignore`）。团队协作应使用远程状态（如 AWS S3 + DynamoDB 锁定）。

- **C ✅**：`terraform init` 是幂等的——重复运行不会产生副作用。如果插件已下载，不会重新下载。每当添加新的 provider 或 module 时都需要重新运行 init。

- **D ✅**：Module 是 Terraform 组织代码的方式。每个 Terraform 配置都有一个 root module，可以调用 child modules。模块实现了基础设施代码的封装和复用。

- **E ❌**：Terraform 是**多云工具**，通过 Provider 插件支持 AWS、GCP、Azure、Kubernetes、甚至数据库等几乎所有主流平台。这是 Terraform 相比 AWS CloudFormation 的核心优势。

</details>

---

### 题目二（多选题）

关于基础设施即代码（IaC）解决的问题和核心原则，以下哪些是**正确的**？

A. 配置漂移（Configuration Drift）是指服务器配置随时间逐渐偏离预期基准，通常由热修复、手动变更等造成  
B. "牲畜不是宠物（Cattle, not Pets）"原则认为，关键生产服务器应该像宠物一样被精心维护和命名  
C. 幂等性（Idempotency）意味着无论执行多少次 IaC 配置，最终状态都是相同的期望状态  
D. IaC 中的密码和安全机密（Secrets）应该存放在 Git 仓库中，以便追踪变更历史  
E. IaC 工具通过版本控制实现基础设施变更的可追溯性、可回滚性和自动化触发  

<details>
<summary>查看答案与解析</summary>

**正确答案：A、C、E**

- **A ✅**：配置漂移（Configuration Drift）是 IaC 要解决的核心问题之一。热修复（hotfix）、不同版本的安装包、个人临时配置——这些都会使服务器实际状态逐渐偏离初始配置，最终产生无法重现的"雪花服务器（Snowflake Server）"。

- **B ❌**：说反了。"Cattle, not Pets" 的核心是：服务器应该像**牲畜**一样——编号管理、可批量替换、出问题直接换新实例，而不是像宠物一样需要精心照料。这是不可变基础设施的哲学基础。"宠物"式管理正是传统运维的问题所在。

- **C ✅**：幂等性是 IaC 工具（如 Terraform）的核心属性。第一次 apply 创建资源；之后用相同配置 apply，工具检测到已处于期望状态，不做任何操作。只有配置变更时才执行相应的最小变更。

- **D ❌**：严重错误！密码、API 密钥、证书等机密信息**绝对不能**存入 Git 仓库（即使是私有仓库）。应使用专门的机密管理工具：AWS Secrets Manager、HashiCorp Vault、GCP Secret Manager。机密一旦进入 Git 历史就视为泄露。

- **E ✅**：VCS（版本控制系统）是 IaC 的核心依赖，提供：可追溯性（who changed what when）、回滚能力（git revert）、关联性（基础设施变更与代码变更关联）、自动触发（Git push → CI 运行 terraform plan/apply）。

</details>

---

### 题目三（判断题）

**陈述**：Terraform 中，如果先运行 `terraform plan`，然后再运行 `terraform apply`，`apply` 一定会执行与 `plan` 预览完全相同的变更，因为 `plan` 已经计算好了执行计划。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

虽然 `plan` 计算出了执行计划，但 `apply` 不保证与上次 `plan` 完全一致，原因如下：

**1. 时间差问题**
`plan` 和 `apply` 之间存在时间窗口。在这段时间内，云资源的状态可能已发生变化：
- 其他团队成员手动修改了资源
- 另一个自动化进程修改了配置
- 云提供商的资源状态发生变化（如实例被终止）

**2. `apply` 会重新刷新状态**
`terraform apply` 在执行前会**重新刷新（refresh）**当前状态，与 `plan` 时的状态可能不同。

**3. 正确做法：保存 plan 文件**

如果需要确保 `apply` 执行与 `plan` 预览完全一致的内容，应该：
```bash
# 将 plan 结果保存到文件
terraform plan -out=tfplan

# 执行时指定已保存的计划（不重新计算）
terraform apply tfplan
```

这在 CI/CD 流水线中尤为重要：
1. CI 阶段运行 `terraform plan -out=tfplan`，人工审查输出
2. CD 阶段运行 `terraform apply tfplan`，执行已审查的精确计划

**总结**：直接运行 `terraform apply`（不指定 plan 文件）会重新计算计划，可能与之前 `plan` 的输出有差异。

</details>

---

### 题目四（判断题）

**陈述**：在 AWS VPC 中，安全组（Security Group）和路由表（Route Table）的作用是相同的，都是用来控制网络流量是否能到达某个实例的。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

安全组和路由表**作用层次完全不同**，是互补而非相同的机制：

| 维度 | 安全组（Security Group） | 路由表（Route Table） |
|------|------------------------|---------------------|
| 工作层次 | **实例层（第4层，传输层）** | **子网层（第3层，网络层）** |
| 控制内容 | 哪些流量**可以进入/离开实例** | 流量**应该路由到哪里去** |
| 规则类型 | 协议 + 端口 + 源/目标 IP | 目标 CIDR + 下一跳（Next Hop）|
| 典型规则 | "允许 TCP 443 从 0.0.0.0/0" | "0.0.0.0/0 → igw-xxx（互联网网关）" |
| 应用位置 | 每个 EC2 实例 | 每个子网 |
| 状态性 | **有状态（Stateful）**：允许入站则响应自动允许 | 无状态 |

**形象类比**：
- **路由表** = 道路系统（决定车辆走哪条路）
- **安全组** = 门卫（决定谁能进入建筑物）

**实际工作流程**：
```
互联网请求
    ↓
路由表检查：这个目标 IP 该怎么路由？→ 路由到子网内的实例
    ↓
安全组检查：这个请求的协议/端口/来源允许进入吗？→ 允许/拒绝
    ↓
到达 EC2 实例
```

两者都需要正确配置，流量才能到达实例。路由表允许"路通了"，安全组允许"门能进"，缺一不可。

</details>

---

### 题目五（场景分析题）

**场景**：

TechFlow 是一家拥有 40 名工程师的中型 SaaS 公司，最近遇到了严重的运维问题：

- **问题 1**：生产环境有 80 台 EC2 实例，每台都是手动配置的。上个月有工程师为了临时修复 Bug 直接 SSH 登录生产服务器修改了 nginx 配置，导致这台服务器的配置与其他服务器不一致，现在没人记得改了什么。
- **问题 2**：在 Dev 环境能跑的代码，部署到 Prod 时总是出问题，排查后发现两个环境的 Python 版本和依赖包版本不一致。
- **问题 3**：上周一台核心数据库服务器故障，团队花了 6 小时才恢复，因为没有文档记录这台服务器是如何配置的。

CTO Lisa 决定引入 IaC，任命 DevOps 工程师 Carlos 负责实施方案。

请回答：
1. 上述三个问题分别对应 IaC 理论中的哪个概念？
2. Carlos 应该如何用 Terraform 解决这些问题？请描述核心方案。
3. 实施 Terraform 后，Carlos 应该把哪些文件提交到 Git，哪些不能提交？

<details>
<summary>查看参考答案</summary>

**参考答案：**

---

**第一部分：问题诊断**

| 问题描述 | 对应 IaC 概念 | 解释 |
|----------|-------------|------|
| 工程师直接 SSH 修改 nginx 配置，导致服务器间不一致 | **配置漂移（Configuration Drift）** | 手动变更使服务器实际状态偏离期望配置，时间越长差异越大 |
| Dev/Prod 环境 Python 版本和依赖不一致 | **环境不一致（Inconsistent Environment）** / 违反"Consistent"原则 | 未将环境配置代码化，两套环境各自独立演化 |
| 核心服务器故障后无法快速重建（缺少配置文档） | **雪花服务器（Snowflake Server）** | 服务器是独一无二的"宠物"，没有文档，无法重现，极度脆弱 |

---

**第二部分：Terraform 解决方案**

**问题1（配置漂移）→ 不可变基础设施 + IaC**
```hcl
# 所有 nginx 配置通过 Terraform + User Data 定义，禁止手动 SSH 修改
resource "aws_instance" "web" {
  ami           = var.ami_id          # 预烘焙的 AMI，含正确 nginx 配置
  instance_type = "t3.micro"
  user_data     = file("scripts/nginx_setup.sh")  # 启动时自动配置

  tags = { Name = "web-${count.index}" }
  count = 80
}
```
- 推行 "Cattle, not Pets" 原则：服务器出问题直接销毁重建（`terraform apply`），而非手动修复
- 通过 IaC 的不可变性消除配置漂移：任何变更都走代码 → Git → `terraform apply`，而非 SSH

**问题2（环境不一致）→ 相同代码，不同变量**
```hcl
# 用同一套 Terraform 代码，通过变量区分环境
variable "environment" {
  type = string   # "dev" / "staging" / "prod"
}

variable "python_version" {
  type    = string
  default = "3.11"   # 统一 Python 版本
}

# Dev 环境：terraform apply -var="environment=dev"
# Prod 环境：terraform apply -var="environment=prod"
```
- 相同的 `main.tf` 代码，通过 `terraform workspace` 或变量切换环境
- 确保 Dev/Staging/Prod 使用**相同的 AMI、相同的依赖版本**
- 对应 IaC 原则：Consistent（一致）+ 12-Factor Factor X（Dev/Prod Parity）

**问题3（雪花服务器）→ 全面代码化，秒级重建**
```hcl
# 数据库实例完全由 Terraform 定义
resource "aws_db_instance" "core_db" {
  engine         = "mysql"
  engine_version = "8.0"
  instance_class = "db.t3.medium"
  db_name        = "coredb"
  # ... 所有配置都在代码里

  backup_retention_period = 7   # 自动备份
  multi_az                = true  # 多AZ高可用
}
```
- 数据库配置完全代码化：`terraform apply` 可以从零重建整个基础设施
- 故障恢复从"6小时找文档手动配置" → "几分钟 terraform apply"
- 体现 IaC 目标：**易故障恢复（Easy Recovery）**

---

**第三部分：Git 管理规范**

**✅ 应该提交到 Git**：
```
terraform/
├── main.tf           # 资源定义
├── variables.tf      # 变量声明
├── outputs.tf        # 输出定义
├── modules/          # 可复用模块
│   ├── vpc/
│   └── ec2/
├── environments/
│   ├── dev.tfvars    # Dev 环境变量值
│   └── prod.tfvars   # Prod 环境变量值
└── .terraform.lock.hcl  # provider 版本锁定（建议提交）
```

**❌ 绝对不能提交到 Git（加入 .gitignore）**：
```gitignore
.terraform/          # provider 插件（体积大，init 重新下载）
*.tfstate            # 状态文件（含敏感信息）
*.tfstate.backup     # 状态备份
*.tfvars             # 如含密码等敏感变量值（视情况）
```

**团队协作补充建议**：
- 使用 **S3 + DynamoDB** 存储远程状态，支持状态加锁，避免多人同时 apply
- 所有变更通过 **PR + terraform plan 审查**，而非直接 apply
- 敏感信息（DB 密码、API Key）存入 **AWS Secrets Manager**，通过 `data "aws_secretsmanager_secret"` 在运行时读取，而不是写在代码中

</details>
