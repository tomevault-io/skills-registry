---
name: configuring-host-based-intrusion-detection
description: > Use when this capability is needed.
metadata:
  author: killvxk
---
# 配置基于主机的入侵检测

## 使用场景

在以下情况下使用本技能：
- 在 Windows 和 Linux 端点上部署 HIDS 代理（Wazuh、OSSEC、AIDE）
- 为合规要求（PCI DSS 11.5、NIST SI-7）配置文件完整性监控（FIM）
- 监控系统配置变更、Rootkit 检测和安全策略违规
- 将 HIDS 告警与 SIEM 平台集成以实现集中监控

**不适用于**基于网络的 IDS（Suricata、Snort）或 EDR 部署。

## 前置条件

- Wazuh 服务器（管理器）已部署且端点可访问
- 目标端点的管理员访问权限
- 网络连接：代理到 Wazuh 管理器的 1514 端口（TCP/UDP）和 1515 端口（TCP 注册）
- Wazuh 仪表板（OpenSearch Dashboards）用于告警可视化
- 了解每个操作系统中需要监控的关键文件/目录

## 操作流程

### 步骤 1：安装 Wazuh 代理

**Windows**：
```powershell
# 下载并安装 Wazuh 代理
Invoke-WebRequest -Uri "https://packages.wazuh.com/4.x/windows/wazuh-agent-4.9.0-1.msi" `
  -OutFile "wazuh-agent.msi"
msiexec /i wazuh-agent.msi /q WAZUH_MANAGER="wazuh-manager.corp.com" `
  WAZUH_REGISTRATION_SERVER="wazuh-manager.corp.com" WAZUH_AGENT_GROUP="windows-workstations"
net start WazuhSvc
```

**Linux（Debian/Ubuntu）**：
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" \
  > /etc/apt/sources.list.d/wazuh.list
apt-get update && apt-get install wazuh-agent -y
sed -i 's/MANAGER_IP/wazuh-manager.corp.com/' /var/ossec/etc/ossec.conf
systemctl daemon-reload && systemctl enable --now wazuh-agent
```

### 步骤 2：配置文件完整性监控（FIM）

编辑代理配置（`/var/ossec/etc/ossec.conf` 或 `C:\Program Files (x86)\ossec-agent\ossec.conf`）：

```xml
<syscheck>
  <!-- 扫描频率：每 12 小时 -->
  <frequency>43200</frequency>
  <scan_on_start>yes</scan_on_start>
  <alert_new_files>yes</alert_new_files>

  <!-- Linux 关键目录 -->
  <directories check_all="yes" realtime="yes">/etc</directories>
  <directories check_all="yes" realtime="yes">/usr/bin</directories>
  <directories check_all="yes" realtime="yes">/usr/sbin</directories>
  <directories check_all="yes" realtime="yes">/bin</directories>
  <directories check_all="yes" realtime="yes">/sbin</directories>
  <directories check_all="yes">/boot</directories>

  <!-- Windows 关键目录 -->
  <directories check_all="yes" realtime="yes">C:\Windows\System32</directories>
  <directories check_all="yes" realtime="yes">C:\Windows\SysWOW64</directories>
  <directories check_all="yes" realtime="yes">%PROGRAMFILES%</directories>

  <!-- Windows 注册表监控 -->
  <windows_registry>HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run</windows_registry>
  <windows_registry>HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce</windows_registry>
  <windows_registry>HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services</windows_registry>

  <!-- 忽略频繁变化的文件 -->
  <ignore>/etc/mtab</ignore>
  <ignore>/etc/resolv.conf</ignore>
  <ignore type="sregex">.log$</ignore>
</syscheck>
```

### 步骤 3：配置 Rootkit 检测

```xml
<rootcheck>
  <disabled>no</disabled>
  <frequency>43200</frequency>
  <rootkit_files>/var/ossec/etc/shared/rootkit_files.txt</rootkit_files>
  <rootkit_trojans>/var/ossec/etc/shared/rootkit_trojans.txt</rootkit_trojans>
  <system_audit>/var/ossec/etc/shared/system_audit_rcl.txt</system_audit>
  <check_dev>yes</check_dev>
  <check_files>yes</check_files>
  <check_if>yes</check_if>
  <check_pids>yes</check_pids>
  <check_ports>yes</check_ports>
  <check_sys>yes</check_sys>
  <check_trojans>yes</check_trojans>
  <check_unixaudit>yes</check_unixaudit>
</rootcheck>
```

### 步骤 4：配置日志分析规则

```xml
<!-- 自定义规则：/var/ossec/etc/rules/local_rules.xml -->
<group name="local,syscheck,">
  <!-- 对关键二进制文件修改发出告警 -->
  <rule id="100001" level="12">
    <if_sid>550</if_sid>
    <match>/usr/bin/|/usr/sbin/|/bin/|/sbin/</match>
    <description>关键系统二进制文件已被修改：$(file)</description>
    <group>syscheck,pci_dss_11.5,</group>
  </rule>

  <!-- 对临时目录中出现的新可执行文件发出告警 -->
  <rule id="100002" level="10">
    <if_sid>554</if_sid>
    <match>/tmp/|/var/tmp/</match>
    <description>在临时目录中创建了新文件：$(file)</description>
    <group>syscheck,malware,</group>
  </rule>

  <!-- 对 SSH 配置更改发出告警 -->
  <rule id="100003" level="10">
    <if_sid>550</if_sid>
    <match>/etc/ssh/sshd_config</match>
    <description>SSH 配置已被修改</description>
    <group>syscheck,authentication,</group>
  </rule>
</group>
```

### 步骤 5：配置主动响应

```xml
<!-- 多次认证失败后自动封锁 IP -->
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>5712</rules_id>
  <timeout>600</timeout>
</active-response>

<!-- 检测到暴力破解后禁用账号 -->
<active-response>
  <disabled>no</disabled>
  <command>disable-account</command>
  <location>local</location>
  <rules_id>100100</rules_id>
  <timeout>3600</timeout>
</active-response>
```

### 步骤 6：与 SIEM 集成

```
# Wazuh 通过 Filebeat 接入 Splunk
# 编辑 /etc/filebeat/filebeat.yml：
filebeat.inputs:
  - type: log
    paths:
      - /var/ossec/logs/alerts/alerts.json
    json.keys_under_root: true
output.elasticsearch:
  hosts: ["https://splunk-hec:8088"]

# Wazuh 直接集成 Elastic
# Wazuh 索引器直接接入 OpenSearch/Elasticsearch
# 仪表板：https://wazuh-dashboard:5601
```

## 关键概念

| 术语 | 定义 |
|------|------|
| **HIDS** | 基于主机的入侵检测系统（Host-based Intrusion Detection System），监控单个端点的恶意活动 |
| **FIM** | 文件完整性监控（File Integrity Monitoring），通过比对加密哈希值检测文件的未授权变更 |
| **Syscheck** | Wazuh/OSSEC 模块，用于文件完整性监控和注册表监控 |
| **Rootcheck** | Wazuh/OSSEC 模块，用于 Rootkit 和恶意软件检测 |
| **主动响应** | 由 HIDS 告警触发的自动防御措施（封锁 IP、禁用账号） |
| **CDB List** | 常量数据库列表，用于 Wazuh 规则中的自定义查找 |

## 工具与系统

- **Wazuh**：开源 HIDS 平台（OSSEC 的分支），包含管理器、代理和仪表板
- **OSSEC**：原始开源 HIDS（Wazuh 的前身）
- **AIDE（高级入侵检测环境）**：Linux 独立文件完整性检查工具
- **Tripwire**：商业文件完整性监控解决方案
- **Samhain**：专注于文件完整性和日志监控的开源 HIDS

## 常见误区

- **监控目录过多**：对整个文件系统进行 FIM 会产生过多告警。专注于关键系统二进制文件、配置文件和 Web 根目录。
- **未排除高噪声文件**：频繁变化的文件（日志、临时文件、缓存）会产生误报 FIM 告警。维护好排除列表。
- **忽视基线建立**：首次 FIM 扫描会创建基线。在基线稳定之前检测到的变更是噪声而非威胁，建议留出 48 小时让基线趋于稳定。
- **未经测试的主动响应**：自动封锁 IP 或禁用账号可能导致服务中断。在非生产环境中先测试主动响应规则。
- **代理注册失败**：代理必须成功向管理器注册才能开始监控。验证防火墙规则是否允许 1514 和 1515 端口的流量。

---
> Source: [killvxk/cybersecurity-skills-zh](https://github.com/killvxk/cybersecurity-skills-zh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
