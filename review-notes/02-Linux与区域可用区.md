# L03 — Linux CLI 与 云区域/可用区

## 模块总览

本模块覆盖两大主题：**Linux 命令行操作**（云基础设施的核心操作系统技能）和**云区域与可用区架构**（高可用设计的地理基础）。

---

## 核心概念

### 一、Linux 基础

#### 1. 什么是 Linux？
Linux 严格来说是操作系统的**内核（Kernel）**，而非完整操作系统。
内核运行在"ring zero"（内核空间），负责协调硬件与软件：管理硬件设备、进程、用户权限和文件系统。

> **关键统计**：超过 90% 的公有云（Public Cloud）工作负载运行在 Linux 上。

#### 2. Linux 为何主导云和服务器环境？
- **开源（Open Source）**：免费使用、修改、分发，无许可证成本
- **稳定性**：服务器可连续运行数年无需重启
- **资源效率**：轻量级，适合最小化硬件配置
- **安全模型**：强大的权限系统（Permissions System），快速安全补丁
- **灵活性**：可针对特定工作负载高度配置

#### 3. 操作系统组件
| 组件 | 作用 |
|------|------|
| 引导程序（Bootloader） | 管理启动过程 |
| 内核（Kernel） | 核心；管理 CPU、内存、外设 |
| 守护进程（Daemons） | 后台服务（Web 服务器、数据库等） |
| Shell | 命令行解释器（Command Line Interpreter） |
| 桌面环境（Desktop Environment） | 云服务器通常为无头（Headless，无 GUI）模式 |

#### 4. Shell 类型
| Shell | 全称 | 说明 |
|-------|------|------|
| bash | Bourne-Again Shell | **大多数 Linux 发行版和云实例的默认 shell** |
| zsh | Z Shell | 现代特性，macOS 默认 |
| sh | Bourne Shell | 最原始版本 |

> 本课程默认使用 bash，除非另有说明。

#### 5. Bash 特殊字符
| 字符 | 含义 |
|------|------|
| `~` | 当前用户主目录（$HOME） |
| `$` | 访问变量（如 $HOME） |
| `&` | 后台运行命令 |
| `*` | 通配符，匹配零个或多个字符 |
| `?` | 通配符，匹配恰好一个字符 |
| `#` | 注释符 |
| `\` | 转义字符 |

---

### 二、Linux 文件系统

#### 核心哲学："一切皆文件（Everything is a File）"
硬盘驱动器是 `/dev` 中的文件，运行中的进程信息是 `/proc` 中的文件。统一性使得同一套工具（cat、grep）几乎可以检查任何东西。

#### 重要目录
| 目录 | 用途 | 备注 |
|------|------|------|
| `/etc` | 系统配置文件 | "Everything To Configure" |
| `/home` | 用户主目录 | 个人文件、应用代码常部署于此 |
| `/var` | 可变数据 | 日志（`/var/log`）、数据库、队列文件 |
| `/tmp` | 临时文件 | 普通用户也可写入的少数目录之一 |
| `/opt` | 可选软件 | 第三方和自编译应用 |
| `/boot` | 引导文件 | **切勿修改**——损坏会导致系统无法启动 |
| `/proc` | 进程信息（虚拟） | 动态生成，含 CPU、内核详情 |
| `/root` | root 用户主目录 | **独立设置出于安全考虑** |

---

### 三、云区域与可用区

#### 1. 区域（Region）
特定地理位置，可在其中托管资源。**区域是可用区（Availability Zone）的集合**。
- 例如：US East（N. Virginia）、EU West（Ireland）、Asia Pacific（Tokyo）
- 部署在一个区域的资源**不会自动复制**到另一个区域
- **数据主权（Data Sovereignty）**：部分数据必须依法留在特定地理边界内（如 GDPR 要求 EU 公民数据留在欧盟）

#### 2. 可用区（Availability Zone, AZ）
区域内的部署区域，是**逻辑隔离边界（Logical Isolation Boundary）**，而非单个物理建筑。

> **关键区别**：AZ ≠ 数据中心。一个 AZ 可以跨越多个数据中心，每个数据中心有独立的电源供应、冷却系统和物理安全设施。

#### 3. AZ 间网络特性
- **典型区内延迟（Inter-zone Latency）**：1–2 毫秒
- 使**同步复制（Synchronous Replication）**成为可能
- **跨区域延迟**：数十到数百毫秒
- 这个延迟差异至关重要：同步复制仅在区域内可行

#### 4. AWS 特定架构
```
Region（区域）
├── Availability Zone 1（可用区 1）
│   ├── Data Center A
│   └── Data Center B
├── Availability Zone 2（可用区 2）
│   └── Data Center C
└── Availability Zone 3（可用区 3）
    └── Data Center D
```

---

## 概念间关系

```
Linux 在云中的角色：
云基础设施 → 90%+ 运行 Linux → 掌握 Linux CLI = 掌握云操作

地理架构层级：
云提供商
└── Region（区域，地理位置）
    └── Availability Zone（可用区，逻辑隔离单元）
        └── Data Center（数据中心，物理设施）

高可用部署策略：
多可用区（Multi-AZ）→ 应对 AZ 级故障（常见）
多区域（Multi-Region）→ 应对区域级灾难（罕见但灾难性）
```

---

## 对比表格

### 1. 绝对路径 vs 相对路径
| 类型 | 定义 | 示例 | 适用场景 |
|------|------|------|----------|
| 绝对路径（Absolute Path） | 从根 `/` 开始的完整路径 | `/home/user/documents` | 脚本中，路径明确时 |
| 相对路径（Relative Path） | 相对于当前位置 | `../config/settings.txt` | 交互操作，位置已知时 |

### 2. 符号链接 vs 硬链接
| 特性 | 符号链接（Symbolic Link） | 硬链接（Hard Link） |
|------|--------------------------|---------------------|
| 创建命令 | `ln -s target link_name` | `ln target link_name` |
| 删除原文件 | 链接失效（悬挂链接） | 文件数据仍可访问 |
| 跨文件系统 | ✅ 可以 | ❌ 不可以 |
| 链接目录 | ✅ 可以 | ❌ 不可以 |
| 常见用途 | 配置文件快捷方式、跨文件系统引用 | 备份场景、节省空间 |

### 3. 权限（Permissions）对比
| 权限 | 符号 | 八进制值 | 对文件的含义 | 对目录的含义 |
|------|------|----------|-------------|-------------|
| 读（Read） | r | 4 | 查看文件内容 | 列出目录内容（ls） |
| 写（Write） | w | 2 | 修改文件内容 | 创建/删除目录中的文件 |
| 执行（Execute） | x | 1 | 作为程序运行 | 进入目录（cd） |

常用权限组合：
| 八进制 | 符号 | 典型用途 |
|--------|------|----------|
| 755 | rwxr-xr-x | 可执行脚本、目录 |
| 644 | rw-r--r-- | 普通文件、配置文件 |
| 600 | rw------- | 私密文件（SSH 密钥） |
| 777 | rwxrwxrwx | **避免使用** |

### 4. 进程信号（Process Signals）
| 信号 | 编号 | 含义 | 能否被捕获 |
|------|------|------|------------|
| SIGHUP | 1 | 挂起，常用于重载配置 | ✅ |
| SIGINT | 2 | 中断（等同 Ctrl+C） | ✅ |
| SIGTERM | 15 | 优雅终止（默认） | ✅ |
| SIGKILL | 9 | 强制终止 | ❌ 不可捕获或忽略 |

### 5. 输出重定向 vs 管道
| 操作符 | 作用 | 示例 |
|--------|------|------|
| `>` | 命令 → 文件（覆盖） | `ls > files.txt` |
| `>>` | 命令 → 文件（追加） | `echo "log" >> app.log` |
| `2>` | 错误输出 → 文件 | `cmd 2> error.log` |
| `\|` | 命令 → 命令（管道） | `ls \| grep txt` |
| `/dev/null` | 丢弃输出 | `cmd 2>/dev/null` |

### 6. 多可用区 vs 多区域
| 维度 | 多可用区（Multi-AZ） | 多区域（Multi-Region） |
|------|---------------------|----------------------|
| 防护范围 | AZ 级故障 | 区域级灾难 |
| 延迟 | 1–2ms（支持同步复制） | 数十–数百ms（异步复制） |
| 复杂度 | 中等 | 高 |
| 成本 | 中等 | 高 |
| 适用场景 | 标准高可用部署 | 灾难恢复、全球低延迟 |

---

## 关键命令与配置

### 文件系统导航
```bash
pwd                          # 显示当前工作目录
cd /home/user/projects       # 绝对路径切换
cd ../config                 # 相对路径切换
cd ~                         # 回到主目录
cd -                         # 回到上一个目录
ls -lah                      # 详细列出（含隐藏文件，人类可读大小）
```

### 文件操作
```bash
cp -r source/ destination/   # 递归复制目录（-r 必须）
mv file.txt newname.txt      # 重命名（Linux 无专用 rename 命令）
mkdir -p projects/app/src    # 创建多层目录（-p 不报错已存在）
touch placeholder.txt        # 创建空文件或更新时间戳
rm -rf old_directory/        # 递归强制删除（无回收站！谨慎）

# 查找文件
find /var/log -name "*.log"  # 按名称查找
find . -type f -mtime -7     # 最近 7 天修改的文件
find /home -size +100M       # 大于 100MB 的文件
```

### I/O 与文本处理
```bash
cat file.txt                 # 显示文件内容
head -n 20 file.txt          # 显示前 20 行（默认 10 行）
tail -f /var/log/app.log     # 实时跟踪日志（-f 非常重要！）
grep -rni "ERROR" /var/log/  # 递归、大小写不敏感、显示行号搜索

# 管道组合
ls -l | grep ".txt" | head -5          # 列出前5个txt文件
ps aux | grep nginx                     # 查找 nginx 进程
cat access.log | grep "404" | wc -l    # 统计 404 错误数量
```

### 权限管理
```bash
chmod 755 script.sh          # 八进制设置权限
chmod u+x script.sh          # 符号：给所有者加执行权限
chmod go-w file.txt          # 移除组和其他人的写权限
chown bob:developers file.txt # 修改所有者和组
sudo command                 # 以超级用户身份执行（用自己的密码）
# 切勿直接编辑 /etc/sudoers，使用 visudo
```

### 进程管理
```bash
ps aux                       # 查看所有进程快照
top                          # 实时动态进程视图（q 退出，k 杀进程）
kill 1234                    # 发送 SIGTERM 到 PID 1234（优雅终止）
kill -9 1234                 # 强制终止（SIGKILL，无法被捕获）
kill -HUP 1234               # 发送 SIGHUP（常用于重载配置）
nohup long_command &         # 后台运行，终端关闭后继续执行

# 工作控制
./script.sh &                # 后台运行
# Ctrl+Z                     # 暂停进程
jobs                         # 查看后台/暂停的作业
bg %1                        # 将作业 1 在后台继续
fg %1                        # 将作业 1 调到前台
```

### 包管理（Ubuntu/Debian APT）
```bash
sudo apt update              # 刷新可用包列表（安装前必做）
sudo apt upgrade             # 升级所有已安装包
sudo apt install nginx       # 安装 nginx
sudo apt remove package      # 删除包（保留配置文件）
sudo apt purge package       # 彻底删除（含配置文件）
sudo apt autoremove          # 清除未使用的依赖

# 自动安全更新
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
```

### 网络与文件传输
```bash
ssh user@hostname            # 远程登录
ssh -p 2222 user@hostname    # 指定端口连接
scp file.txt user@host:/path/ # 复制文件到远程
scp -r dir/ user@host:/path/ # 递归复制目录到远程
curl -O https://example.com/file.zip  # 下载文件（保持原文件名）
curl -I https://example.com  # 只获取 HTTP 头
wget https://example.com/file.zip     # 下载文件
```

### 磁盘与压缩
```bash
df -h                        # 查看磁盘可用空间（人类可读）
du -sh directory/            # 查看目录大小
du -sh * | sort -h           # 按大小排序

# tar 打包
tar -czvf archive.tar.gz directory/  # 创建 gzip 压缩包
tar -xzvf archive.tar.gz             # 解压 gzip 压缩包
tar -xzvf archive.tar.gz -C /dest/   # 解压到指定目录
```

---

## ⭐ 易考点

**1. AZ ≠ 数据中心**
一个 AZ 可以由**多个数据中心**组成，每个数据中心有独立电源、冷却和物理安全设施。这是 AWS 的重要设计哲学。

**2. 区域内 vs 跨区域延迟**
- 区内（Inter-zone）：1–2ms → 支持**同步复制**
- 跨区域（Cross-region）：数十–数百ms → 只能**异步复制**
延迟差异决定了复制策略的选择。

**3. chmod 777 是大忌**
生产环境绝不使用 777 权限。应使用最小必要权限（Least Privilege）。若某功能"需要" 777，真正的修复方法是纠正所有权或组成员关系。

**4. SIGKILL（9）无法被捕获**
`kill -9` 是强制终止，进程无法优雅关机（对比 12-Factor 要素九：优雅关机应响应 SIGTERM）。日常运维优先用 `kill`（默认 SIGTERM），只在进程无响应时才用 `kill -9`。

**5. `rm -rf` 无回收站**
Linux 删除是永久性的，没有回收站。在运行 `rm -rf` 前务必确认路径。不要让脚本以 root 权限运行宽泛的 rm 命令。

**6. `tail -f` 是日志监控的核心工具**
实时跟踪日志文件是 SRE 和 DevOps 的必备技能（对应 12-Factor 要素十一：日志作为事件流）。

---

## 自测题

### 题目一（多选题）

以下关于 Linux 文件权限的说法，哪些是**正确的**？

A. `chmod 755` 表示所有者有读写执行权限，组和其他人有读和执行权限  
B. 要 `cd` 进入一个目录，只需要对该目录有读（r）权限  
C. 符号链接（Symbolic Link）可以跨文件系统创建，而硬链接（Hard Link）不能  
D. `chmod 777` 是一种合理的生产环境权限设置，因为它最简单  
E. SIGKILL（信号 9）不能被进程捕获或忽略  

<details>
<summary>查看答案与解析</summary>

**正确答案：A、C、E**

- **A ✅**：755 = 7（rwx）给所有者，5（r-x）给组，5（r-x）给其他人。
- **B ❌**：错误。进入目录需要**执行（x）**权限，不是读权限。读权限允许 `ls` 列出目录内容，但 `cd` 进入需要 `x`。
- **C ✅**：符号链接（`ln -s`）可以跨文件系统；硬链接（`ln`）不能跨文件系统。
- **D ❌**：错误。777 给所有人完全访问权限，是严重的安全风险，生产环境绝对禁止。
- **E ✅**：SIGKILL 是唯一不能被进程捕获或忽略的信号，操作系统强制执行终止。

</details>

---

### 题目二（多选题）

关于云区域（Region）和可用区（Availability Zone），以下哪些陈述是**正确的**？

A. 一个可用区（AZ）在物理上等同于一个数据中心  
B. 同一区域内不同 AZ 之间的典型延迟约为 1–2 毫秒，支持同步数据复制  
C. 跨区域（Cross-region）部署可以保护应用免受区域级灾难影响，但成本和复杂度更高  
D. 在一个区域部署的资源会自动复制到其他区域以实现高可用  
E. 数据主权（Data Sovereignty）法规（如 GDPR）是选择云区域时的重要考量因素  

<details>
<summary>查看答案与解析</summary>

**正确答案：B、C、E**

- **A ❌**：错误。AZ 是逻辑隔离边界，一个 AZ **可以包含多个数据中心**，每个数据中心有独立电源、冷却设施。AZ ≠ 单个数据中心。
- **B ✅**：1–2ms 的低延迟是 AZ 间网络的关键特性，使同步复制（数据写入主 AZ 同时写入备 AZ）成为可能。
- **C ✅**：多区域（Multi-Region）确实提供更高级别的保护，但需要处理更高的跨区延迟、数据同步复杂性和更高的运营成本。
- **D ❌**：错误。一个区域的资源**不会自动复制**到其他区域。跨区域复制需要显式配置。
- **E ✅**：GDPR 等法规要求特定数据（如 EU 公民个人数据）必须保存在符合要求的地理区域内，这是选择 Region 的法规驱动因素。

</details>

---

### 题目三（判断题）

**陈述**：在 Linux 中，`kill -9 PID` 是终止失控进程的首选命令，因为它最彻底。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

`kill -9`（SIGKILL）虽然彻底，但并**非首选**，原因如下：

1. **无法优雅关机（Graceful Shutdown）**：SIGKILL 无法被进程捕获，进程没有机会：
   - 保存当前状态
   - 关闭数据库连接
   - 完成正在进行的事务
   - 清理临时文件

2. **正确操作顺序**：
   ```bash
   kill 1234         # 首先发送 SIGTERM（15），给进程优雅退出的机会
   # 等待几秒...
   kill -9 1234      # 如果进程不响应，才升级为 SIGKILL
   ```

3. **12-Factor 关联**：要素九（Disposability）要求进程响应 SIGTERM 实现优雅关机，`kill -9` 完全绕过了这一机制。

4. **SIGHUP（1）**：对于服务配置重载，通常发送 SIGHUP 而不是终止进程（如 `kill -HUP nginx_pid` 让 nginx 重读配置）。

**结论**：`kill -9` 是"最后手段"，日常优先使用 `kill`（默认 SIGTERM）。

</details>

---

### 题目四（判断题）

**陈述**：在 Linux 中，`apt update` 命令会升级所有已安装的软件包到最新版本。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

`apt update` 和 `apt upgrade` 是**两个不同的命令**，经常被混淆：

| 命令 | 实际作用 |
|------|----------|
| `sudo apt update` | **仅刷新**可用包的列表（从软件源下载最新的包元数据）。不安装任何东西。 |
| `sudo apt upgrade` | **实际升级**所有已安装的包到新版本。 |

**正确的更新工作流**：
```bash
sudo apt update     # Step 1: 先刷新包列表
sudo apt upgrade    # Step 2: 再升级包
```

**类比**：`apt update` 就像刷新超市的商品目录（知道有哪些新品），`apt upgrade` 才是真正去购买更新的商品。

在生产服务器上，建议始终先运行 `update` 再运行 `install` 或 `upgrade`，确保获取最新的安全补丁版本。

</details>

---

### 题目五（场景分析题）

**场景**：

Priya 是一家金融科技（FinTech）公司的云架构师，公司正在将核心交易系统迁移到 AWS。该系统要求：
- 服务可用性 **99.99%**（每年允许停机不超过约 52 分钟）
- 印度用户的数据**必须存储在印度境内**（本地法规要求）
- 数据库的写操作必须**实时同步**到备份节点，不允许数据丢失

Priya 在规划部署架构时，需要决定：
1. 应该选择哪个 AWS Region，为什么？
2. 应该使用多可用区（Multi-AZ）还是多区域（Multi-Region）部署？
3. 如何确保数据库实时同步不丢失数据？

请从区域与可用区的角度，分析并给出技术方案。

<details>
<summary>查看参考答案</summary>

**参考答案：**

**1. Region 选择：AWS ap-south-1（亚太地区，孟买）**

- **合规驱动**：印度本地法规（类似 GDPR 的数据主权要求）规定用户数据必须存储在印度境内。必须选择位于印度的 AWS Region（ap-south-1 Mumbai）。
- **性能驱动**：用户在印度，选择地理位置最近的 Region 可最小化延迟，提升用户体验。
- **错误选择分析**：选择 us-east-1（弗吉尼亚）不仅违反法规，还会引入高延迟（数百毫秒）。

**2. 部署策略：多可用区（Multi-AZ）**

- **首选 Multi-AZ**，而非 Multi-Region，原因：
  - 99.99% 可用性通过 Multi-AZ 完全可实现（保护最常见的 AZ 级故障）
  - **延迟关键**：Multi-AZ 的 AZ 间延迟约 1–2ms，支持**同步复制**
  - Multi-Region 的跨区域延迟数十–数百ms，只能异步复制，无法满足"不允许数据丢失"的要求
  - Multi-Region 成本和复杂度更高，且法规要求数据不能离开印度，排除了使用其他 Region 的可能

- **具体部署**：在 ap-south-1 Region 内的 3 个 AZ 中均匀分布应用实例，通过 Load Balancer 分流。

**3. 数据库实时同步方案**

- 利用 AZ 间 1–2ms 低延迟，配置**同步复制（Synchronous Replication）**：
  - 主数据库在 AZ-1，写操作同时写入 AZ-1 主库和 AZ-2 备库
  - 只有两个节点都确认写入成功，才向应用程序返回成功（零数据丢失）
  - 使用 AWS RDS Multi-AZ 可自动实现此配置

- **为何不用 Multi-Region 同步**：跨区域延迟太高（数百ms），同步等待会严重影响每笔交易的响应时间；且数据法规禁止数据离开印度。

**总结架构**：
```
印度用户
    ↓
AWS ap-south-1 (Mumbai) Region
    ├── AZ-1：应用实例 + 主数据库（Primary DB）
    ├── AZ-2：应用实例 + 备用数据库（Standby DB，同步复制）
    └── AZ-3：应用实例（自动故障转移备用）
```

**关键考点**：法规合规（选 Region）→ 高可用架构（Multi-AZ）→ 低延迟使同步复制可行（零数据丢失）——三个需求分别对应区域/AZ 架构的不同层次。

</details>
