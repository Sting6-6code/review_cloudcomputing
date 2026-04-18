==============================
【通用前置 prompt - 只跑一次】
==============================

我在复习 CSYE 6225 Cloud Computing。
课件路径：/Users/wangsiting/github_Siting/ReviewCloud/review_cloudcomputing/ppt
请创建 /Users/wangsiting/github_Siting/ReviewCloud/review_cloudcomputing/review-notes 文件夹存放复习笔记。



老师说考试一般考多选，正确错误判断，subject problem，场景题分析；
这里有老师发过的一些类似的场景题，期末考试基本上是一半多选，正确错误判断一半这种类似的sample question；
These specific questions will most likely not appear on the actual exam. Please do not over-rely on them or limit your studying to just these topics. The exam will cover the full breadth of material discussed in class.
Use these samples as a starting point to check your understanding, then make sure you're comfortable applying concepts across all the topics we've covered.
Good luck with your preparation!

Regards,

--Tejas



Question 1 – TCP vs UDP

Sarah is building two applications: a banking portal where every transaction must be reliably delivered, and a live sports score ticker where speed matters more than perfection. She's choosing between TCP and UDP for each.

Explain the key differences between TCP and UDP that would help Sarah make her decision. Which protocol should she choose for each application, and why

Question 2 – DNS TTL

Priya's company is planning a migration where their main website www.example.com will move from an old server (IP: 10.0.0.5) to a new server (IP: 10.0.0.99) over the weekend. She's worried that even after updating the DNS record, some users will still be directed to the old server for hours.

Explain what the TTL setting on a DNS record is and how it contributes to this problem. What would you advise Priya to do before the migration weekend to minimize downtime?

Question 3 – Horizontal vs Vertical Scaling

An e-commerce company experiences a 10x traffic spike every year during Black Friday. Their current setup runs on a single powerful server. The CTO is debating two strategies: upgrading to an even more powerful server, or distributing the load across multiple smaller servers.

Explain the difference between these two approaches — vertical and horizontal scaling. Discuss the trade-offs of each, and recommend which strategy is better suited for handling unpredictable traffic spikes like Black Friday.

Question 4 – Five Essential Characteristics of Cloud

Your manager claims that because the company recently moved its application to a set of virtual machines hosted in a co-location data center, they are now "in the cloud." You're not so sure.

According to NIST, what are the five essential characteristics an IT environment must exhibit to be considered cloud computing? Based on these characteristics, explain whether your manager's claim is justified or not.

Question 5 – Instance Store vs EBS-backed EC2

A data analytics team runs nightly batch jobs that process terabytes of raw log files. The jobs are compute-intensive, generate massive amounts of temporary intermediate data, and the results are written to S3 when complete. If a job fails, it simply restarts from scratch — no intermediate data needs to be preserved.

Explain the difference between an Instance Store-backed EC2 instance and an EBS-backed EC2 instance. Given the analytics team's workload described above, which backing store would you recommend and why? What is the key risk the team should be aware of with your recommendation?
==============================
【模块 1：基础概念与开发理念】
==============================

请阅读以下PDF文件：
- L01_01_Fundamental*.pdf
- L02_01_SDLC.pdf
- L02_02_12factor.pdf
- L02_03_DevOps.pdf

生成 review-notes/01-基础概念与开发理念.md，要求：
1. **模块总览**：一句话概括
2. **核心概念**：每个关键概念1-2句中文解释，专有名词保留英文
3. **概念间关系**：说清这几个PDF的逻辑关联
4. **对比表格**：有对比关系的用markdown表格
5. **关键命令/配置**：用code block
6. **⭐ 易考点**：3-5个
7. **自测题**：5道（选择+简答+场景），答案放<details>标签

用中文，技术术语首次出现括号注英文。

==============================
【模块 2：版本控制与 CI/CD】
==============================

请阅读：
- L04_03_Version Cont*.pdf
- L04_01_Testing.pdf
- L04_02_CI.pdf
- L12_01_CD.pdf

生成 review-notes/02-版本控制与CICD.md，同上格式。

==============================
【模块 3：云平台基础】
==============================

请阅读：
- L03_01_Introduction*.pdf
- L03_02_Regions*.pdf

生成 review-notes/03-云平台基础AWS.md，同上格式。

==============================
【模块 4：基础设施即代码】
==============================

请阅读：
- L05_01_Fundamental*.pdf
- L05_02_Infrastructur*.pdf
- L05_03_Terraform.pdf

生成 review-notes/04-基础设施即代码IaC.md，同上格式。

==============================
【模块 5：计算身份与镜像】
==============================

请阅读：
- L06_01_IAM.pdf
- L06_02_Virtualization*.pdf
- L06_03_Machine Ima*.pdf
- L06_04_Cloud Init.pdf

生成 review-notes/05-计算身份与镜像.md，同上格式。

==============================
【模块 6：网络存储与运维】
==============================

请阅读：
- L07_01_Cloud Storag*.pdf
- L08_01_DNS.pdf（注意文件名有空格）
- L08_02_CDN.pdf
- L08_03_SRE.pdf
- L08_04_Observability*.pdf
- L10_01_Email.pdf
- L10_02_Scaling.pdf

生成 review-notes/06-网络存储与运维.md，同上格式。

==============================
【模块 7：现代架构】
==============================

请阅读：
- L11_01_Microservices*.pdf
- L11_02_Event Driven*.pdf
- L11_03_Serverless.pdf
- L12_02_Cloud*.pdf
- L13_01_Architecting*.pdf

生成 review-notes/07-现代架构.md，同上格式。

==============================
【全部完成后 - 生成总复习】
==============================

请阅读 review-notes/ 下所有md文件，生成 review-notes/00-期末总复习.md：
1. 课程知识图谱（模块间关系）
2. 跨模块易混淆概念对比表
3. Top 30 必背知识点（按重要度排序）
4. 20道综合模拟题




请阅读 review-notes/ 下所有已生成的 md 文件（01到07），生成 review-notes/00-期末总复习.md：

1. **课程知识图谱**：用文字描述7个模块之间的关系和依赖
2. **跨模块易混淆概念对比表**：把容易搞混的概念放在一起对比
3. **Top 30 必背知识点**：按重要度排序，标注来源模块
4. **综合模拟考试**（模拟真实考试）：
   - 10道多选题
   - 10道判断题
   - 5道场景分析题（模仿老师sample question风格，带人名和业务场景）
   - 所有答案放 <details> 标签

这是最终的总复习文件，请确保覆盖所有模块的核心内容。