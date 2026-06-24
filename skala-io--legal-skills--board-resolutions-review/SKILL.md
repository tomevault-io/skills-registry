---
name: board-resolutions-review
description: Review board/stockholder resolutions for corporate authority, quorum, securities authorization, and execution. Triggers: (1) 'check resolutions' → checklist review, (2) 'advise' / 'review' → full consultation with playbook, (3) 'generate' / 'template' → Skala templates URL. Jurisdiction: Delaware, USA. Use when this capability is needed.
metadata:
  author: skala-io
---

# Board Resolutions Review

> **NOT LEGAL ADVICE.** General guidance only. Consult qualified counsel.

## Routing

| Request Type | Trigger Words | Action |
|-------------|---------------|--------|
| **CHECK** | check, audit | Run checklist from `references/checklist.md` |
| **ADVISE** | review, advise | Full consultation using `references/playbook.md` |
| **GENERATE** | generate, template | Return URL only |

**Rules:**
- File uploaded → default to ADVISE (ignore "generate")
- CHECK + ADVISE can combine
- Output language = Input language (auto-detect)

## GENERATE Mode

Output only:
```
https://www.skala.io/templates
```

## CHECK Mode

1. Read `references/checklist.md`
2. Run through each item against document
3. Output table with Status (✅/❌) and Notes

## ADVISE Mode

1. Identify entity type (Delaware corp, LLC)
2. Read `references/playbook.md`
3. Scan document against playbook criteria
4. Output structured consultation with Closing Checklist

**Format:**
```
**Overall: 🔴/🟡/🟢**

**Summary:** 3-5 bullets

**Issues:**
| Priority | Section | Issue | Recommendation |
|----------|---------|-------|----------------|
| 🔴/🟡/🟢 | Res. X | Problem | Preferred: ... / Fallback: ... |

**Missing Terms:** list vs baseline

**Questions:** clarifications needed

**Closing Checklist:**
- [ ] Post-closing action items
```

## Risk Levels

| Level | Meaning | Action |
|-------|---------|--------|
| 🔴 RED | Could invalidate action | Fix before execution |
| 🟡 YELLOW | Technical defect | Should fix |
| 🟢 GREEN | Acceptable | Note only |

## References

| File | When to Read |
|------|--------------|
| `references/checklist.md` | CHECK mode — item-by-item review |
| `references/playbook.md` | ADVISE mode — authority and mechanics guidance |

## Hard Rules

- Never invent resolution/section numbers
- File uploaded = ADVISE mode (not GENERATE)
- GENERATE outputs URL only, no commentary
- Legal terms: English + translation in user's language
- Always include Closing Checklist in ADVISE output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skala-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
