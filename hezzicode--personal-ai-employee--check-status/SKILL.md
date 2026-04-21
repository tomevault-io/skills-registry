---
name: check-status
description: Use when user requests to check AI Employee status, system health, pending approvals, or dashboard summary.
metadata:
  author: hezzicode
---

# Check Status Skill

Display current state of AI Employee operations, system health, and pending actions.

## Workflow

1. **Read Dashboard** from `Dashboard.md`
2. **Check Folders**:
   - `/Needs_Action/` - Waiting to be processed
   - `/Pending_Approval/` - Awaiting human decision
   - `/In_Progress/` - Currently being worked on
   - `/Logs/` - Recent activity
3. **Report**:
   - Active tasks count
   - Pending approvals count
   - Recent completions
   - System health status
   - Any errors or alerts

## Output Format

```
AI EMPLOYEE STATUS REPORT
=========================

📊 Dashboard
- Revenue MTD: $X / $X target
- Tasks completed today: N
- Pending items: N

⏳ Pending Approvals
- [N items awaiting decision]

🔄 In Progress
- [Currently active tasks]

⚠️ Alerts
- [Any system warnings]

📈 Recent Activity
- [Last 5 completions]
```

## Script Location

`~/.claude/skills/check-status/scripts/check_status.py`

## References

See `references/dashboard_schema.md` for file structures.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hezzicode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
