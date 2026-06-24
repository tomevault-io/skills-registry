---
name: openevidence-incident-runbook
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Incident Runbook

## Severity
| Level | Condition | Response |
|-------|-----------|----------|
| P1 | API down | Immediate |
| P2 | Degraded | 15 min |
| P3 | Intermittent | 1 hour |

## Triage
1. Check OpenEvidence status page
2. Verify API key is valid
3. Test connectivity with curl
4. Check error logs for patterns

## Mitigation
- Enable cached/fallback responses
- Queue requests for retry
- Notify affected teams

## Resources
- [OpenEvidence Status](https://www.openevidence.com)

## Next Steps
See `openevidence-data-handling`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
