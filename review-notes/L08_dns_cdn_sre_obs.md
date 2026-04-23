# L08 PPT Deep Review — DNS / CDN / SRE / Observability

> Exam format: Multiple Select · True/False · Scenario  
> All keywords in English; Chinese explanations where helpful

---

## L08_01 — DNS

### Core Definition
**DNS = "phonebook of the Internet"**  
A globally distributed, hierarchical database that maps domain names to IP addresses.  
DNS 是全球分布式的分层数据库，不是单一服务器。

### 5 Properties of DNS
| Property | Meaning |
|----------|---------|
| Distributed | No single point of failure |
| Scalable | Handles billions of queries |
| Reliable | Multiple redundant servers |
| Dynamic | Records can change |
| Loosely Coherent | Updates propagate gradually (TTL-based) |

### 3 Components
1. **Name Space** — the inverted tree structure
2. **Name Servers** — store and serve zone data
3. **Resolvers** — client-side, initiate queries (e.g., 8.8.8.8, 1.1.1.1)

### Hierarchy (inverted tree)
```
. (root)
└── TLDs: .com / .org / .edu / .cn
    └── Domains: example.com
        └── Subdomains: www.example.com
```
**FQDN** (Fully Qualified Domain Name) ends with an implied dot: `www.example.com.`

### TLD Types
- **gTLDs**: .com / .org / .edu / .net (generic)
- **ccTLDs**: **exactly 2 letters**, 312 in use (e.g., .cn, .uk)

### Name Server Types
| Type | Role |
|------|------|
| Authoritative (Primary) | Holds original zone data |
| Authoritative (Secondary) | Read-only copy, zone transfer from primary |
| Caching / Recursive Resolver | Does the work for clients (8.8.8.8, 1.1.1.1) |

### Zone & Delegation
- **Zone** = a portion of DNS namespace managed by one set of name servers
- Every zone MUST start with **SOA** record + at least one **NS** record
- **Zone Delegation**: parent zone hands off authority for a subdomain to a child set of name servers
- **Glue Records**: A records added to parent zone for child nameservers that live *inside* the delegated zone — solves circular dependency

### Root Servers
- **13 logical root servers** (A through M), Anycast-mirrored globally (hundreds of physical machines)

### DNS Record Types ⭐

| Record | Purpose | Exam Trap |
|--------|---------|-----------|
| **A** | Domain → IPv4 | — |
| **AAAA** | Domain → IPv6 | — |
| **CNAME** | Alias → another domain name (NOT IP) | ❌ CANNOT be placed at apex/root domain |
| **MX** | Mail server | **Lower number = higher priority** |
| **NS** | Name server for zone | At least 2 required for redundancy |
| **TXT** | SPF / DKIM / domain verification | Used for anti-spam |
| **SOA** | Start of Authority — one per zone | Contains: serial, refresh, retry, expiry, negative TTL |
| **CAA** | Controls which CA can issue TLS certs | No CAA record = **any CA can issue** |
| **PTR** | Reverse DNS (IP → domain) | Lives under `in-addr.arpa` |
| **ALIAS** (Route 53) | Like CNAME but **can** be at apex | Route 53-specific, not standard |

### TTL Strategies
| Scenario | TTL |
|---------|-----|
| Stable records | 300–3600 s |
| Before planned migration | Lower to 60 s (1 day in advance) |
| CDN / Load Balancer | 60–300 s |

### Recursive vs Iterative Queries ⭐
| Type | Who does the work | # of queries |
|------|-----------------|--------------|
| **Recursive** | Resolver does all work | Client makes **1 query** |
| **Iterative** | Client queries each server itself | Multiple queries |

**In practice**: browser sends recursive query to ISP/8.8.8.8; that resolver uses iterative queries internally.

### 8-Step Full DNS Lookup
1. Browser cache → 2. OS cache → 3. Local DNS cache → 4. Recursive resolver  
→ 5. Root server → 6. TLD server → 7. Authoritative name server → 8. Response back to client

### Route 53
| Zone Type | Visibility |
|----------|-----------|
| **Public Hosted Zone** | Accessible from the internet |
| **Private Hosted Zone** | VPC-only, not publicly visible |

- Route 53 SLA: **100%**
- **ALIAS records** solve the CNAME-on-apex limitation

---

## L08_02 — CDN

### Core Definition
**CDN = Content Delivery Network**  
A geographically distributed network of proxy servers that cache content closer to end users.  
将静态内容缓存在离用户最近的服务器，减少延迟。

### Origin vs Edge
| | Origin Server | Edge Server / PoP |
|-|-------------|-----------------|
| Role | Authoritative, handles dynamic requests | Caches static content |
| Content | All types | Static: images, CSS, JS, video |

### Cache Hit vs Cache Miss
- **Cache Hit**: content found at edge → served immediately, no origin request
- **Cache Miss**: not cached → fetch from origin, then cache for future requests

### TTL & Cache-Control
- **Short TTL** = fresher content, more origin load
- **Long TTL** = more cache hits, risk of serving stale content
- Controlled via **HTTP `Cache-Control` header**: `max-age=86400`

### Anycast vs Unicast ⭐
| | Unicast | Anycast |
|-|---------|---------|
| IP mapping | 1 IP → 1 server | **1 IP → multiple servers** |
| Routing | Fixed destination | Routes to **nearest** server |
| CDN use | No | Yes — low latency + DDoS resilience |

### CDN Benefits
1. **Performance**: reduced distance, minification, TLS false start
2. **Reliability**: failover, reduces origin load
3. **Security**: DDoS mitigation, TLS termination at edge, WAF

### AWS CloudFront
- 400+ Points of Presence (PoPs)
- **Two-tier caching**: Edge Locations → Regional Edge Caches → Origin
- **Lambda@Edge**: run code at edge for request/response manipulation
- **Cache behaviors**: configure different TTL/rules per URL path pattern

### GCP Cloud CDN ⭐ (exam trap)
- **Cannot be used standalone** — requires External HTTP(S) Load Balancer
- **Negative caching**:
  - 404 → cached for 120 s
  - 301/308 → cached for 10 min
  - 302/307 → **NOT cached**

### Cache Invalidation (Purge)
- **CloudFront Invalidation**: explicitly invalidate paths; first 1,000/month free
- **Cache Busting**: embed version hash in filename (e.g., `app.a3f9c2.js`) — browsers always fetch new file

### When CDN is NOT Suitable ⭐
- Personalized / user-specific content
- Real-time / live data
- Internal applications (intranet)
- Very low traffic sites

### Common Mistakes
- Forgetting `Cache-Control` header → CDN won't cache
- POST requests are **never cached** by default
- Not invalidating CDN cache after new deployment

---

## L08_03 — SRE

### Core Definition
> "SRE is what happens when a software engineer is tasked with what used to be called operations."  
> — Ben Treynor (Google)

SRE = software engineering applied to operations.  
用软件工程方法解决运维问题。

### SRE vs DevOps ⭐
- **DevOps** = a generalization / set of principles
- **SRE** = a **specific implementation** of DevOps principles

### Key Stat
> 40–90% of total system costs are incurred **after** the system goes live (production phase).

### 8 Tenets of SRE
1. Availability
2. Latency
3. Performance
4. Efficiency
5. Change management
6. Monitoring
7. Emergency response
8. Capacity planning

### Toil ⭐
**Toil** = work that is:
- Manual, repetitive, automatable
- **Scales linearly** with system growth
- Devoid of long-term value

SRE goal: **eliminate toil** through automation.

### Error Budget ⭐
```
Error Budget = 1 − Availability Target
```
- **100% availability is WRONG** — impossible and costs more than it's worth
- Each additional "nine" costs ~**100× more** than the previous
- 99.99% → ~52 min downtime/year; error budget = **0.01%**

### Availability Formulas
| Method | Formula |
|--------|---------|
| Time-based | `uptime / (uptime + downtime)` |
| Aggregate | `successful requests / total requests` |

### SLI / SLO / SLA ⭐

| Term | Full Name | Definition |
|------|-----------|-----------|
| **SLI** | Service Level Indicator | Quantitative metric: request latency, error rate, throughput |
| **SLO** | Service Level Objective | Internal target for an SLI (e.g., p99 latency ≤ 200 ms) |
| **SLA** | Service Level Agreement | **Contract** with customers; violating it has **financial consequences** |

**Key distinction**: If there is no explicit consequence (rebate/penalty) → it's an **SLO**, not an SLA.

### Postmortem
- **Blameless** written record of an incident
- Includes: impact, timeline, root causes, action items
- Rule: **"No Postmortem Left Unreviewed"**

---

## L08_04 — Observability

### Definition
**Observability = Logging + Metrics + Monitoring + Alerting**  
可观测性 = 日志 + 指标 + 监控 + 告警

**Centralized Logging**: aggregate logs from ALL servers into one location (e.g., AWS CloudWatch Logs).

### Log Levels (order matters) ⭐
```
ALL < TRACE < DEBUG < INFO < WARN < ERROR < FATAL < OFF
```
**Rule**: a log request at level **p** is enabled if **p ≥ q** (the logger's configured level).  
Example: logger set to WARN → only WARN, ERROR, FATAL appear.

### Production Logging Rules ⭐
- Minimum production level = **INFO**
- **DEBUG must NOT appear in production**
- Use **guarded logging**: check `isDebugEnabled()` before assembling expensive log messages
- Timestamps in **UTC/GMT**
- Format: **JSON preferred**
- **Text format only** (no binary)
- **English only**
- **NEVER log PII**: no passwords, credit card numbers, SSNs

### Metric Data Types ⭐

| Type | Description | Example |
|------|-------------|---------|
| **Counter** | Monotonically increasing; track rate of change | Total requests served |
| **Gauge** | Arbitrary value, can go up or down | Current CPU usage, memory |
| **Timer** | Measures durations | API response time, p99 latency |

### Metric Backend Datastores
- Graphite, InfluxDB, OpenTSDB, **Prometheus**

### Alert Severity Levels ⭐

| Level | Action |
|-------|--------|
| **Low Severity** | Record only, no immediate action |
| **Moderate Severity** | Send email notification, not urgent |
| **High Severity** | **Immediate human intervention** required; can impact customer SLAs |

---

## ⭐ High-Frequency Exam Traps (All 4 PPTs)

| Statement | True / False | Reason |
|-----------|-------------|--------|
| DNS is a single centralized server | **False** | Globally distributed, hierarchical system |
| CNAME can be placed at the apex domain | **False** | Cannot; use ALIAS (Route 53) instead |
| Lower MX priority number = higher priority | **True** | MX 10 is preferred over MX 20 |
| No CAA record means no CA can issue certs | **False** | No CAA = **any** CA can issue |
| 100% availability is the ideal SLO target | **False** | 100% is impossible; error budget = 0; costs explode |
| SLA and SLO are the same thing | **False** | SLA has financial consequences; SLO is internal |
| POST requests can be cached by CDN | **False** | POST is never cached by default |
| GCP Cloud CDN can be used independently | **False** | Requires External HTTP(S) Load Balancer |
| DEBUG logs should appear in production | **False** | Production minimum level = INFO |
| Anycast uses one IP per server | **False** | One IP → multiple servers, routes to nearest |
| PTR record lives under in-addr.arpa | **True** | Reverse DNS convention |
| A Counter metric can decrease | **False** | Counter is monotonically increasing only |

---

## Practice Questions

### Multiple Select — DNS Records
Which of the following DNS record types can be placed at the **apex/root domain**? (Select all that apply)

- A. CNAME  
- B. A  
- C. AAAA  
- D. ALIAS (Route 53)  
- E. MX

<details>
<summary>Answer</summary>

**B, C, D, E**  
CNAME cannot be at the apex domain. All others (A, AAAA, ALIAS, MX, NS, SOA) can be placed at root.
</details>

---

### True/False — SRE
**Statement**: An SLA and an SLO are interchangeable terms; both describe performance targets for a service.

<details>
<summary>Answer</summary>

**False.**  
An SLO is an *internal* target value for an SLI, with no contractual obligation. An SLA is a *contract* with customers that includes explicit financial consequences (rebates/penalties) if the target is missed.
</details>

---

### Scenario — CDN
**Alex is deploying a new version of a React SPA to production. The JS bundle has been updated, but users are still seeing the old version even after the deployment.**

What are the TWO most effective solutions?

- A. Increase the CDN TTL to force re-fetch  
- B. Add version hash to the bundle filename (cache busting)  
- C. Invalidate the CDN cache for the affected paths  
- D. Switch from CloudFront to GCP Cloud CDN  
- E. Disable HTTPS at the edge

<details>
<summary>Answer</summary>

**B and C**  
Cache busting (filename hash) ensures the browser requests a new URL so it bypasses the old cache. CDN cache invalidation forces the edge to discard the stale file immediately. A increases staleness. D and E are irrelevant.
</details>
