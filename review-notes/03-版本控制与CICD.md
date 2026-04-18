# L04 + L12 — 版本控制、测试与 CI/CD

## 模块总览

本模块构建从**代码管理**到**自动化交付**的完整流水线：Git 版本控制提供变更历史与协作基础，测试金字塔保障代码质量，CI 实现自动化集成验证，CD 将经过验证的制品安全推向生产环境。

---

## 核心概念

### 一、版本控制（Version Control）

#### 1. 什么是版本控制？
记录文件随时间变化的系统，支持**记录变更（Records changes）**、**随时间追踪（Over time）**、**召回特定版本（Recall specific versions）**三大功能。

#### 2. 版本控制系统分类
| 类型 | 代表产品 | 核心特点 | 缺点 |
|------|----------|----------|------|
| 本地（Local VCS） | 手动备份文件夹 | 最简单 | 易出错，无法协作 |
| 集中式（CVCS） | CVS、SVN、Perforce | 所有人共享一个服务器 | **单点故障（Single Point of Failure）**——服务器宕机无法协作 |
| 分布式（DVCS） | **Git**、Mercurial | 每个克隆都是**完整仓库备份** | 复杂度稍高 |

> ⭐ Git 是分布式的，因此没有单点故障，可以**离线工作**，弹性更强——这是它在云环境中占主导地位的原因。

#### 3. Git 核心概念
| 术语 | 定义 |
|------|------|
| 仓库（Repository） | 存储所有文件及完整历史的数据库 |
| 提交（Commit） | 项目在某时间点的**快照（Snapshot）**，含唯一哈希、作者、消息 |
| 分支（Branch） | 仓库的并行版本，不影响主线的实验空间 |
| 合并（Merge） | 将不同分支的变更合并在一起 |

#### 4. Git 的架构特点
- **基于快照（Snapshot-based）**，而非增量差异（Delta-based）——分支和合并极快
- **SHA-1 哈希（Hash）**保证完整性——任何文件变更都无法逃过检测
- **几乎所有操作都是本地的**——只有 push/pull/fetch 需要网络

#### 5. Git 三种状态
```
工作目录（Working Directory）
    → git add →
暂存区（Staging Area / Index）
    → git commit →
本地仓库（Local Repository .git）
    → git push →
远程仓库（Remote Repository）
```

暂存区的价值：让你精心选择哪些变更一起提交，而不是强迫将所有修改打包。

---

### 二、软件测试（Software Testing）

#### 1. 测试金字塔（Testing Pyramid）
```
        ┌───────────────────┐
        │   E2E / 功能测试   │  ← 最少、最慢、信心最高
        └───────────────────┘
        ┌───────────────────┐
        │    集成测试        │  ← 适中数量
        └───────────────────┘
        ┌───────────────────┐
        │    单元测试        │  ← 最多、最快
        └───────────────────┘
```
**核心原则**：金字塔优化快速反馈。CI/CD 管道需要快速失败的测试，让开发者获得即时反馈。

#### 2. 三类核心测试
| 测试类型 | 测试对象 | 速度 | 依赖 | 特点 |
|----------|----------|------|------|------|
| 单元测试（Unit Test） | 单个函数/方法/类 | 毫秒级 | 无外部依赖，使用 Mock/Stub | 最快、最多、隔离 |
| 集成测试（Integration Test） | 组件间交互 | 秒级 | 数据库、API、消息队列 | 验证组件协同 |
| 端到端测试（E2E Test） | 完整用户工作流 | 分钟级 | 完整栈（UI → API → DB） | 最慢、最易脆、信心最高 |

#### 3. 其他测试类型
| 类型 | 定义 |
|------|------|
| 冒烟测试（Smoke Test） | 部署后快速验证关键功能正常 |
| 回归测试（Regression Test） | 验证新变更未破坏已有功能 |
| 性能测试（Performance Test） | 负载下测量响应时间、吞吐量 |
| 安全测试（Security Test） | 识别注入、认证漏洞等 |
| 契约测试（Contract Test） | 验证服务间 API 协议 |

---

### 三、持续集成（Continuous Integration, CI）

#### 1. 定义
开发者将代码变更**频繁集成**到共享仓库（每天多次），每次集成通过**自动化构建**和**自动化测试**验证。

#### 2. CI 三大支柱
| 支柱 | 说明 |
|------|------|
| 频繁提交（Frequent Commits） | 至少每天集成，理想每天多次；小而增量的变更 |
| 自动化构建（Automated Build） | 每次提交触发自动构建，构建必须可重复和一致 |
| 自动化测试（Automated Testing） | 测试自动运行，通过才视为"已集成" |

#### 3. CI 管道流程
```
代码推送（Trigger）
    → 检出代码 + 安装依赖
    → 编译/构建
    → 单元测试（< 1 分钟，快速失败）
    → 集成测试
    → 代码分析（Lint、安全扫描）
    → 生成制品（Artifact，如 Docker 镜像、JAR）
    → 推送到制品仓库（如 ECR、GCR）
```

> **目标**：主要反馈在 **10 分钟以内**。

#### 4. 关键原则
- **流水线即代码（Pipeline as Code）**：CI 配置文件存于版本控制，和应用代码同等对待
- **快速失败（Fail Fast）**：第一个失败即停止管道，节省时间
- CI 是 CD 的前提——没有 CI，就没有 CD

---

### 四、持续交付与持续部署（Continuous Delivery & Continuous Deployment, CD）

#### 1. 三者区别（极重要！）
| 概念 | 核心特征 | 人工介入？ |
|------|----------|------------|
| 持续集成（CI） | 自动构建 + 自动测试 | 是（合并代码） |
| 持续交付（Continuous Delivery） | 制品**可以**一键部署到生产 | **是（人工审批关卡）** |
| 持续部署（Continuous Deployment） | 通过测试则**自动**部署到生产 | **否（全自动）** |

> **行业现实**：大多数组织实践持续**交付**；完全自动化的持续**部署**要求对测试覆盖率和可观测性有极高信心。

#### 2. 部署管道（Deployment Pipeline）结构
```
CI 输出制品
    → 制品存储（Artifact Store，如 ECR、S3）
    → 部署到暂存环境（Staging，与生产相同）
    → 自动验收测试（冒烟测试、集成测试）
    → 审批关卡（持续交付）/ 自动提升（持续部署）
    → 部署到生产
    → 部署后验证（健康检查、监控指标）
    → 回滚路径（Rollback Path）
```

**关键原则**：**经过测试的制品就是被部署的制品**——永远不要为生产重新构建。

#### 3. 三种部署策略

**滚动部署（Rolling Deployment）**
```
负载均衡器
  ├→ 实例 A：v1 → v2 ✓
  ├→ 实例 B：v1 → v2 ✓
  └→ 实例 C：v1 → 更新中...
```
- 逐批替换实例，部署期间新旧版本**同时提供服务**
- AWS: CodeDeploy（OneAtATime / HalfAtATime / AllAtOnce）

**蓝绿部署（Blue-Green Deployment）**
```
DNS/负载均衡器
    ↙              ↘
  Blue（v1）     Green（v2）
  当前生产         新版本

步骤：部署 v2 到 Green → 测试 Green → 切换流量 → Green 成为新生产
回滚：流量切回 Blue（瞬间）
```
- 回滚最快，但成本最高（需要双倍基础设施）
- AWS: Elastic Beanstalk URL 切换、Route 53 加权路由

**金丝雀部署（Canary Deployment）**
```
负载均衡器
    ↙ 95%          ↘ 5%
  Stable（v1）   Canary（v2）

指标健康 → 逐步增加：5% → 25% → 50% → 100%
指标异常 → 立即回滚到 100% v1
```
- 风险最低，复杂度最高，需要强大的**可观测性（Observability）**
- AWS: CodeDeploy 金丝雀配置、ALB 加权目标组
- GCP: Cloud Run 流量拆分

#### 4. CD 的关键支撑技术
| 支撑技术 | 作用 |
|----------|------|
| 不可变基础设施（Immutable Infrastructure） | 从不修补运行中的服务器，直接替换——消除配置漂移 |
| 基础设施即代码（IaC） | 暂存环境精确镜像生产环境 |
| 功能标志（Feature Flags/Toggles） | 将**部署**与**发布**解耦——代码上线但功能隐藏 |
| 全面自动化测试 | CD 的信心来源 |
| 可观测性（Observability） | 捕捉部署后的回归 |

---

## 概念间关系

```
版本控制（Git）
    ↓ 代码推送触发
持续集成（CI）
    频繁提交 + 自动构建 + 自动测试（金字塔：Unit > Integration > E2E）
    ↓ 输出：经过验证的不可变制品（Docker 镜像、AMI 等）
持续交付（CD - Delivery）
    部署到 Staging → 自动验收测试 → [人工审批] → 部署生产
    OR
持续部署（CD - Deployment）
    部署到 Staging → 自动验收测试 → [自动] → 部署生产
    ↓ 部署策略选择
滚动（简单低成本）/ 蓝绿（快速回滚）/ 金丝雀（渐进降险）
```

**12-Factor 对应关系**：
- Factor V（Build/Release/Run）→ CI 构建阶段 + CD 不可变制品
- Factor X（Dev/Prod Parity）→ Staging 环境与生产保持一致
- Factor III（Config in Env）→ 部署时注入环境变量，不硬编码
- Factor XI（Logs as Streams）→ 可观测性支撑部署后验证

---

## 对比表格

### 1. 三种测试对比
| 维度 | 单元测试 | 集成测试 | E2E 测试 |
|------|----------|----------|----------|
| 测试范围 | 单个函数/类 | 多组件交互 | 完整用户流程 |
| 执行速度 | 毫秒级 | 秒级 | 分钟级 |
| 外部依赖 | 无（用 Mock） | 有（DB、API） | 完整栈 |
| 数量 | 最多 | 中等 | 最少 |
| 脆弱性 | 最低 | 中等 | 最高（易不稳定） |
| CI 管道中的位置 | 最先运行 | 中间 | 最后/暂存环境 |

### 2. 三种部署策略对比
| 标准 | 滚动（Rolling） | 蓝绿（Blue-Green） | 金丝雀（Canary） |
|------|----------------|-------------------|-----------------|
| 停机风险 | 低 | 非常低 | 非常低 |
| 回滚速度 | 中等 | 快速（切换流量） | 快速（转移流量） |
| 基础设施成本 | 最低 | 高（2x 环境） | 中等 |
| 复杂度 | 低 | 中等 | 高 |
| 混合版本 | 是 | 短暂或无 | 是（受控） |
| 适合场景 | 成本敏感、简单应用 | 任务关键、快速回滚 | 大用户群、逐步降险 |

### 3. git merge vs git rebase
| 维度 | git merge | git rebase |
|------|-----------|------------|
| 历史记录 | 保留完整分叉历史，有 merge commit | 线性历史，干净整洁 |
| 冲突处理 | 一次性解决 | 逐提交解决 |
| 共享分支 | ✅ 安全 | ❌ 不要在共享分支上 rebase |
| 适用场景 | 主分支合并、团队协作 | 本地功能分支整理 |

### 4. CI vs CD 对比
| 维度 | 持续集成（CI） | 持续交付（Delivery） | 持续部署（Deployment） |
|------|---------------|---------------------|----------------------|
| 触发条件 | 代码推送/PR | CI 成功后 | CI 成功后 |
| 自动化程度 | 构建 + 测试 | 到 Staging 全自动 | 到生产全自动 |
| 人工介入 | 代码审查 | **生产部署需审批** | **无需人工** |
| 风险控制 | 测试拦截 | 审批关卡 | 依赖测试 + 可观测性 |

### 5. git fetch vs git pull
| 命令 | 作用 | 安全性 |
|------|------|--------|
| `git fetch` | 下载远程变更到本地，但不合并 | 更安全，先看再合并 |
| `git pull` | `fetch` + `merge`（或 `rebase`） | 直接合并，可能产生冲突 |

---

## 关键命令与配置

### Git 基础工作流
```bash
# 初始化与克隆
git init                         # 创建新仓库
git clone <url>                  # 克隆远程仓库

# 日常工作流
git status                       # 检查工作区状态（经常使用！）
git add <file>                   # 暂存指定文件
git add .                        # 暂存所有变更
git diff                         # 查看未暂存的差异
git diff --staged                # 查看已暂存的差异
git commit -m "Add user auth"    # 创建提交（提交消息用祈使语气）
git log --oneline                # 紧凑历史视图
```

### Git 分支管理
```bash
git branch                       # 列出本地分支
git branch feature/login         # 创建新分支
git checkout feature/login       # 切换到分支
git checkout -b feature/login    # 创建并切换（快捷方式）
git merge feature/login          # 将分支合并到当前分支
git branch -d feature/login      # 删除已合并的分支
```

### Git 远程操作
```bash
git remote add origin <url>      # 添加远程仓库
git push -u origin main          # 首次推送（设置上游）
git push                         # 后续推送
git pull                         # 拉取并合并（= fetch + merge）
git fetch                        # 只拉取，不合并
```

### SSH 密钥配置（连接 GitHub）
```bash
# 生成 SSH 密钥
ssh-keygen -t ed25519 -C "your.email@example.com"

# 启动 SSH agent 并添加密钥
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 查看公钥（复制到 GitHub → Settings → SSH Keys）
cat ~/.ssh/id_ed25519.pub

# 测试 GitHub 连接
ssh -T git@github.com
```

### .gitignore 示例
```gitignore
# 依赖
node_modules/
__pycache__/
*.pyc

# 构建产物
dist/
build/
*.jar

# 环境与密钥（绝对不要提交！）
.env
*.key
*.pem
secrets.json

# IDE 文件
.idea/
.vscode/
```

### GitHub Actions CI 配置示例（Pipeline as Code）
```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3         # 检出代码
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: pip install -r requirements.txt  # 安装依赖
      
      - name: Run unit tests
        run: pytest tests/unit/ -v           # 单元测试（快速）
      
      - name: Run integration tests
        run: pytest tests/integration/ -v    # 集成测试
      
      - name: Lint code
        run: flake8 .                        # 代码风格检查
      
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .  # 用 commit SHA 标记
      
      - name: Push to ECR
        run: |
          aws ecr get-login-password | docker login --username AWS ...
          docker push $ECR_REGISTRY/myapp:${{ github.sha }}
```

### 提交消息规范
```bash
# 好的提交消息
git commit -m "Add OAuth2 authentication for Google login"
git commit -m "Fix null pointer exception in payment handler"
git commit -m "Refactor user service to use repository pattern"

# 坏的提交消息（避免）
git commit -m "fix bug"
git commit -m "WIP"
git commit -m "asdfgh"

# 规范：
# - 祈使语气：Add / Fix / Refactor（不是 Added / Fixed）
# - 第一行 ≤ 50 个字符
# - 说明 WHAT 和 WHY，而非 HOW
```

---

## ⭐ 易考点

**1. 持续交付 vs 持续部署 的核心区别**
持续交付（Continuous **Delivery**）：生产部署需要**人工审批**，"可以"部署但不自动部署。
持续部署（Continuous **Deployment**）：通过所有测试后**全自动**部署到生产，无任何人工干预。
大多数公司实践持续**交付**，而非持续**部署**。

**2. 测试金字塔的方向**
单元测试（最多）→ 集成测试（中等）→ E2E 测试（最少）。不要倒置金字塔（过度依赖 E2E 会让 CI 极慢）。单元测试运行在**毫秒级**，CI 管道目标反馈时间 **< 10 分钟**。

**3. 蓝绿部署的回滚机制**
回滚 = 将流量切回 Blue 环境（瞬间完成）。代价是需要同时维护两套完整环境（成本翻倍）。

**4. 不提交密钥到版本控制**
`git commit` 之前必须检查 `.gitignore`，绝对不提交 `.env`、`*.key`、`*.pem`、`secrets.json`。密钥一旦推送到远程仓库就视为泄露，即使删除历史记录也不安全（GitHub 会扫描并告警）。

**5. "经过测试的制品就是被部署的制品"**
CD 的核心原则：CI 阶段构建的制品（Docker 镜像、AMI）直接推进到生产，**不在生产前重新构建**。重新构建会引入环境差异，违背 12-Factor Factor X（Dev/Prod Parity）。

**6. 功能标志（Feature Flags）解耦部署与发布**
代码可以部署到生产但在功能标志后面隐藏，等业务准备好再开启。这使持续部署变得可行，同时避免向用户暴露未完成功能。

---

## 自测题

### 题目一（多选题）

以下关于 Git 版本控制的说法，哪些是**正确的**？

A. Git 是集中式版本控制系统（CVCS），所有历史记录存储在中央服务器上  
B. Git 基于快照（Snapshot）存储，而非逐次增量差异（Delta），这使分支和合并操作非常高效  
C. 暂存区（Staging Area）的存在是多余的，可以直接从工作目录提交  
D. 每个 Git 提交（Commit）都有唯一的 SHA-1 哈希标识符，保证内容完整性  
E. 使用 `git fetch` 只下载远程变更但不合并，而 `git pull` 相当于 `fetch` + `merge`  

<details>
<summary>查看答案与解析</summary>

**正确答案：B、D、E**

- **A ❌**：错误。Git 是**分布式版本控制系统（DVCS）**，每个克隆都是完整仓库的备份，没有单点故障，可以离线工作。CVS、SVN 才是集中式（CVCS）。
- **B ✅**：Git 存储的是整个项目状态的快照，而非文件差异。文件未改变时，Git 存储对前一个相同文件的引用。快照模型使分支创建和切换极快（O(1)操作）。
- **C ❌**：错误。暂存区非常有价值——它允许你精心选择哪些变更组合在一次提交中，将相关变更一起提交，将不相关变更分开。没有暂存区，你只能将工作目录所有改动一起提交。
- **D ✅**：所有内容经过 SHA-1 哈希校验。不可能在 Git 不知情的情况下修改内容，这保证了历史记录的完整性和不可篡改性。
- **E ✅**：`git fetch` 将远程变更下载到本地仓库但不修改工作目录；`git pull` = `git fetch` + `git merge`，直接将远程变更合并到当前分支。

</details>

---

### 题目二（多选题）

以下关于 CI/CD 流水线（Pipeline）的说法，哪些是**正确的**？

A. 持续部署（Continuous Deployment）和持续交付（Continuous Delivery）的唯一区别是，前者在部署到生产前需要人工审批  
B. 测试金字塔中，单元测试（Unit Tests）应该数量最多，因为它们执行最快且隔离最好  
C. 金丝雀部署（Canary Deployment）通过将一小部分流量（如 5%）路由到新版本来降低部署风险  
D. 蓝绿部署（Blue-Green Deployment）的主要优势是成本最低，因为只需一套环境  
E. "流水线即代码（Pipeline as Code）"意味着 CI 配置文件应该存放在版本控制中，与应用代码一起管理  

<details>
<summary>查看答案与解析</summary>

**正确答案：B、C、E**

- **A ❌**：说反了。持续**交付（Delivery）**需要人工审批才能部署到生产（可以但不自动部署）；持续**部署（Deployment）**是全自动的，通过所有测试即自动部署到生产，**无需**人工介入。
- **B ✅**：测试金字塔的核心原则——单元测试在毫秒级内执行、无外部依赖、最易维护，应该数量最多。过度依赖 E2E 测试会导致 CI 管道极慢，违背"10 分钟内反馈"的目标。
- **C ✅**：金丝雀部署的核心机制就是先路由小比例流量（如 5%）到新版本，监控错误率和延迟等指标，健康则逐步增加比例，问题则立即回滚。
- **D ❌**：错误。蓝绿部署需要**两套完整的相同环境**（Blue + Green），成本最高（在切换窗口期间相当于双倍基础设施费用）。成本最低的是滚动部署（Rolling Deployment）。
- **E ✅**：Pipeline as Code 是 CI/CD 的重要实践——将 CI 配置（如 `.github/workflows/*.yml`、`Jenkinsfile`）存放在版本控制中，管道变更经历相同的代码审查流程，保证可追溯性和一致性。

</details>

---

### 题目三（判断题）

**陈述**：在 CD 流水线中，为了确保生产环境的代码是最新的，最佳实践是在部署到生产之前，针对生产环境的配置重新构建（Rebuild）应用制品。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

这违反了 CD 的核心原则：**"经过测试的制品就是被部署的制品（The artifact that was tested is the artifact that gets deployed）"**。

**为什么不能重新构建？**

1. **引入不确定性**：重新构建意味着代码、依赖、构建环境都可能发生变化。即使代码相同，依赖包版本可能已更新，构建工具行为也可能不同——你部署的实际上是一个**没经过测试的新制品**。

2. **违反 12-Factor Factor X（Dev/Prod Parity）**：如果在不同环境重新构建，每次构建可能产生略有不同的制品，破坏环境一致性。

3. **正确做法**：
   ```
   CI 阶段（一次性构建）：
       代码 → 构建 → 测试 → 打标签制品
       例如：myapp:git-sha-abc123 → 推送到 ECR/GCR
   
   CD 阶段（拉取同一制品部署）：
       Staging：拉取 myapp:abc123 → 测试通过
       Production：拉取相同的 myapp:abc123 → 部署
   ```

4. **不可变制品（Immutable Artifact）**：制品一旦构建，用 Git commit SHA 标记，之后只是在不同环境间"提升（Promote）"，而不是重建。这是**不可变基础设施（Immutable Infrastructure）**思想在制品层面的体现。

</details>

---

### 题目四（判断题）

**陈述**：在 CI 管道中，应该先运行端到端测试（E2E Tests），因为它们覆盖最广、测试最全面，有助于最早发现问题。

**True or False？**

<details>
<summary>查看答案与解析</summary>

**答案：False（错误）**

**解析：**

这违反了**测试金字塔（Testing Pyramid）**的基本原则。正确顺序是从金字塔**底部**往上运行：

**正确的 CI 测试顺序**：
```
1. 单元测试（Unit Tests）  ← 毫秒级，最先运行
2. 构建/编译
3. 集成测试（Integration Tests）  ← 秒级
4. 代码分析（Linting、安全扫描）
5. 生成制品
── 部署到 Staging ──
6. E2E 测试 / 冒烟测试  ← 在类生产环境中运行
```

**为什么不先运行 E2E？**

1. **速度**：E2E 测试运行需要分钟甚至十几分钟。CI 目标是在 **10 分钟内**给出反馈。如果先跑 E2E，开发者等待时间太长，生产力严重下降。

2. **快速失败（Fail Fast）原则**：单元测试在毫秒内能发现大多数逻辑错误。如果单元测试就失败了，没必要等 E2E 跑完。

3. **E2E 的脆弱性（Flakiness）**：E2E 测试因网络抖动、时序问题容易出现非确定性失败，先跑反而会制造噪音，干扰真正的问题。

4. **资源消耗**：E2E 需要完整环境（数据库、外部服务），提前运行会占用大量资源。

**类比**：先检查数学题的计算过程（单元测试），再检查最终答案（E2E），而不是直接拿最终答案倒推——这样找错误更高效。

</details>

---

### 题目五（场景分析题）

**场景**：

Alex 是一家在线票务平台（类似 Ticketmaster）的 DevOps 工程师。平台当前的问题是：
- 开发团队由 20 人组成，每 2 周才发布一次版本
- 每次发布需要手动测试 3 天，部署过程需要 4 小时停机
- 上个月一次发布引入了支付 Bug，导致部分用户无法购票，花了 2 小时才回滚到旧版本
- 公司明年将举办大型赛事，预计在售票开始时有 100x 的流量峰值，届时绝对不能停机

Alex 的技术负责人要求他设计一套现代化的 CI/CD 方案，并解决以下三个核心问题：
1. 如何将发布频率从"2周一次"提升到"随时可发布"？
2. 如何消除 4 小时停机部署？
3. 如果新版本有问题，如何在 2 分钟内回滚（而非 2 小时）？

请从版本控制、测试、CI、CD 和部署策略的角度，给出完整的技术方案。

<details>
<summary>查看参考答案</summary>

**参考答案：**

---

**问题 1：如何实现"随时可发布"**

根本原因：发布频率低是因为手动测试 3 天 + 集成痛苦（批量变更）。解决方案：

**A. 版本控制规范化**
- 采用**功能分支工作流（Feature Branch Workflow）**：每个功能/修复在独立分支开发
- 每个 PR 提交前经过**代码审查（Code Review）**
- 执行 "Pull before push" 规范，减少合并冲突

**B. 构建持续集成（CI）流水线**
```
开发者 push 代码
    → 触发 GitHub Actions / GitLab CI
    → 单元测试（毫秒级，覆盖支付逻辑等核心路径）
    → 集成测试（验证票务服务 ↔ 支付服务 ↔ 数据库）
    → 代码安全扫描（防止 OWASP 漏洞）
    → 构建 Docker 镜像，标记 commit SHA
    → 推送到 ECR/GCR
总耗时目标：< 10 分钟
```

**C. 测试金字塔策略**
- **大量单元测试**：覆盖支付计算、票价逻辑等，毫秒级执行
- **集成测试**：验证订单服务与支付服务的 API 契约（契约测试 Contract Tests）
- **E2E 冒烟测试**：在 Staging 验证"搜索 → 选座 → 支付"完整流程
- 以前 3 天手动测试可以被 < 10 分钟自动化测试替代

**D. 持续交付（Continuous Delivery）**
- 通过 CI 的代码自动部署到 **Staging 环境**
- 自动化冒烟测试在 Staging 运行
- 生产部署保留**人工审批关卡**（适合票务业务的合规要求）
- 结果：随时"准备好部署"，无需 3 天手工验证

---

**问题 2：消除 4 小时停机部署**

原因分析：4 小时停机说明当前是"停机更新（Maintenance Window）"模式——停服 → 替换 → 重启。

解决方案：**蓝绿部署（Blue-Green Deployment）**

```
当前状态（发布时）：
DNS → Blue（v1，当前生产）

发布新版本时：
1. 将 v2 部署到 Green 环境（用户无感知）
2. 在 Green 上运行完整冒烟测试（10-15分钟）
3. Route 53 DNS 切换 / ALB 目标组切换：流量指向 Green
4. Green（v2）成为新生产环境

停机时间：DNS 切换约 0-30 秒（TTL 期间），实际用户体验零停机
```

**选蓝绿而非金丝雀的原因**：
- 大型票务赛事（演唱会、体育赛事）对数据一致性要求极高，不适合同时运行两个版本处理购票事务（避免同一张票被两个版本同时处理）
- 蓝绿保证任意时刻只有一个版本在处理事务

---

**问题 3：2 分钟内回滚**

蓝绿部署天然支持**即时回滚**：
```
回滚操作：将 DNS / ALB 目标组切回 Blue（v1）
耗时：< 1 分钟（Route 53 加权路由调整）
```

对比之前：2 小时回滚说明是手动重新部署旧版本（没有制品版本化）。

**关键：不可变制品（Immutable Artifact）**
- 每次 CI 构建产生用 commit SHA 标记的 Docker 镜像
- Blue 环境保持运行，只需切换流量指向
- 无需重新构建、无需重新拉取代码

**辅助手段：功能标志（Feature Flags）**
- 对于支付模块这类高风险功能，使用功能标志在蓝绿切换后仍可快速开关
- 即使蓝绿切换完成，发现支付 Bug 也能在不部署的情况下 < 1 分钟禁用该功能

---

**大型赛事流量峰值（100x）应对**

这不在 CI/CD 范围内，但值得提及：
- CD 管道与 Auto Scaling 集成：部署新版本时自动扩容到峰值容量
- 蓝绿环境的 Green 提前热启动（预热实例），避免冷启动

---

**方案总结**

| 问题 | 解决方案 | 关键技术 |
|------|----------|----------|
| 2周发布 → 随时可发布 | 持续集成 + 自动化测试金字塔 | GitHub Actions + 单元/集成测试 |
| 4小时停机 → 零停机 | 蓝绿部署 | Route 53 + ALB 目标组切换 |
| 2小时回滚 → 2分钟 | 不可变制品 + 蓝绿保留旧环境 | ECR 版本化镜像 + 流量切换 |

</details>
