---
name: feishu-attendance
description: Monitor Feishu (Lark) attendance records. Check for late, early leave, or absent employees and report to admin. Use when this capability is needed.
metadata:
  author: openclaw
---

# Feishu Attendance Skill

Monitor daily attendance, notify employees of abnormalities, and report summary to admin.

## Features
- **Smart Checks**: Detects Late, Early Leave, and Absence.
- **Holiday Aware**: Auto-detects holidays/weekends via `timor.tech` API.
- **Safe Mode**: Disables user notifications if holiday API fails (prevents spam).
- **Caching**: Caches user list (24h TTL) and holiday data for performance.
- **Reporting**: Sends rich interactive cards to Admin.

## Usage

```bash
# Check today's attendance (Default)
node index.js check

# Check specific date
node index.js check --date 2023-10-27

# Dry Run (Test mode, no messages sent)
node index.js check --dry-run
```

## Permissions Required
- `attendance:report:readonly`
- `contact:user.employee:readonly`
- `im:message:send_as_bot`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
