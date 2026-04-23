---
name: quizapp-docs
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

## When to Use

Use this skill when writing QuizApp documentation for:
- Feature walkthroughs (frontend, backend, shared)
- Quiz domain guides (attempt lifecycle, learning mode, history)
- Tutorials, troubleshooting steps, or Devbox/infra docs
- Release notes or change logs under `docs/`

## Brand Voice

### Inclusive, Direct Tone
- Talk directly to the reader (“Run `pnpm dev`…”), avoid passive voice when possible.
- Use neutral, inclusive language (no gender assumptions, no militaristic metaphors).
- Explain QuizApp-specific terms on first use: “Quiz Attempt Session (QAS) stores the randomized order…”.
- Focus on clarity over marketing language—docs teach, not sell.

## Formatting Standards

### Title Case Capitalization
Use Title Case for all headers:
- Good: "How to Configure Security Scanning"
- Bad: "How to configure security scanning"

### Hyphenation
- Prenominal position: "world-leading company"
- Postnominal position: "features built in"

### Bullet Points
Use when information can be logically divided:
```markdown
QuizApp attempts include:
* **Randomization:** Stable order per attempt
* **Persistence:** Active attempts stored in `quizapp:activeAttemptByQuiz`
* **History:** Completed attempts saved immutably
```

### Interaction Verbs
- Desktop: Click, Double-click, Right-click, Drag, Scroll
- Touch: Tap, Double-tap, Press and hold, Swipe, Pinch

### Mintlify Components
- Prefer `<Info>`, `<Warning>`, `<Danger>` for callouts instead of bold text.
- Use `<Steps>` when describing procedures; keep each `<Step>` short (3–6 sentences).
- `<Columns>` + `<Card>` help summarize related resources (e.g., docs/use-cases.md, docs/wireframes.yaml).

## SEO Optimization

### Sentence Structure
Place keywords at the beginning:
- Good: "To create a custom role, open a terminal..."
- Bad: "Open a terminal to create a custom role..."

### Headers
- H1: Primary (unique, descriptive)
- H2-H6: Subheadings (logical hierarchy)
- Include keywords naturally

## MDX Components

### Version Badge
```mdx
import { VersionBadge } from "/snippets/version-badge.mdx"

## New Feature Name

<VersionBadge version="4.5.0" />

Description of the feature...
```

### Warnings and Danger Calls
```mdx
<Warning>
Disabling encryption may expose sensitive data to unauthorized access.
</Warning>

<Danger>
Running this command will **permanently delete all data**.
</Danger>
```

## Prowler Features (Proper Nouns)

Reference without articles:
- QuizApp Frontend, QuizApp Backend, QuizApp Shared
- Learning Mode, Quiz Attempt Session, Attempt History
- QuizApp Devbox Environment
- QuizApp AI Skills System
- Turborepo Pipeline, pnpm Workspace

## Content Requirements

- Always cross-link to the canonical sources:
  - Behavior → `docs/use-cases.md`
  - Architecture or package responsibilities → `docs/architecture.md`
  - UI flows → `docs/wireframes.yaml`
  - AI guardrails → `docs/developer-guide/introduction.mdx` + relevant Skills
- Mention which Skills contributors should load when the topic is scoped (e.g., `quizapp-ui`, `quizapp-api`).
- Include code snippets or commands only after running them locally.
- Use diagrams (Mermaid) when describing flows across packages or the Skill system.

## Local Preview Workflow

1. `pnpm install`
2. Install Mintlify CLI (`npm install -g mintlify`)
3. Run `mint dev` from repo root or inside `docs/`
4. Verify the edited page at `http://localhost:3000`
5. Run `pnpm lint`, `pnpm format`, and relevant tests before opening a PR

## Documentation Structure

```
docs/
├── architecture.md
├── use-cases.md
├── wireframes.yaml
├── developer-guide/
│   └── ai-skills.mdx
└── developer-guide/
    ├── introduction.mdx
    └── documentation.mdx
skills/
└── quizapp-docs/
    ├── SKILL.md
    └── references/
        └── documentation-docs.md
```

## Documentation Structure

Use the structure above when adding new files. Keep names descriptive and aligned with the relevant package.

## Resources

- **Documentation**: See [references/documentation-docs.md](references/documentation-docs.md) for authoritative local paths and quick checklist.
- **Onboarding**: `docs/developer-guide/introduction.mdx` for contribution rules and AI flow.
- **Writing Workflow**: `docs/developer-guide/documentation.mdx` for formatting + Mintlify instructions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
