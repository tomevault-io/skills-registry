---
name: correlating-security-events-in-qradar
description: > Use when this capability is needed.
metadata:
  author: killvxk
---
# 在 QRadar 中关联安全事件

## 适用场景

以下情况使用本技能：
- SOC 分析师需要调查 QRadar 告警并跨多个日志源关联事件
- 检测工程师构建自定义关联规则以识别多阶段攻击
- 需要告警调优以减少误报告警并提升信号质量
- 团队从基础事件监控迁移到基于行为的关联

**不适用于**日志源接入或解析——这需要 QRadar 管理员权限和 DSM 编辑器知识。

## 前置条件

- IBM QRadar SIEM 7.5+ 并启用告警管理
- 了解 AQL 用于即席事件和流量查询
- 日志源已通过正确的 QID 映射规范化（Windows、防火墙、代理、端点）
- 具有告警管理、规则创建和 AQL 搜索权限的用户角色
- 已为白名单和监控列表管理配置参考集/映射

## 工作流程

### 步骤 1：使用 AQL 调查告警

在 QRadar 中打开告警并使用 AQL（Ariel Query Language）查询相关事件：

```sql
SELECT DATEFORMAT(startTime, 'yyyy-MM-dd HH:mm:ss') AS event_time,
       sourceIP, destinationIP, username,
       LOGSOURCENAME(logSourceId) AS log_source,
       QIDNAME(qid) AS event_name,
       category, magnitude
FROM events
WHERE INOFFENSE(12345)
ORDER BY startTime ASC
LIMIT 500
```

以源 IP 为轴心查找所有活动：

```sql
SELECT DATEFORMAT(startTime, 'yyyy-MM-dd HH:mm:ss') AS event_time,
       destinationIP, destinationPort, username,
       QIDNAME(qid) AS event_name,
       eventCount, category
FROM events
WHERE sourceIP = '192.168.1.105'
  AND startTime > NOW() - 24*60*60*1000
ORDER BY startTime ASC
LIMIT 1000
```

### 步骤 2：构建自定义关联规则

创建检测暴力破解后成功登录的多条件规则：

**规则 1 — 暴力破解检测（构建块）：**
```
规则类型：Event
规则名称：BB: 同一源 IP 多次登录失败
条件：
  - 当事件由一个或多个本地实体检测到
  - 且当事件 QID 为 [Authentication Failure (5000001)] 之一
  - 且在 5 分钟内来自同一源 IP 的事件数量不少于 10 次
规则动作：分发新事件（类别：Authentication，QID：Custom_BruteForce）
```

**规则 2 — 暴力破解成功（关联规则）：**
```
规则类型：Offense
规则名称：COR: 暴力破解后随即成功登录
条件：
  - 当事件匹配构建块 BB: 同一源 IP 多次登录失败
  - 且在 10 分钟内从同一源 IP 检测到 QID [Authentication Success (5000000)] 的事件
  - 且两个事件的目标 IP 相同
规则动作：创建告警，严重级别设为高，相关性设为 8
```

### 步骤 3：使用 AQL 进行跨源关联

关联认证失败与网络流量以检测横向移动：

```sql
SELECT e.sourceIP, e.destinationIP, e.username,
       QIDNAME(e.qid) AS event_name,
       e.eventCount,
       f.sourceBytes, f.destinationBytes
FROM events e
LEFT JOIN flows f ON e.sourceIP = f.sourceIP
  AND e.destinationIP = f.destinationIP
  AND f.startTime BETWEEN e.startTime AND e.startTime + 300000
WHERE e.category = 'Authentication'
  AND e.sourceIP IN (
    SELECT sourceIP FROM events
    WHERE QIDNAME(qid) = 'Authentication Failure'
      AND startTime > NOW() - 3600000
    GROUP BY sourceIP
    HAVING COUNT(*) > 20
  )
  AND e.startTime > NOW() - 3600000
ORDER BY e.startTime ASC
```

通过关联 DNS 查询与大量出站流量检测数据外泄：

```sql
SELECT sourceIP, destinationIP,
       SUM(sourceBytes) AS total_bytes_out,
       COUNT(*) AS flow_count
FROM flows
WHERE sourceIP IN (
    SELECT sourceIP FROM events
    WHERE QIDNAME(qid) ILIKE '%DNS%'
      AND destinationIP NOT IN (
        SELECT ip FROM reference_data.sets('Internal_DNS_Servers')
      )
      AND startTime > NOW() - 86400000
    GROUP BY sourceIP
    HAVING COUNT(*) > 500
  )
  AND destinationPort NOT IN (80, 443, 53)
  AND startTime > NOW() - 86400000
GROUP BY sourceIP, destinationIP
HAVING SUM(sourceBytes) > 104857600
ORDER BY total_bytes_out DESC
```

### 步骤 4：配置参考集用于上下文富化

创建用于动态白名单和监控列表的参考集：

```bash
# 通过 QRadar API 创建参考集
curl -X POST "https://qradar.example.com/api/reference_data/sets" \
  -H "SEC: YOUR_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Known_Pen_Test_IPs",
    "element_type": "IP",
    "timeout_type": "LAST_SEEN",
    "time_to_live": "30 days"
  }'

# 添加条目
curl -X POST "https://qradar.example.com/api/reference_data/sets/Known_Pen_Test_IPs" \
  -H "SEC: YOUR_API_TOKEN" \
  -d "value=10.0.5.100"
```

在规则条件中使用参考集排除已知良性活动：

```
条件：AND 当源 IP 不包含在任何 [Known_Pen_Test_IPs] 中时
条件：AND 当目标 IP 包含在任何 [Critical_Asset_IPs] 中时
```

### 步骤 5：调优告警生成

通过添加构建块过滤器减少误报：

```sql
-- 查找误报最多的触发源
SELECT QIDNAME(qid) AS event_name,
       LOGSOURCENAME(logSourceId) AS log_source,
       COUNT(*) AS event_count,
       COUNT(DISTINCT sourceIP) AS unique_sources
FROM events
WHERE INOFFENSE(
    SELECT offenseId FROM offenses
    WHERE status = 'CLOSED'
      AND closeReason = 'False Positive'
      AND startTime > NOW() - 30*24*60*60*1000
  )
GROUP BY qid, logSourceId
ORDER BY event_count DESC
LIMIT 20
```

应用调优：
- 将高频误报来源添加到参考集排除列表
- 提高噪声规则的事件阈值（如：服务账户将 10 次登录失败 -> 25 次）
- 设置告警合并以将相关事件归组到单个告警下

### 步骤 6：构建关联监控的自定义仪表板

创建包含关键关联指标的 QRadar Pulse 仪表板：

```sql
-- 按类别统计活跃告警
SELECT offenseType, status, COUNT(*) AS offense_count,
       AVG(magnitude) AS avg_magnitude
FROM offenses
WHERE status = 'OPEN'
GROUP BY offenseType, status
ORDER BY offense_count DESC

-- 告警平均关闭时间
SELECT DATEFORMAT(startTime, 'yyyy-MM-dd') AS day,
       AVG(closeTime - startTime) / 60000 AS avg_close_minutes,
       COUNT(*) AS closed_count
FROM offenses
WHERE status = 'CLOSED'
  AND startTime > NOW() - 30*24*60*60*1000
GROUP BY DATEFORMAT(startTime, 'yyyy-MM-dd')
ORDER BY day
```

## 核心概念

| 术语 | 定义 |
|------|------|
| **AQL** | Ariel Query Language——QRadar 用于搜索事件、流量和告警的 SQL 风格查询语言 |
| **Offense（告警）** | QRadar 将多个事件/流量关联到单个调查单元的关联事件分组 |
| **Building Block（构建块）** | 可复用规则组件，用于对事件分类但不生成告警，作为关联规则的输入 |
| **Magnitude（量级）** | QRadar 计算的告警严重程度，综合相关性、严重性和可信度得分（1-10） |
| **Reference Set（参考集）** | QRadar 中用于规则白名单、监控列表和富化数据的动态查询表 |
| **QID** | QRadar 标识符——将供应商特定事件映射到规范化类别的唯一数字 ID |
| **Coalescing（合并）** | QRadar 将相关事件分组为单个告警以减轻分析师工作量的机制 |

## 工具与系统

- **IBM QRadar SIEM**：具有事件关联、告警管理和 AQL 查询引擎的企业级 SIEM 平台
- **QRadar Pulse**：用于构建告警和事件指标自定义可视化的仪表板框架
- **QRadar API**：用于自动化参考集管理、告警操作和规则部署的 RESTful API
- **QRadar Use Case Manager**：将检测规则映射到 MITRE ATT&CK 框架覆盖范围的应用
- **QRadar Assistant**：帮助分析师用自然语言调查告警的 AI 驱动分析工具

## 常见场景

- **暴力破解到入侵**：将认证失败事件与来自同一源 IP 的随后成功登录关联
- **横向移动链**：追踪来自单一源的跨多台内部主机的认证事件
- **C2 信标**：将低熵载荷的周期性 DNS 查询与异常域名关联
- **权限提升**：将用户账户变更（加入组）与之前可疑的认证关联
- **数据外泄**：将大量出站流量与之前的内部侦察活动关联

## 输出格式

```
QRADAR 告警调查 — 告警 #12345
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
告警类型：   暴力破解后随即成功访问
量级：       8/10（严重性：8，相关性：9，可信度：7）
创建时间：   2024-03-15 14:23:07 UTC
贡献事件：   来自 3 个日志源的 247 个事件

关联链：
  14:10-14:22  — 234 次认证失败（EventCode 4625），192.168.1.105 -> DC-01
  14:23:07     — 认证成功（EventCode 4624），192.168.1.105 -> DC-01（用户：admin）
  14:25:33     — 新进程：DC-01 上由 admin 启动的 cmd.exe
  14:26:01     — 在 DC-01 上检测到 Net.exe user /add

关联来源：
  Windows 安全日志（DC-01）
  Sysmon（DC-01）
  防火墙（Palo Alto PA-5260）

处置结果：  真阳性 — 已升级至事件响应
工单编号：  IR-2024-0432
```

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
