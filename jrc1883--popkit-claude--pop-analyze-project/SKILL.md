---
name: project-analysis-workflow
description: Analysis workflow finished Use when this capability is needed.
metadata:
  author: jrc1883
---

# Analyze Project

## Overview

Perform deep analysis of a codebase to understand its architecture, patterns, dependencies, and opportunities for improvement.

**Core principle:** Understand before changing. Map before navigating.

**Trigger:** `/popkit:project analyze` command or when starting work on unfamiliar project

## Arguments

| Flag             | Description                                                                 |
| ---------------- | --------------------------------------------------------------------------- |
| `--json`         | Output structured JSON instead of markdown, save to `.claude/analysis.json` |
| `--quick`        | Quick summary only (5-10 lines)                                             |
| `--focus <area>` | Focus analysis: `arch`, `deps`, `quality`, `patterns`                       |

## Analysis Areas

### 1. Project Structure

Directory structure, entry points, file counts by type.

### 2. Technology Stack

Package managers, frameworks (Next.js, React, Vue, Express, FastAPI, Rust), databases (Supabase, Prisma, MongoDB, PostgreSQL).

### 3. Architecture Patterns

Frontend (component structure, state management, routing), Backend (API design, service layer, database access), Common (error handling, logging, configuration).

### 4. Code Quality

Linting config, TypeScript strictness, TODO/FIXME comments.

### 5. Testing Coverage

Test files, test frameworks, coverage reports.

### 6. Dependencies

Count, outdated packages, security vulnerabilities.

### 7. CI/CD and DevOps

CI config, Docker, deployment configuration.

## Output Format

### Markdown Report

```markdown
# [Project Name] Analysis Report

## Summary

- **Type**: [Web App / API / CLI / Library]
- **Stack**: [Primary technologies]
- **Size**: [Files, Lines of code]
- **Health**: [Good / Needs attention / Critical issues]

## Technology Stack

### Frontend

- Framework: [Next.js 14 / React / Vue / etc.]
- Styling: [Tailwind / styled-components / etc.]
- State: [Redux / Zustand / Context]

### Backend

- Runtime: [Node.js / Python / Rust / Go]
- Framework: [Express / FastAPI / Actix]
- Database: [PostgreSQL / MongoDB / etc.]

### DevOps

- CI/CD: [GitHub Actions / GitLab CI / etc.]
- Deployment: [Vercel / AWS / etc.]
- Container: [Docker / etc.]

## Architecture

### Key Patterns

- [Pattern 1]: [Where used]
- [Pattern 2]: [Where used]

### Entry Points

- Main: `[path]`
- API: `[path]`
- Tests: `[path]`

## Code Quality

| Metric            | Value                | Status       |
| ----------------- | -------------------- | ------------ |
| Linting           | [Configured/Missing] | [OK/Warning] |
| TypeScript Strict | [Yes/No]             | [OK/Warning] |
| Test Coverage     | [X%]                 | [OK/Warning] |
| TODO Comments     | [N]                  | [OK/Warning] |

## Recommendations

### Critical

1. [Issue requiring immediate attention]

### High Priority

1. [Important improvement]

### Nice to Have

1. [Enhancement suggestion]

## Next Steps

1. Run `/generate-mcp` to create project-specific tools
2. Run `/generate-skills` to capture discovered patterns
3. Run `/setup-precommit` to configure quality gates
```

### JSON Output

When `--json` flag is provided, saves to `.claude/analysis.json`:

```json
{
  "project_name": "project-name",
  "project_type": "nextjs",
  "analyzed_at": "2026-01-30T00:00:00Z",
  "frameworks": [{ "name": "nextjs", "confidence": 0.95, "version": "14.0.0" }],
  "patterns": [
    {
      "name": "pattern-name",
      "category": "architecture",
      "confidence": 0.85,
      "examples": ["path1", "path2"],
      "description": "Description"
    }
  ],
  "recommended_skills": ["skill1", "skill2"],
  "recommended_agents": ["agent1", "agent2"],
  "commands": {},
  "quality_metrics": {}
}
```

## Skill/Agent Recommendations

Based on detected patterns:

| Pattern                | Recommended Skill        | Priority |
| ---------------------- | ------------------------ | -------- |
| nextjs + vercel-config | `project:deploy`         | high     |
| prisma OR drizzle      | `project:db-migrate`     | high     |
| supabase               | `project:supabase-sync`  | medium   |
| docker-compose         | `project:docker-dev`     | medium   |
| feature-flags          | `project:feature-toggle` | low      |

| Pattern                     | Recommended Agent        |
| --------------------------- | ------------------------ |
| Large codebase (>100 files) | `performance-optimizer`  |
| React/Vue components        | `accessibility-guardian` |
| API routes                  | `api-designer`           |
| Security-sensitive          | `security-auditor`       |
| Low test coverage           | `test-writer-fixer`      |

## Integration

**Called by:**

- Manual `/analyze-project` command
- After `/init-project`

**Informs:**

- **/generate-mcp** - What tools to create
- **/generate-skills** - What patterns to capture
- **/setup-precommit** - What checks to configure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jrc1883) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
