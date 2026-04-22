---
name: memory-guardian
description: This skill monitors system physical memory usage, alerts users with bilingual warnings Use when this capability is needed.
metadata:
  author: y1feng200156
---
---
name: memory-guardian
description: Cross-platform memory monitoring and cleanup skill for AI development environments
version: 1.0.0
author: Agents-MD Pro
---

# Memory Guardian Skill

> 🛡️ **内存守护者** - 跨平台内存监控与清理技能  
> **Memory Guardian** - Cross-platform memory monitoring and cleanup skill

## 功能描述 / Description

此技能用于监控系统物理内存使用情况，当内存使用率过高时向用户发出双语警告，
并提供安全的 Python/Node.js 进程清理功能。

This skill monitors system physical memory usage, alerts users with bilingual warnings
when memory usage is high, and provides safe Python/Node.js process cleanup.

## 使用场景 / Use Cases

- AI 开发工具（如 Antigravity）运行时内存监控
- 长时间开发会话中的内存管理
- 防止系统因内存耗尽而崩溃

## 命令 / Commands

### 检查内存状态

```bash
python .agent/skills/memory-guardian/scripts/monitor.py --check
```

### 启动后台监控

```bash
python .agent/skills/memory-guardian/scripts/monitor.py --daemon
```

### 手动清理进程

```bash
python .agent/skills/memory-guardian/scripts/cleanup.py
```

## 警告阈值 / Thresholds

| 级别 / Level | 内存使用率 / Usage | 行为 / Action |
|-------------|-------------------|---------------|
| 🟢 正常 / Normal | < 70% | 静默 / Silent |
| 🟡 注意 / Notice | 70-80% | 提示 / Notice |
| 🟠 警告 / Warning | 80-90% | 警告 / Warning |
| 🔴 严重 / Critical | ≥ 90% | 严重警告 / Critical |

## 配置 / Configuration

编辑 `config.yaml` 自定义阈值和行为：
Edit `config.yaml` to customize thresholds and behavior:

```yaml
thresholds:
  notice: 70
  warning: 80
  critical: 90
check_interval: 30  # seconds
```

## 依赖 / Dependencies

```
psutil>=5.9.0
plyer>=2.1.0
```

安装依赖 / Install dependencies:

```bash
pip install -r .agent/skills/memory-guardian/scripts/requirements.txt
```

## 注意事项 / Notes

- 仅监控物理内存，不考虑虚拟内存/Swap
- 仅清理 Python 和 Node.js 进程
- 清理操作需要用户确认，不会自动执行
- Only monitors physical RAM, ignores virtual memory/Swap
- Only cleans Python and Node.js processes
- Cleanup requires user confirmation, never automatic

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/y1feng200156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
