# L05 — 计算机网络基础 / 基础设施即代码 / Terraform
> 来源：L05_01 Fundamentals of Computer Networking · L05_02 Infrastructure as Code (IaC) · L05_03 Terraform
> CSYE 6225，Tejas Parikh，东北大学

---

## 第一部分：计算机网络基础（L05_01）

---

### 1. 什么是网络（What is a Network）

- 网络是通过某种通信链路连接在一起的一组系统。
- 常见网络类型：
  - **LAN（Local Area Network，局域网）**：局限于本地地理范围。
  - **WAN（Wide Area Network，广域网）**：跨越更大地理范围；通常由多个 LAN 组成。
- 系统之间不是直接相连，而是通过**交换机（switch）或路由器（router）**间接相连。

---

### 2. 通信链路（Communication Link）

- 系统之间通过通信链路连接，例如：同轴电缆、铜缆、光纤或无线电波。
- 不同通信链路的数据传输速率不同。
- 数据传输速率单位：**bits/second（比特每秒）**。
- 注意：使用 bits/s，不是 bytes/s。**1 byte = 8 bits**。

---

### 3. 带宽（Bandwidth）

- **定义**：带宽是给定路径上数据传输的最大速率（maximum rate of data transfer across a given path）。

---

### 4. 网络协议（Network Protocols）

- **定义**：协议定义了两个或多个通信实体之间交换的消息的**格式和顺序**，以及在消息传输/接收或其他事件发生时采取的**动作**。

---

### 5. 网络协议栈与 OSI 参考模型

#### 五层互联网协议栈（Five-layer Internet Protocol Stack）

| 层级 | 名称 |
|------|------|
| 5 | Application（应用层）|
| 4 | Transport（传输层）|
| 3 | Network（网络层）|
| 2 | Link（链路层）|
| 1 | Physical（物理层）|

#### 七层 OSI 参考模型（Seven-layer ISO OSI Reference Model）

| 层级 | 名称 | 交换单元 |
|------|------|------|
| 7 | Application（应用层）| APDU |
| 6 | Presentation（表示层）| PPDU |
| 5 | Session（会话层）| SPDU |
| 4 | Transport（传输层）| TPDU |
| 3 | Network（网络层）| Packet（数据包）|
| 2 | Data link（数据链路层）| Frame（帧）|
| 1 | Physical（物理层）| Bit（比特）|

> 图示：互联网协议栈（5层）与 OSI 参考模型（7层）的对比。路由器仅包含 Physical、Data link、Network 三层；主机包含全部层级。

---

### 6. 各层详解

#### 应用层（Application Layer）

- 网络应用和应用层协议所在的层。
- 协议示例：**HTTP、FTP** 等。

#### 表示层与会话层（Presentation & Session Layer）

- **会话层（Session Layer）**：处理特定用户会话相关的数据。
- **表示层（Presentation Layer）**：处理数据的渲染/呈现。
- 表示层协议示例：音视频编解码器、不同图像格式（PNG、JPEG 等）。

#### 传输层（Transport Layer）

- 负责在应用的客户端和服务器端之间传输应用层消息。
- 常见传输层协议：**TCP（Transmission Control Protocol）** 和 **UDP（User Datagram Protocol）**。

#### 网络层（Network Layer）

- 负责将传输层协议从一台主机移动到另一台主机。
- 定义数据包（datagram）中的字段，以及终端系统和路由器如何处理这些字段。
- 若数据包过大，网络层可将其拆分为多个包，在接收节点重新组合。
- 最常见的网络层协议：**IP（Internet Protocol）**。

#### 链路层（Link Layer）

- 负责将数据包从路由中的一个节点移动到下一个节点。

#### 物理层（Physical Layer）

- 链路层移动的是数据包，物理层的工作是移动**单个比特（individual bits）**。

---

### 7. TCP（传输控制协议）

| 特性 | 说明 |
|------|------|
| 可靠数据传输 | 确保数据正确、有序地从发送进程传到接收进程 |
| 基于连接 | 发送方保持与接收方的连接，直到所有数据包成功发送完毕 |
| 错误检测 | 进行错误检查，有错误的包将从源头重传 |
| 拥塞控制 | 不发送下一块数据，直到收到接收方的 ACK（确认）|
| 常用协议 | HTTP、FTP、SMTP 等应用层协议使用 TCP/IP |

---

### 8. UDP（用户数据报协议）

| 特性 | 说明 |
|------|------|
| 设计理念 | 尽可能少做工作来发送数据 |
| 无拥塞控制 | 数据立即发送 |
| 无可靠性保证 | 不关心消息是否成功接收 |
| 无连接建立开销 | 没有连接建立的额外开销 |
| 无连接状态维护 | 不跟踪数据的成功传输或发送顺序 |
| 常用协议 | DNS（域名系统）、DHCP（动态主机配置协议）|

> **TCP vs UDP 类比**：TCP 就像用杯子喝水（受控、有序）；UDP 就像把水瓶直接倒到脸上（无序、直接）。

---

### 9. IP（互联网协议）

- **定义**：IP 是互联网协议套件中的主要通信协议，用于跨网络边界转发数据报（datagram）。
- IP 是**尽力交付（best-effort delivery）**服务：不保证数据正确性、顺序或避免重复数据（类似 UDP）。
- **主要版本**：
  - **IPv4**：第一个主要版本。
  - **IPv6**：IPv4 的继任版本。

#### TCP/IP 参考模型与 OSI 对比

| OSI 层 | TCP/IP 层 | 备注 |
|--------|-----------|------|
| Application (7) | Application | |
| Presentation (6) | （不存在）| Not present in TCP/IP model |
| Session (5) | （不存在）| Not present in TCP/IP model |
| Transport (4) | Transport | |
| Network (3) | Internet | |
| Data link (2) | Link | |
| Physical (1) | （不存在）| Not present in TCP/IP model |

---

### 10. IPv4

- 使用 **32 位**地址系统。
- 地址空间：**2³² ≈ 40 亿**个 IP 地址。
- 网络中每个系统必须有**全球唯一**的 IP 地址（*有例外，见 NAT）。

#### IPv4 地址表示（点分十进制）

- 格式：四个十进制数，以点（.）分隔。
- 示例：`172.16.254.1` → 二进制 `10101100.00010000.11111110.00000001`
- 每个字节（byte）= 8 位（bits）；总计 32 位（4 × 8）。

#### IPv4 地址耗尽

- **IPv4 地址已于 2011 年耗尽**。
- 并非所有 40 亿地址都可用：
  - 约 **~1800 万**地址分配给私有网络。
  - 约 **~2.7 亿**地址用于组播（multicast）地址。

---

### 11. IPv6

- IPv4 的最新继任版本。
- 使用 **128 位**地址系统，理论上允许 **2¹²⁸** 个地址。
- **IPv6 不支持 NAT**。

#### IPv6 地址表示

- 格式：8 组，每组 4 位十六进制数，组间用冒号（:）分隔。
- 示例：`2001:0db8:0000:0042:0000:8a2e:0370:7334`
- 全零组可省略，例如：`2001:0DB8:AC10:FE01:0000:0000:0000:0000` → `2001:0DB8:AC10:FE01::`

---

### 12. 子网（Subnet）

- **定义**：子网是一个隔离的网络。
- IP 地址通过**子网掩码（subnet mask）**分配给子网。
- 子网通常用 **CIDR 表示法**：`<网络地址>/<前缀位数>`。
  - 示例：`192.168.1.0/24`
    - 24 位分配给网络前缀。
    - 剩余 8 位用于主机寻址（即该子网最多支持 2⁸ = 256 个地址）。

---

### 13. NAT（网络地址转换）

- **功能**：允许整个私有网络共享 NAT 网关的一个互联网可路由 IP 地址。
- **作用**：使 ISP（互联网服务提供商）能够用有限的公网 IP 支持更多系统。
- IoT 的发展显著增加了连接互联网的设备数量。
- IPv6 的普及进展缓慢，旧设备/系统/应用不支持。
- **NAT 的缺点**：与 P2P（点对点）路由配合不好，P2P 通常要求两台主机能直接通信。

#### NAT 表示例（图示描述）

```
内部网络（Intranet）                外部网络（Internet）
                    NAT 网关
192.168.1.3:23549 → 193.19.219.216:80
                ↕ 转换 ↕
82.25.123.27:23549 → 193.19.219.216:80
```
- NAT 网关将内部私有 IP 映射到一个公网 IP，对外部网络隐藏内部结构。

---

### 14. Virtual Private Cloud（VPC，虚拟私有云）

#### VPC 基本概念

- **定义**：VPC 允许您将资源启动到您自定义的虚拟网络中。
- 一个 VPC 是属于您的虚拟网络，与其他用户的虚拟网络**逻辑隔离**。
- AWS 和 GCP 上的**默认 VPC 都包含互联网网关**。
- **互联网网关（Internet Gateway）**使您的实例能够连接到互联网。

#### VPC 可配置项

- 防火墙（firewall）
- 路由表（routing table）
- 公有或私有子网（public or private subnets）
- 网络网关（network gateway）

---

### 15. 弹性网络接口（Elastic Network Interfaces）

**定义**：弹性网络接口是一种虚拟网络接口，可以包含以下属性：

1. 一个主私有 IPv4 地址（primary private IPv4 address）
2. 一个或多个辅助私有 IPv4 地址
3. 每个私有 IPv4 地址对应一个弹性 IP 地址
4. 一个公有 IPv4 地址（启动实例时可自动分配给 eth0）
5. 一个或多个 IPv6 地址
6. 一个或多个安全组
7. 一个 MAC 地址源/目标检查标志
8. 一个描述

#### 弹性网络接口的关键行为

- 可以创建、附加到实例、从实例分离，再附加到另一个实例。
- 网络接口的属性跟随接口本身，不随实例绑定。
- 将网络接口从一个实例移到另一个实例时，网络流量重定向到新实例。
- 每个 VPC 中的实例都有一个**默认网络接口（主网络接口）**，分配了来自 VPC IPv4 地址范围的私有 IPv4 地址。
- **不能将主网络接口从实例上分离**。
- 可以创建并附加额外的网络接口；可附加数量取决于实例类型。

---

### 16. 路由表（Route Tables）

- **定义**：路由表包含一组规则（称为"路由"），用于确定网络流量的走向。
- VPC 中每个子网必须与一个路由表关联；路由表控制该子网的路由。
- **一个子网同时只能关联一个路由表**，但多个子网可以关联同一个路由表。

#### 路由表示例

| Destination（目标）| Target（指向）|
|------|------|
| 10.0.0.0/16 | Local（VPC 内部路由）|
| 172.31.0.0/16 | pcx-1a2b1a2b（VPC 对等连接）|
| 0.0.0.0/0 | igw-11aa22bb（互联网网关）|

**路由规则说明**：
- 流量目标为 `172.31.0.0/16` → 走 VPC 对等连接（更具体的路由优先）。
- 流量目标为 VPC 内部（`10.0.0.0/16`）→ 走 Local 路由（VPC 内路由）。
- 其余所有流量 → 走互联网网关。

---

### 17. 互联网网关（Internet Gateways）

- **定义**：互联网网关是一个水平扩展、冗余、高可用的 VPC 组件，允许 VPC 中的实例与互联网通信。
- 不施加可用性风险或带宽限制。
- **两个目的**：
  1. 为 VPC 路由表中的互联网可路由流量提供目标。
  2. 为分配了公有 IPv4 地址的实例执行 NAT。
- 支持 **IPv4 和 IPv6** 流量。

---

### 18. 弹性 IP 地址（Elastic IP Addresses）

- **定义**：弹性 IP 地址是为动态云计算设计的**静态公有 IPv4 地址**。
- 可以与账户中任何 VPC 中的任何实例或网络接口关联。
- 通过快速重新映射地址到另一个实例，可以**掩盖实例故障**。
- **AWS 不支持 IPv6 的弹性 IP 地址**。

---

### 19. 安全组（Security Groups）

- **定义**：安全组充当实例的**虚拟防火墙**，控制入站和出站流量。
- 启动 VPC 中的实例时，最多可以分配 **5 个安全组**。
- 安全组**作用在实例级别**，不是子网级别。
  - 同一子网中的不同实例可以分配不同的安全组。
  - 启动时如未指定安全组，实例自动分配 VPC 的默认安全组。
- 每个安全组中，可以添加规则控制**入站流量**，以及单独的规则控制**出站流量**。

#### 安全组规则示例表

**入站（Inbound）规则：**

| Source | Protocol | Port Range | Comments |
|--------|----------|------------|----------|
| 0.0.0.0/0 | TCP | 80 | 允许所有 IPv4 地址的 HTTP 入站 |
| ::/0 | TCP | 80 | 允许所有 IPv6 地址的 HTTP 入站 |
| 0.0.0.0/0 | TCP | 443 | 允许所有 IPv4 地址的 HTTPS 入站 |
| ::/0 | TCP | 443 | 允许所有 IPv6 地址的 HTTPS 入站 |
| 您网络的公有 IPv4 范围 | TCP | 22 | 允许通过互联网网关从您网络对 Linux 实例的 SSH 入站 |
| 您网络的公有 IPv4 范围 | TCP | 3389 | 允许通过互联网网关从您网络对 Windows 实例的 RDP 入站 |

**出站（Outbound）规则：**

| Destination | Protocol | Port Range | Comments |
|-------------|----------|------------|----------|
| 数据库服务器安全组 ID | TCP | 1433 | 允许到指定安全组实例的 Microsoft SQL Server 出站 |
| MySQL 数据库安全组 ID | TCP | 3306 | 允许到指定安全组实例的 MySQL 出站 |

---

### 20. VPC 公有与私有子网架构（图示描述）

**典型架构**（AWS VPC，单可用区示例）：

```
AWS Region
└── VPC（10.0.0.0/16）
    ├── 公有子网（Public Subnet，10.0.0.0/24）
    │   ├── Web 服务器（弹性 IP：198.51.100.1/2/3）
    │   ├── NAT 网关（弹性 IP：198.51.100.4）
    │   └── 自定义路由表：
    │       10.0.0.0/16 → local
    │       0.0.0.0/0   → igw-id（互联网网关）
    │
    └── 私有子网（Private Subnet，10.0.1.0/24）
        ├── 数据库服务器（10.0.1.5/6/7）
        └── 主路由表：
            10.0.0.0/16 → local
            0.0.0.0/0   → nat-gateway-id（NAT 网关）
```

- 公有子网通过互联网网关直接访问互联网。
- 私有子网通过 NAT 网关访问互联网（出站），但互联网无法直接访问私有子网（入站被阻断）。

---

## 第二部分：基础设施即代码（L05_02 — IaC）

---

### 1. 学习目标

- 解释 IaC 是什么以及为何存在。
- 识别在动态云环境中手动管理基础设施时出现的问题。
- 描述 IaC 的核心原则：可再现性、可丢弃性、一致性、可重复性和持续变更。
- 区分管理服务器的"宠物 vs 牲畜"思维方式。
- 定义 IaC 关键概念：栈（stack）和环境（environment）。
- 阐述版本控制系统如何支持基础设施管理。

---

### 2. 为什么需要 IaC

- 云计算和自动化的承诺：快速提供资源。
- **现实情况**：云和自动化常常使情况更糟（如果没有好的实践）。
- **服务器蔓延（Server Sprawl）**：快速配置导致无法管理的增长。
- 工具采用与有效实践采用之间的差距。
- 现代组织中的变更管理挑战。

---

### 3. IaC 的定义

> **Infrastructure as Code（IaC）**：基于软件开发实践的基础设施自动化。

- 强调对系统配置和变更采用**一致、可重复的流程**。
- 将基础设施视为**软件和数据**来对待。
- 连接 IaC 与软件开发工具和实践：
  - VCS（版本控制系统）
  - 自动化测试
  - TDD（测试驱动开发）
  - CI/CD（持续集成/持续交付）

---

### 4. IaC 的目标

- 基础设施支持并**促进变更**。
- **日常变更不造成混乱或压力**。
- 自助式资源配置（Self-service resource provisioning）。
- 易于从故障中恢复。
- 持续改进，而非大爆炸式项目。
- 通过实施和测量来验证解决方案。

---

### 5. 动态基础设施的挑战

#### 服务器蔓延（Server Sprawl）
- 快速配置导致无法管理的增长。
- 大规模补丁、更新和漏洞管理。

#### 配置漂移（Configuration Drift）
- 即使是一致创建的服务器也会随时间发生偏差。
- 现实场景：热修复、版本不匹配、个人配置。
- 受管理的变化 vs 非受管理的变化。

#### 雪花服务器（Snowflake Servers）
- **定义**：独一无二、无法可靠再现的服务器。
- 为什么危险：难以恢复、难以调试。
- **可再现性测试**：你现在能重建这台服务器吗？

#### 脆弱基础设施（Fragile Infrastructure）
- 雪花问题扩展到整个基础设施组合层面。
- 向可再现基础设施迁移的路径。

---

### 6. IaC 的核心原则

| 原则 | 说明 |
|------|------|
| **可再现（Reproducible）** | 可以轻松可靠地重建任何基础设施元素；所有决策记录在脚本和工具中 |
| **可丢弃（Disposable）** | 为持续变化而设计；软件必须容忍基础设施的出现和消失 |
| **牲畜不是宠物（Cattle, not Pets）** | 服务器是可替换的牲畜，而非珍贵的宠物（见下表）|
| **一致（Consistent）** | 集群内的基础设施元素近乎相同；一致性使自动化值得信赖 |
| **可重复（Repeatable）** | 脚本文化：能脚本化的就脚本化；脚本和配置管理优先于手动变更 |
| **设计持续变化（Design Always Changing）** | 云时代：简单设计 + 频繁变更；频繁变更促成良好习惯和高效流程 |

#### 宠物 vs 牲畜（Pets vs. Cattle）

| | 宠物（Pets）| 牲畜（Cattle）|
|--|------------|--------------|
| 类比 | 手工维护的服务器，命名独特，出故障要修复 | 可替换的服务器，编号管理，出故障直接替换 |
| 时代 | 铁器时代（可靠硬件 + 不可靠软件）| 云时代（可靠软件 + 不可靠硬件）|
| 处理故障 | 尽力修复 | 直接销毁并重建 |

---

### 7. 实践 IaC

#### 栈（Stack）

- **定义**：作为一个单元来定义和变更的基础设施元素集合。
- 栈规模：从单台服务器到整个数据中心。
- 可以跨网络边界组织栈。

#### 环境（Environment）

- 栈的实例，例如：开发（development）、测试（test）、预生产（preproduction）、生产（production）。
- 使用 IaC 保持各环境的一致性。

#### VCS（版本控制）在 IaC 中的作用

- VCS 是 IaC 体制的核心。
- 提供：可追溯性（traceability）、回滚（rollback）、关联（correlation）、可见性（visibility）、自动触发（automated triggers）。

**应该放入 VCS 的内容：**
- 配置定义（cookbooks、manifests、playbooks）
- 配置文件、模板、测试代码
- CI/CD 任务定义、实用脚本、文档

**不应该放入 VCS 的内容：**
- 软件构件（使用制品库，artifact repositories）
- 可从源码重建的构建产物
- 应用数据、日志和备份
- 密码和安全机密（使用机密管理工具，secrets management tools）

---

### 8. IaC 关键术语表

| 术语 | 定义 |
|------|------|
| **Infrastructure as Code (IaC)** | 通过机器可读的定义文件来管理和配置基础设施，而非手动流程 |
| **Configuration Drift（配置漂移）** | 基础设施配置逐渐偏离其预期或原始状态 |
| **Snowflake Server（雪花服务器）** | 独一无二、无法可靠再现的服务器 |
| **Fragile Infrastructure（脆弱基础设施）** | 容易中断且难以修复的基础设施组合 |
| **Stack（栈）** | 作为单一单元来定义和管理的基础设施元素集合 |
| **Environment（环境）** | 栈的一个实例，如开发、暂存或生产 |
| **Idempotency（幂等性）** | 多次应用相同操作产生相同结果的属性 |

---

## 第三部分：Terraform（L05_03）

---

### 1. 安装 Terraform

- Terraform 以**单一二进制文件（single binary）**形式发布。
- 安装方法：解压并移动到系统 PATH 目录中。

---

### 2. 配置 AWS 环境

- 为使 Terraform 能修改 AWS 账户，需要为 IAM 用户设置 AWS 凭证作为环境变量：

```bash
$ export AWS_ACCESS_KEY_ID=(your access key id)
$ export AWS_SECRET_ACCESS_KEY=(your secret access key)
```

- 除环境变量外，Terraform 还支持与所有 AWS CLI 和 SDK 工具相同的身份验证机制。
- 也可以使用 `$HOME/.aws/credentials` 中的凭证。

---

### 3. 编写 Terraform 文件

- Terraform 代码使用 **HashiCorp Configuration Language（HCL）** 编写。
- 文件扩展名为 `.tf`。
- 可以在几乎任何文本编辑器中编写 Terraform 代码。

---

### 4. Provider（提供者）

- 使用 Terraform 的**第一步**是配置要使用的 provider。

```hcl
provider "aws" {
  region = "us-east-2"
}
```

- 常见 provider：AWS provider、Azure provider、GCP provider 等。

---

### 5. 初始化工作目录（terraform init）

- Terraform 二进制文件包含基本功能，但**不包含任何 provider 的代码**。
- 首次使用时，需要运行 `terraform init`：
  - 扫描代码，找出正在使用的 provider。
  - 下载相应的 provider 代码。
- Provider 代码默认下载到 `.terraform` 文件夹（Terraform 的临时目录）。
  - 建议将 `.terraform` 添加到 `.gitignore`。
- **任何时候启动新 Terraform 代码都需要运行 init**。
- **`init` 命令是幂等的（idempotent）**，可以安全地多次运行。

---

### 6. 创建执行计划（terraform plan）

- `terraform plan` 命令用于创建执行计划。
- Terraform 执行刷新（refresh，除非明确禁用），然后确定达到配置文件中所需状态需要采取哪些操作。
- **不对真实资源或状态进行任何更改**；是一种检查变更是否符合预期的方便方式。
- 用途示例：在提交变更到版本控制之前运行，确认其行为符合预期。

---

### 7. 应用变更（terraform apply）

- `terraform apply` 命令用于应用变更，以达到配置文件描述的所需状态。
- 也可以应用由 `terraform plan` 生成的预定义操作集合。

> **注意**：只运行 `plan` 而不运行 `apply`，就像只是制定计划但从不执行一样（幻灯片用电影梗图说明"只 plan 不 apply"的代价）。

---

### 8. Terraform 状态（Terraform State）

- Terraform 必须存储关于受管基础设施和配置的**状态（state）**。
- **状态的用途**：
  - 将现实世界的资源映射到配置。
  - 跟踪元数据（metadata）。
  - 提升大型基础设施的性能。
- **默认存储位置**：本地文件 `terraform.tfstate`。
  - 也可以远程存储（适合团队协作）。
- Terraform 使用本地状态来创建计划并更改基础设施。
- **每次操作前，Terraform 会刷新（refresh）状态**以与真实基础设施同步。
- 每次运行 Terraform，它可以从 AWS 获取资源的最新状态，与配置对比，确定需要应用哪些变更。
- `plan` 命令的输出是本地代码与真实基础设施（通过状态文件中的 ID 发现）之间的 **diff（差异）**。

---

### 9. 销毁基础设施（terraform destroy）

- `terraform destroy` 命令用于销毁 Terraform 管理的基础设施。

---

### 10. 配置 .gitignore

应忽略以下文件/目录：

```gitignore
.terraform
*.tfstate
*.tfstate.backup
```

- `.terraform`：Terraform 使用的临时目录。
- `*.tfstate`：Terraform 用于存储状态的文件。
- `*.tfstate.backup`：状态备份文件。

---

### 11. 资源与模块（Resources & Modules）

#### 资源（Resource）

- Terraform 语言的**主要目的**是声明资源（resources）。
- 其他所有语言特性都只是为了让资源定义更灵活、方便。
- **资源**描述单个基础设施对象（如一台 EC2 实例）。

**资源声明语法：**

```hcl
resource "<PROVIDER>_<TYPE>" "<NAME>" {
  [CONFIG ...]
}
```

- `<PROVIDER>`：provider 名称（如 `aws`）。
- `<TYPE>`：资源类型（如 `instance`）。
- `<NAME>`：资源的本地名称（在当前模块内唯一）。
- `[CONFIG ...]`：资源的配置参数。

#### 模块（Module）

- 一组资源可以聚合成一个**模块（module）**，构成更大的配置单元。
- **模块**可以描述一组对象及其之间的必要关系，以创建更高层次的系统。
- Terraform 配置由一个**根模块（root module）**组成（从这里开始评估），以及当一个模块调用另一个模块时创建的**子模块树（tree of child modules）**。

---

### 12. 引用资源（Referencing Resources）

**语法：**

```
<PROVIDER>_<TYPE>.<NAME>.<ATTRIBUTE>
```

- 示例：`aws_security_group.instance.id` 引用名为 `instance` 的 AWS 安全组的 `id` 属性。

---

### 13. 变量（Variables）

#### 声明输入变量（Declaring an Input Variable）

每个模块接受的输入变量必须使用 `variable` 块声明：

```hcl
variable "image_id" {
  type = string
}

variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
}

variable "docker_ports" {
  type = list(object({
    internal = number
    external = number
    protocol = string
  }))
  default = [
    {
      internal = 8300
      external = 8300
      protocol = "tcp"
    }
  ]
}
```

- `variable` 关键字后的标签是变量名，在同一模块内必须唯一。
- 该名称用于从外部为变量赋值，以及在模块内部引用变量值。

#### 使用输入变量值（Using Input Variable Values）

在声明了变量的模块内，可以通过 `var.<NAME>` 表达式访问其值：

```hcl
resource "aws_instance" "example" {
  instance_type = "t2.micro"
  ami           = var.image_id
}
```

- 变量的值只能在声明它的模块内的表达式中访问。

---

### 14. 变量类型约束（Variable Type Constraints）

- `variable` 块中的 `type` 参数允许限制变量可接受的值类型。
- 若不设置类型约束，则接受任何类型的值。
- **建议始终指定类型约束**：
  - 作为模块使用者的提醒。
  - 使用错误类型时，Terraform 能返回有用的错误信息。

---

### 15. Terraform 核心命令速查表

| 命令 | 作用 |
|------|------|
| `terraform init` | 初始化工作目录，下载 provider 代码（幂等）|
| `terraform plan` | 创建执行计划，查看将要进行的变更（不实际执行）|
| `terraform apply` | 应用变更，实际创建/修改/删除基础设施资源 |
| `terraform destroy` | 销毁所有 Terraform 管理的基础设施 |

---

## 考试重点速查

### 网络部分（高频考点）

| 考点 | 关键记忆 |
|------|---------|
| TCP vs UDP | TCP：可靠、有序、有连接、有拥塞控制；UDP：不可靠、无序、无连接、无拥塞控制 |
| TCP 常用协议 | HTTP、FTP、SMTP |
| UDP 常用协议 | DNS、DHCP |
| IPv4 | 32位，2³² ≈ 40亿，2011年耗尽 |
| IPv6 | 128位，2¹²⁸，不支持 NAT |
| CIDR `/24` | 24位网络前缀，8位主机，2⁸=256个地址 |
| NAT | 共享一个公网 IP，隐藏内部私有网络；与 P2P 不兼容 |
| VPC | 虚拟私有云，逻辑隔离；包含子网、路由表、安全组、网关 |
| 安全组 | 实例级别的虚拟防火墙；有状态；最多5个/实例 |
| 弹性 IP | 静态公有 IPv4；不支持 IPv6 |
| 互联网网关 | 两用途：路由表目标 + 执行 NAT；支持 IPv4 和 IPv6 |
| OSI vs TCP/IP | OSI 7层；TCP/IP 4层（无 Presentation 和 Session 层）|
| 1 byte | 8 bits |

### IaC 部分（高频考点）

| 考点 | 关键记忆 |
|------|---------|
| IaC 定义 | 通过机器可读定义文件管理基础设施，非手动 |
| 配置漂移 | 服务器随时间偏离预期配置 |
| 雪花服务器 | 独一无二，无法再现 → 危险 |
| 幂等性 | 同一操作多次执行结果相同 |
| 宠物 vs 牲畜 | 宠物：修复；牲畜：销毁重建 |
| Stack | 作为一个单元管理的基础设施集合 |
| Environment | Stack 的实例（dev/staging/prod）|
| VCS 不存储的内容 | 密码/机密、应用数据、日志、构建产物 |

### Terraform 部分（高频考点）

| 考点 | 关键记忆 |
|------|---------|
| 语言 | HCL（HashiCorp Configuration Language），文件扩展名 `.tf` |
| terraform init | 下载 provider；幂等；生成 `.terraform` 目录 |
| terraform plan | 预览变更，不实际执行 |
| terraform apply | 实际执行变更 |
| terraform destroy | 销毁所有受管资源 |
| 状态文件 | `terraform.tfstate`；映射真实资源到配置 |
| .gitignore | `.terraform`、`*.tfstate`、`*.tfstate.backup` |
| 资源语法 | `resource "<PROVIDER>_<TYPE>" "<NAME>" { ... }` |
| 引用语法 | `<PROVIDER>_<TYPE>.<NAME>.<ATTRIBUTE>` |
| 变量引用 | `var.<NAME>` |
| 模块 | root module + child modules 树 |
| AWS 凭证 | `AWS_ACCESS_KEY_ID` + `AWS_SECRET_ACCESS_KEY` 环境变量 |
| Provider 配置 | `provider "aws" { region = "us-east-2" }` |
