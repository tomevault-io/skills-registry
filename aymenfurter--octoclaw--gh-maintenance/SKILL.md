---
name: gh-maintenance
description: | Use when this capability is needed.
metadata:
  author: aymenfurter
---

# GitHub Scheduled Maintenance Skill

Check the GitHub Status API for planned and in-progress maintenance windows.

## Steps

### 1. Fetch Upcoming Maintenance

```bash
curl -s https://www.githubstatus.com/api/v2/scheduled-maintenances/upcoming.json
```

For each scheduled maintenance, extract:
- `name` -- maintenance title
- `scheduled_for` -- start time
- `scheduled_until` -- end time
- `components` -- affected services
- `incident_updates` -- description of what will happen

### 2. Fetch Active Maintenance

```bash
curl -s https://www.githubstatus.com/api/v2/scheduled-maintenances/active.json
```

If maintenance is currently in progress, extract the same fields plus current status.

### 3. Present the Maintenance Schedule

```markdown
## GitHub Scheduled Maintenance

### Currently Active
<If any maintenance is in progress, show details. Otherwise: "No active maintenance.">

### Upcoming
| When | Duration | Affected Services | Description |
|------|----------|-------------------|-------------|
| <date/time> | <calculated> | <components> | <name> |

<If no upcoming maintenance: "No scheduled maintenance windows.">
```

### 4. Summary

Tell the user:
- Whether any maintenance is happening right now
- When the next maintenance window is (if any)
- Which services will be affected
- Suggest planning around the maintenance window if it overlaps with working hours

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aymenfurter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
