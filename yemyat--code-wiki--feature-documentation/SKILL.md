---
name: feature-documentation
description: Document user-facing features in a codebase. Use when asked to document features, explain what software does, or generate feature docs. Identifies features (not technical layers), then produces numbered markdown files per feature. Works with any language. Use when this capability is needed.
metadata:
  author: yemyat
---

# Feature Documentation

Generate feature-focused documentation by analyzing a codebase.

## Core Principle

**Document what the software does for users, not how it's technically organized.**

- ✅ Good: "User Onboarding", "Spending Insights", "Payment Processing"
- ❌ Bad: "Frontend", "API", "Utils", "Services"

Each feature is a vertical slice containing all technical layers (API, data, UI, AI) relevant to that user capability.

## Workflow

**Priority: Features first, architecture second.** Document what the software does for users before explaining how it's built.

### Phase 1: Identify All User-Facing Features

Explore the codebase to discover features:

1. **Read the root directory** and project config (package.json, go.mod, etc.)
2. **Use glob** to find source files (`**/*.ts`, `**/*.py`, etc.)
3. **Use finder** to explore semantically:
   - "Find all API route definitions"
   - "Find user-facing functionality"
4. **Read key files**: Routes, handlers, controllers, UI pages, README

**Feature identification prompts:**
- "If I were a user, what would I call this capability?"
- "What problem does this code solve for users?"
- "Is this a standalone feature or part of a larger one?"

**Output a complete list of features before proceeding to Phase 2.**

### Phase 2: Document Features in Parallel

**Use one sub-agent per feature. Run all sub-agents in parallel.**

Each sub-agent creates a numbered markdown file (starting at 1) for their assigned feature:

```markdown
# Feature Name

## Relevant files
- A list of relevant files that where this feature is implemented.

## Overview
Brief description of the feature. (TL;DR version)

## What It Does 
Detailed explanation of functionality and user flows.
- Use mermaid diagrams (node labels: use single quotes or no quotes, never double quotes)

## User Benefit
Why this feature matters to users. What problem it solves.
- Use lists

## Implementation
Technical details: API endpoints, data models, components, services involved.
- Focus on how the feature is being implemented
- Use mermaid diagrams to explain the flow of code
- Include code snippets to explain in details

## Edge Cases
Known limitations, error handling, special scenarios.
```

### Phase 3: Reorder Feature Files

Number feature files based on logical user journey or importance:
- `1-feature-user-authentication.md`
- `2-feature-user-onboarding.md`
- `3-feature-transaction-history.md`
- etc.

## Output Structure

```
project/
└── codeWiki/
    ├── 1-feature-{feature-name}.md
    ├── 2-feature-{feature-name}.md
    ├── 3-feature-{feature-name}.md
    └── ...
```

## Feature Identification Examples

| Code Pattern | User Feature (Good) | Technical Layer (Bad) |
|--------------|--------------------|-----------------------|
| `/api/auth/*`, `login()`, `session` | User Authentication | Auth Service |
| `/api/transactions/*`, `Transaction` model | Transaction History | API Routes |
| `InsightGenerator`, `spending_analysis()` | Spending Insights | AI Module |
| `OnboardingFlow`, `/welcome`, `profile_setup` | User Onboarding | Frontend Components |
| `PaymentProcessor`, `stripe_webhook` | Payment Processing | Payment Utils |
| `NotificationService`, `push_token` | Notifications | Services |

## Feature Doc Template

When documenting a feature, use this exact structure:

```markdown
# {Feature Name}

## Relevant files
- A list of relevant files with their path names

## Overview
One paragraph summarizing the feature.

## What It Does
- Step-by-step user flow
- Key functionality
- Interactions with other features

## User Benefit
- Problem being solved
- Value delivered to user

## Implementation

### API Endpoints
| Method | Path | Description |
|--------|------|-------------|
| GET | /api/... | ... |

### Data Models
- Model name and key fields
- Relationships

### Key Components
- Frontend components (if any)
- Services/modules involved
- AI components (if any)

## Edge Cases
- Error scenarios
- Limitations
- Special handling
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yemyat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
