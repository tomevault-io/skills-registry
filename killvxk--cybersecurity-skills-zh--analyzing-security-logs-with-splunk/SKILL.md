---
name: analyzing-security-logs-with-splunk
description: > Use when this capability is needed.
metadata:
  author: killvxk
---

# 使用 Splunk 分析安全日志

## 适用场景

- 调查需要跨多个日志源进行关联的安全事件
- 使用已知 TTP 和 IOC 进行威胁狩猎
- 针对特定攻击模式构建检测规则
- 从分散的日志源中重建事件时间线
- 分析异常认证行为、横向移动或数据外泄模式

**不适用于**实时数据包级别分析；全流量分析请使用 Wireshark 或 Zeek。

## 前置条件

- 安装了 Enterprise Security（ES）应用的 Splunk Enterprise 或 Splunk Cloud
- 已接入的日志源：Windows 事件日志（通过 Splunk Universal Forwarder 或 WEF）、防火墙、代理、DNS、EDR、邮件网关
- 配置了 Splunk CIM（Common Information Model，通用信息模型）数据模型，用于规范化字段名
- 中级及以上 SPL 能力
- 在 Splunk 中拥有 `search` 和 `accelerate_search` 能力的基于角色的访问权限

## 工作流程

### 步骤 1：在 Splunk 中界定调查范围

根据事件分诊数据定义搜索参数：

```spl
| 设置初始调查范围
index=windows OR index=firewall OR index=proxy
  earliest="2025-11-14T00:00:00" latest="2025-11-16T00:00:00"
  (host="WKSTN-042" OR src_ip="10.1.5.42" OR user="jsmith")
| stats count by index, sourcetype, host
| sort -count
```

此查询确定哪些日志源包含调查时间段和受影响资产的相关数据。

### 步骤 2：分析认证事件

使用 Windows 安全事件日志调查可疑认证模式：

```spl
| 检测暴力破解和撞库攻击
index=windows sourcetype="WinEventLog:Security" EventCode=4625
  earliest=-24h
| stats count as failed_attempts, values(src_ip) as source_ips,
  dc(src_ip) as unique_sources by TargetUserName
| where failed_attempts > 10
| sort -failed_attempts

| 检测哈希传递（Logon Type 9 - NewCredentials）
index=windows sourcetype="WinEventLog:Security" EventCode=4624
  Logon_Type=9
| table _time, host, TargetUserName, src_ip, LogonProcessName

| 通过 RDP 检测横向移动
index=windows sourcetype="WinEventLog:Security" EventCode=4624
  Logon_Type=10
| stats count, values(host) as targets by TargetUserName, src_ip
| where count > 3
| sort -count
```

### 步骤 3：追踪进程执行

使用 Sysmon 日志重建进程执行链：

```spl
| 带父进程链的进程创建（Sysmon Event ID 1）
index=sysmon EventCode=1 host="WKSTN-042"
  earliest="2025-11-15T14:00:00" latest="2025-11-15T15:00:00"
| table _time, ParentImage, ParentCommandLine, Image, CommandLine, User, Hashes
| sort _time

| 检测可疑 PowerShell 执行
index=sysmon EventCode=1 Image="*\\powershell.exe"
  (CommandLine="*-enc*" OR CommandLine="*-encodedcommand*"
   OR CommandLine="*downloadstring*" OR CommandLine="*iex*")
| table _time, host, User, ParentImage, CommandLine
| sort _time

| 检测 LSASS 凭据转储
index=sysmon EventCode=10 TargetImage="*\\lsass.exe"
  GrantedAccess=0x1010
| table _time, host, SourceImage, SourceUser, GrantedAccess
```

### 步骤 4：分析网络活动

将网络日志与端点事件进行关联：

```spl
| 检测 C2 信标模式
index=proxy OR index=firewall dest_ip="185.220.101.42"
| timechart span=1m count by src_ip
| where count > 0

| 检测 DNS 隧道（向单一域名发送大量查询）
index=dns
| rex field=query "(?<subdomain>[^\.]+)\.(?<domain>[^\.]+\.[^\.]+)$"
| stats count, avg(len(query)) as avg_query_len by domain, src_ip
| where count > 500 AND avg_query_len > 40
| sort -count

| 检测大量数据传输（潜在外泄）
index=proxy action=allowed
| stats sum(bytes_out) as total_bytes by src_ip, dest_ip, dest_host
| eval total_MB=round(total_bytes/1024/1024,2)
| where total_MB > 100
| sort -total_MB
```

### 步骤 5：构建事件时间线

跨所有日志源重建统一时间线：

```spl
| 统一事件时间线
index=windows OR index=sysmon OR index=proxy OR index=firewall
  (host="WKSTN-042" OR src_ip="10.1.5.42" OR user="jsmith")
  earliest="2025-11-15T14:00:00" latest="2025-11-15T16:00:00"
| eval event_summary=case(
    sourcetype=="WinEventLog:Security" AND EventCode==4624, "登录: ".TargetUserName." 来自 ".src_ip,
    sourcetype=="WinEventLog:Security" AND EventCode==4625, "登录失败: ".TargetUserName,
    sourcetype=="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" AND EventCode==1,
      "进程: ".Image." 由 ".User." 执行",
    sourcetype=="proxy", "Web: ".http_method." ".url,
    1==1, sourcetype.": ".EventCode)
| table _time, sourcetype, host, event_summary
| sort _time
```

### 步骤 6：创建检测规则

将调查发现转化为持续性 Splunk 关联搜索：

```spl
| 关联搜索：Office 应用程序生成的 PowerShell
index=sysmon EventCode=1
  Image="*\\powershell.exe"
  (ParentImage="*\\winword.exe" OR ParentImage="*\\excel.exe"
   OR ParentImage="*\\outlook.exe")
| eval severity="high"
| eval mitre_technique="T1059.001"
| collect index=notable_events
```

## 核心概念

| 术语 | 定义 |
|------|------|
| **SPL（搜索处理语言）** | Splunk 的查询语言，用于搜索、过滤、转换和可视化机器数据 |
| **CIM（通用信息模型）** | Splunk 的字段规范化标准，将厂商特定字段名映射为通用名称，以支持跨源查询 |
| **Notable Event（显著事件）** | Splunk Enterprise Security 中基于关联搜索匹配而标记供分析师审查的事件 |
| **Data Model（数据模型）** | Splunk 中索引数据的结构化表示，支持加速搜索和基于透视的分析 |
| **Sourcetype（数据源类型）** | Splunk 中定义特定日志类型格式和解析规则的分类标签 |
| **Correlation Search（关联搜索）** | 持续运行的 Splunk 计划搜索，当条件满足时生成显著事件 |
| **Timechart（时间图表）** | SPL 命令，创建时序可视化图表以识别模式、异常和趋势 |

## 工具与系统

- **Splunk Enterprise Security（ES）**：高级 SIEM 应用，提供关联搜索、基于风险的告警和调查工作台
- **Splunk SOAR**：与 Splunk ES 集成的编排平台，用于自动化响应手册
- **Sysmon**：Microsoft 系统监控工具，提供详细的进程、网络和文件变更遥测，并接入 Splunk
- **Splunk Attack Analyzer**：自动化威胁分析工具，对可疑文件和 URL 进行沙箱分析，并将结果输入 Splunk
- **BOSS of the SOC（BOTS）**：SANS/Splunk 训练数据集，用于练习事件调查 SPL 查询

## 常见场景

### 场景：调查撞库攻击导致的账户接管

**场景背景**：安全运营收到告警：同一账户在 30 分钟内从地理位置分散的 IP 地址多次成功登录。

**方法**：
1. 查询受影响账户的 Event ID 4624，映射所有登录来源和时间
2. 使用 Splunk 查找表将登录 IP 与威胁情报源进行关联
3. 检查来自已认证会话的代理日志中的可疑活动
4. 搜索受损账户的横向移动（Event ID 4624 Type 3 到其他主机）
5. 构建时间线，展示撞库攻击尝试、成功登录及攻陷后活动
6. 创建关联搜索以检测其他账户上的类似模式

**常见陷阱**：
- 仅搜索最近 24 小时，而撞库攻击可能持续数周
- 未检查 VPN 日志（可能显示同一账户从不可能的地理距离进行认证）
- 未将不同时区的日志源的时间戳进行规范化

## 输出格式

```
SPLUNK 调查报告
============================
事件编号:         INC-2025-1547
分析人员:         [姓名]
调查时段:         2025-11-14 00:00 UTC - 2025-11-16 00:00 UTC

搜索范围
索引:             windows, sysmon, proxy, firewall, dns
主机:             WKSTN-042, SRV-FILE01
用户:             jsmith, svc-backup
源 IP:            10.1.5.42, 10.1.10.15

关键发现
1. [时间戳] - 通过钓鱼邮件初始入侵（Sysmon Event 1）
2. [时间戳] - C2 已建立（代理日志，检测到信标模式）
3. [时间戳] - 凭据窃取（Sysmon Event 10，LSASS 访问）
4. [时间戳] - 横向移动至 SRV-FILE01（Event 4624 Type 3）
5. [时间戳] - 数据暂存和外泄（代理 bytes_out 异常）

使用的 SPL 查询
[关键查询的编号列表及描述]

发现的检测盲区
- SRV-FILE01 上未部署 Sysmon（存在盲区）
- 代理日志缺少对 C2 域名的 SSL 检测
- PowerShell ScriptBlock 日志记录未启用

推荐检测规则
1. Office 生成 PowerShell 的关联搜索
2. LSASS 访问模式的阈值告警
3. 信标间隔网络流量的行为规则
```

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
