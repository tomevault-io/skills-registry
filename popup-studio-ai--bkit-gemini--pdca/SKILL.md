---
name: pdca
description: Execute PDCA {action} phase for feature '{feature} Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# PDCA Skill

> Unified Skill for managing PDCA cycle. Supports the entire Plan → Design → Do → Check → Act flow.

## Arguments

| Argument | Description | Example |
|----------|-------------|---------|
| `plan [feature]` | Create Plan document | `/pdca plan user-auth` |
| `design [feature]` | Create Design document | `/pdca design user-auth` |
| `do [feature]` | Do phase guide | `/pdca do user-auth` |
| `analyze [feature]` | Run Gap analysis (Check) | `/pdca analyze user-auth` |
| `iterate [feature]` | Auto improvement (Act) | `/pdca iterate user-auth` |
| `report [feature]` | Generate completion report | `/pdca report user-auth` |
| `archive [feature]` | Archive PDCA documents | `/pdca archive user-auth` |
| `status` | Show current status | `/pdca status` |
| `next` | Guide to next phase | `/pdca next` |

## Action Details

### plan (Plan Phase)
1. Check if `docs/01-plan/features/{feature}.plan.md` exists
2. If not, create based on plan.template.md
3. Create Task: `[Plan] {feature}`

### design (Design Phase)
1. Verify Plan document exists
2. Create `docs/02-design/features/{feature}.design.md`
3. Create Task: `[Design] {feature}` (blockedBy: Plan)

### do (Do Phase)
1. Verify Design document exists
2. Provide implementation guide
3. Create Task: `[Do] {feature}` (blockedBy: Design)

### analyze (Check Phase)
1. Call gap-detector agent
2. Compare Design vs implementation
3. Calculate Match Rate
4. Create Task: `[Check] {feature}`

### iterate (Act Phase)
1. Check results (when matchRate < 90%)
2. Call pdca-iterator agent
3. Auto-fix and re-verify
4. Max 5 iterations

### report (Completion Report)
1. Verify Check >= 90%
2. Call report-generator agent
3. Create completion report

## PDCA Flow

```
[Plan] ✅ → [Design] ✅ → [Do] ✅ → [Check] 🔄 → [Act] ⏳ → [Report] 📋
                                       ↑_________|
                                    (if < 90%)
```

## References

- `${extensionPath}/templates/plan.template.md`
- `${extensionPath}/templates/design.template.md`
- `${extensionPath}/templates/analysis.template.md`
- `${extensionPath}/templates/report.template.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
