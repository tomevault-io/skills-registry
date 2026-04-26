---
name: trigger-scheduled-task
description: Immediately trigger scheduled task without waiting for scheduled time. When you need to test task execution or run task ahead of schedule. Use when this capability is needed.
metadata:
  author: openakita
---

# Trigger Scheduled Task

立即触发定时任务（不等待计划时间）。

## Parameters

| 参数 | 类型 | 必填 | 说明 |
|-----|------|-----|------|
| task_id | string | 是 | 任务 ID |

## Notes

- 不会影响原有的执行计划
- 适用于测试任务或提前运行

## Related Skills

- `list-scheduled-tasks`: 获取任务 ID
- `schedule-task`: 创建新任务

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openakita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
