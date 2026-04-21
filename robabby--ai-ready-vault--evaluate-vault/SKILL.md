---
name: evaluate-vault
description: Analyze an Obsidian vault's AI-readiness with pass/fail checks. Use when assessing a new vault, auditing an existing vault, or verifying setup is complete. Returns a structured report with clear status indicators and actionable recommendations. Use when this capability is needed.
metadata:
  author: robabby
---

# Evaluate Vault

Check vault AI-readiness with pass/fail indicators.

## Checks Performed

| Check | Pass Condition | Priority |
|-------|---------------|----------|
| CLAUDE.md exists | File present at vault root | Critical |
| CLAUDE.md sections | Contains Overview, Structure, Preferences | Critical |
| Memory folders | All 4 type folders exist | High |
| Memory.md dashboard | Dashboard file present | High |
| Example memories | At least 1 memory file exists | Medium |
| Skills directory | skills/ folder present | Medium |

## Workflow

1. Check for CLAUDE.md
   - Does it exist at vault root?
   - Does it contain required sections?

2. Verify memory system structure
   - Does `Areas/AI/Memory/` exist?
   - Are all 4 type folders present? (Episodic, Semantic, Procedural, Strategic)
   - Is Memory.md dashboard present?
   - Are there any example memories?

3. Check for skills
   - Does skills/ directory exist?

4. Calculate score
   - Count passed checks out of total
   - Assign qualitative rating

5. Generate recommendations
   - Actionable steps for any failed checks
   - Next steps for improvement

## Scoring

| Score | Rating |
|-------|--------|
| 6/6 | Excellent |
| 5/6 | Good |
| 4/6 | Needs Work |
| <4/6 | Not Ready |

## Output Format

```
Evaluating vault AI-readiness...

✓ CLAUDE.md found at vault root
✓ CLAUDE.md contains required sections (Overview, Structure, Preferences)
✓ Memory system folders exist (Episodic, Semantic, Procedural, Strategic)
✓ Memory.md dashboard present
✗ No example memories found (optional but recommended)
✓ Skills directory found

AI-READINESS SCORE: 5/6 (Good)

Recommendations:
- Add 1-2 example memories to help Claude understand the format

Run /hydrate to start your first AI-ready session.
```

## Parameters

- `$ARGUMENTS` (optional): Path to vault root if not current directory

## Example

User: `/evaluate-vault`

Response:
"Evaluating vault AI-readiness...

✓ CLAUDE.md found at vault root
✓ CLAUDE.md contains required sections (Overview, Structure, Preferences)
✓ Memory system folders exist (Episodic, Semantic, Procedural, Strategic)
✓ Memory.md dashboard present
✓ Example memories found (4 files across memory types)
✓ Skills directory found

AI-READINESS SCORE: 6/6 (Excellent)

Your vault is fully AI-ready!

Run /hydrate to start your first AI-ready session."

---

User: `/evaluate-vault`

Response:
"Evaluating vault AI-readiness...

✗ CLAUDE.md not found at vault root
✗ Memory system folders missing
✗ Memory.md dashboard missing
✗ No example memories found
✗ Skills directory not found

AI-READINESS SCORE: 0/6 (Not Ready)

Recommendations:
- Run /init-claude-md to create CLAUDE.md
- Run /init-memory to set up the memory system
- Copy skills/ folder to your vault root

Run these commands in order, then re-run /evaluate-vault to verify."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
