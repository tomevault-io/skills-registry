---
name: documenting-architecture
description: Document project architecture, tech stack, and data flow. Use when setting up or updating architecture documentation. Use when this capability is needed.
metadata:
  author: avicdro
---

# Documenting Architecture

Create and maintain architecture documentation for AI agents and developers.

## When to use

- Starting a new project
- Major tech stack changes
- Adding new services or integrations
- Onboarding new team members or AI agents

## Architecture Document Structure

```markdown
# Project Architecture

## Project Goal
[2-3 lines describing the application purpose]

## Tech Stack

### Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| React      | 18.x    | UI      |

### Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js    | 20.x    | API     |

## Directory Structure
[Tree showing key directories]

## Data Flow
[Diagram or description of how data moves]

## Environment Variables
| Variable | Description | Required |
|----------|-------------|----------|
| API_KEY  | Service key | ✅       |
```

## Key Sections

1. **Tech Stack**: All technologies with versions
2. **Directory Structure**: Key folders and their purpose
3. **Data Flow**: How data moves through the system
4. **Environment Variables**: Required configuration
5. **Critical Dependencies**: Important packages

## Update Triggers

Update architecture.md when:
- Adding new frameworks or libraries
- Changing directory structure
- Adding new services or APIs
- Modifying data flow

## Best Practices

### ✅ Do

- Include specific versions
- Document WHY not just WHAT
- Keep current with actual codebase
- Use tables for scanability

### ❌ Avoid

- Outdated version numbers
- Missing critical integrations
- Overly detailed (link to docs instead)
- Implementation details (put in code comments)

## File Location

```
project/
└── .context/
    └── architecture.md  # Main architecture doc
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/avicdro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
