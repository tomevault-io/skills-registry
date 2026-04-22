---
name: ralph-prd
description: Interactive PRD (Product Requirements Document) builder. Creates structured prd.json with stories, acceptance criteria, dependencies, and priorities. All JSON constructed safely via jq. Use when planning a new feature or project. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra PRD Builder

Create structured PRD documents for autonomous development.

### What this does

1. **Interactive story creation** — Guides through story definition
2. **Acceptance criteria** — Testable criteria for each story
3. **Dependency mapping** — Story dependencies for execution order
4. **Priority assignment** — Critical, high, medium, low
5. **Schema validation** — Validates against PRD JSON Schema
6. **Requirement analysis** — Runs requirement-analyzer skill for quality check

### Usage

```
/ralph-ultra:ralph-prd [--from-markdown FILE] [--validate]
```

### PRD Structure

```json
{
  "project_name": "my-project",
  "version": "1.0.0",
  "stories": [
    {
      "id": "STORY-001",
      "title": "User authentication",
      "description": "Implement JWT-based auth flow",
      "status": "pending",
      "priority": "critical",
      "acceptance_criteria": [
        "Users can register with email/password",
        "Login returns JWT token with 24h expiry",
        "Protected routes return 401 without valid token"
      ],
      "depends_on": [],
      "estimated_complexity": "medium"
    }
  ]
}
```

### Options

| Option | Description |
|--------|-------------|
| `--from-markdown` | Convert a markdown PRD into prd.json format |
| `--validate` | Validate existing prd.json against schema |

### Tips

- Keep stories small and independently verifiable
- Each acceptance criterion should be testable by automated tests
- Define dependencies to enable parallel execution where possible
- Use `ralph-ultra:ralph-skill requirement-analyzer` to validate after creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
