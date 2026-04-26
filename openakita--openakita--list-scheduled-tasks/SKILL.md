---
name: list-scheduled-tasks
description: List all scheduled tasks with their ID, name, type, status, and next execution time. When you need to check existing tasks, find task ID for cancel/update, or verify task creation. Use when this capability is needed.
metadata:
  author: openakita
---

# List Scheduled Tasks

列出所有定时任务。

## Parameters

| 参数 | 类型 | 必填 | 说明 |
|-----|------|-----|------|
| enabled_only | boolean | 否 | 是否只列出启用的任务，默认 false |

## Returns

- 任务 ID
- 名称
- 类型（reminder/task）
- 状态（enabled/disabled）
- 下次执行时间

## Examples

**列出所有任务**:
```json
{}
```

**只列出启用的任务**:
```json
{"enabled_only": true}
```

## Related Skills

- `schedule-task`: 创建新任务
- `cancel-scheduled-task`: 取消任务
- `update-scheduled-task`: 更新任务设置

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openakita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
