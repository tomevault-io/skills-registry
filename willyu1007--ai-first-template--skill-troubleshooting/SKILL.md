---
name: skill-troubleshooting
description: Troubleshooting common skill issues. Keywords: skill, troubleshooting, issues. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Troubleshooting

This document helps diagnose common issues in skill selection/suggestion.

---

## 1. Skill is never suggested

Common causes:
- Missing or overly narrow keywords/intent patterns
- Wrong scope inference (routing points to a different area)
- Path triggers do not match the actual target files

Fix:
- Add 1鈥? high-signal keywords
- Add a specific intent pattern
- Add a conservative path glob (e.g., `/modules/**` when appropriate)

---

## 2. Skill is suggested too often (noisy)

Common causes:
- Generic keywords (鈥渢est鈥? 鈥渇ix鈥? 鈥渦pdate鈥? without context
- Broad path globs
- Over-broad regex patterns

Fix:
- Remove generic keywords; replace with domain-specific terms
- Narrow path globs
- Reduce regex usage; keep patterns anchored

---

## 3. Conflicting skills suggested together

Fix:
- Adjust priorities or enforcement (if supported)
- Split one skill into narrower responsibilities
- Add an alignment doc under `/.system/skills/ssot/<scope>/alignment/` clarifying boundaries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
