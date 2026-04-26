---
name: creating-adrs
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# ADR Creator

## 6-Phase Process

| Phase         | Actions                                                                  |
| ------------- | ------------------------------------------------------------------------ |
| 1. Pre-Check  | Run `./scripts/pre-check.sh "$TITLE"` (uses shared scripts)              |
| 2. Template   | Run `$HOME/.claude/scripts/select-adr-template.sh "$TITLE"`              |
| 3. References | Gather project docs, issues, external resources                          |
| 4. Validate   | Check required sections (Title, Status, Context, Decision, Consequences) |
| 5. Index      | Auto-generate `adr/README.md`                                            |
| 6. Recovery   | Handle missing dirs, duplicates, missing sections                        |

## Template Selection

Use script to auto-select template:

```bash
TEMPLATE=$("$HOME/.claude/scripts/select-adr-template.sh" "$TITLE")
```

| Template             | Use Case                   | Required Sections          |
| -------------------- | -------------------------- | -------------------------- |
| technology-selection | Library, framework choices | Options (min 3), Pros/Cons |
| architecture-pattern | Structure, design policy   | Context, Consequences      |
| process-change       | Workflow, rule changes     | Before/After comparison    |
| deprecation          | Retiring technology        | Migration plan, Timeline   |

## Directory Structure

```text
adr/
├── README.md          # Auto-generated index
├── 0001-*.md         # Sequential numbering
└── 0002-*.md
```

## Rules

| Rule         | Detail                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------- |
| Immutability | Once accepted, never modify. To change, create a new ADR that supersedes it                 |
| Brevity      | Target ~80 lines. Context: 3 lines. Options: 3-5 lines each. Consequences: 2-3 bullets     |
| Confidence   | `- Confidence: {level} — {rationale}` in metadata. Level + reason in one line               |
| Reassessment | Optional `## Reassessment Triggers` section after Consequences                              |

### Confidence Levels

| Level  | When to use                                              |
| ------ | -------------------------------------------------------- |
| high   | All options evaluated, clear winner, team consensus      |
| medium | Some unknowns remain, limited production data            |
| low    | Best guess under constraints, significant unknowns exist |

## References

| Topic     | Resource                      |
| --------- | ----------------------------- |
| MADR      | <https://adr.github.io/madr/> |
| Fowler    | <https://martinfowler.com/articles/architecture-decision-record.html> |
| Skill     | `../adr/SKILL.md`             |
| Templates | `../../templates/adr/`        |
| Scripts   | `./scripts/`                  |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
