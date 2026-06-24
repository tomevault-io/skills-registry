---
name: learn
description: Extract reusable patterns from sessions and save them as skills. Use after solving complex problems, discovering useful workarounds, or establishing project-specific conventions. Keywords: learn, pattern, extract, skill, knowledge, save, remember. Use when this capability is needed.
metadata:
  author: excatt
---

# Learn Skill

## Purpose
Analyze problem-solving patterns, debugging techniques, and workarounds from sessions to save them as reusable skills.

**Core Principle**: Recurring problem-solving → Pattern extraction → Skill creation → Reuse in future sessions

## Activation Triggers
- After solving complex errors
- When discovering useful workarounds
- When establishing project-specific conventions
- Session end knowledge consolidation
- Explicit user request: `/learn`, `remember this`, `save pattern`

---

## Pattern Extraction Focus

### Include ✅
| Category | Examples |
|----------|----------|
| **Error Resolution Patterns** | TypeScript type error fixes, build failure repairs |
| **Debugging Techniques** | Specific tool combinations, log analysis methods |
| **Library Quirks** | Undocumented behaviors, version-specific differences |
| **API Workarounds** | Rate limit bypasses, authentication patterns |
| **Project Conventions** | Naming rules, file structure, code style |
| **Architecture Decisions** | Pattern selection rationale, trade-offs |

### Exclude ❌
| Category | Reason |
|----------|--------|
| Simple typo fixes | No reuse value |
| One-time issues | External service outages, etc. |
| Syntax errors | Basic knowledge scope |
| Environment-specific configs | Lacks generalizability |

---

## Workflow

### Step 1: Session Analysis
```
/learn

🔍 Analyzing session...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Messages: 47
Tool calls: 89
Errors resolved: 3 cases
Main task: Auth system implementation
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step 2: Pattern Identification
```
💡 Extractable patterns found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. [HIGH VALUE] NextAuth + Prisma Session Type Extension
   - Problem: userId missing in Session type
   - Solution: Type extension in next-auth.d.ts
   - Reusability: ⭐⭐⭐⭐⭐

2. [MEDIUM VALUE] Supabase RLS Debugging Pattern
   - Problem: Empty results due to RLS policy
   - Solution: Test with service_role key, then fix policy
   - Reusability: ⭐⭐⭐⭐

3. [LOW VALUE] ESLint Rule Disable
   - Problem: unused-vars warning
   - Solution: .eslintrc modification
   - Reusability: ⭐⭐ (project-specific)

Select patterns to save [1,2,3 or all]:
```

### Step 3: Skill Document Generation
```
📝 Generating skill document...

File: ~/.claude/skills/learned/nextauth-prisma-session-type.md
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
# NextAuth + Prisma Session Type Extension

## Problem
Type error when accessing userId in NextAuth session
`Property 'userId' does not exist on type 'Session'`

## Solution
Create `types/next-auth.d.ts`:
\`\`\`typescript
import { DefaultSession } from "next-auth"

declare module "next-auth" {
  interface Session {
    user: {
      id: string
    } & DefaultSession["user"]
  }
}
\`\`\`

## When to Apply
- When using NextAuth + Prisma combination
- When adding custom fields to session

## Related
- NextAuth official docs: TypeScript section
- Prisma adapter configuration
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Skill saved
```

### Step 4: Confirmation
```
View saved skill? [y/N]
```

---

## Learned Skills Storage

### Storage Location
```
~/.claude/skills/learned/
├── nextauth-prisma-session-type.md
├── supabase-rls-debugging.md
├── react-hydration-mismatch-fix.md
└── vercel-edge-function-timeout.md
```

### Skill File Structure
```markdown
---
name: pattern-name
description: Brief description
learned_at: 2025-01-26
source_project: project-name
tags: [nextauth, prisma, typescript]
---

# Pattern Name

## Problem
Problem situation description

## Cause
Root cause analysis

## Solution
Resolution method (with code examples)

## When to Apply
Situations where this pattern applies

## Caveats
Warnings, edge cases

## Related
Related documentation, resource links
```

---

## Auto-Learning (Stop Hook)

### Automatic Session End Analysis
`.claude/settings.json`:
```json
{
  "hooks": {
    "Stop": [
      {
        "type": "command",
        "command": "~/.claude/scripts/evaluate-session.sh"
      }
    ]
  }
}
```

### Auto-Learning Configuration
`.claude/learn.config.json`:
```json
{
  "auto_learn": {
    "enabled": true,
    "min_session_length": 10,
    "extraction_threshold": "medium",
    "auto_approve": false
  },
  "storage": {
    "path": "~/.claude/skills/learned/",
    "max_skills": 100
  },
  "filters": {
    "ignore_patterns": [
      "typo",
      "syntax_error",
      "one_time_fix"
    ],
    "focus_patterns": [
      "error_resolution",
      "debugging_technique",
      "workaround",
      "architecture_decision"
    ]
  }
}
```

---

## Integration

### With PM Agent
Integration with PM Agent's self-improvement layer:
```
Session complete
    │
    ├─→ /learn (pattern extraction)
    │
    └─→ PM Agent (documentation, knowledge base update)
```

### With `/checkpoint`
```
/checkpoint create "before-experiment"
... experimental resolution attempts ...
... success! ...
/learn  # Save successful pattern
```

### With Future Sessions
Saved skills auto-referenced when similar problems occur:
```
🔍 Similar pattern detected
━━━━━━━━━━━━━━━━━━━━━━━━━━
Previously learned pattern available:
- nextauth-prisma-session-type.md

Apply? [y/N]
```

---

## Quality Filters

### Value Assessment
| Criterion | Weight |
|-----------|--------|
| Resolution complexity | 30% |
| Reusability potential | 40% |
| Time-saving impact | 20% |
| Documentation value | 10% |

### Extraction Threshold
- **Low**: Extract most patterns (noisy)
- **Medium**: Medium+ value only (recommended)
- **High**: High-value patterns only (strict)

---

## Commands

| Command | Description |
|---------|-------------|
| `/learn` | Analyze current session and extract patterns |
| `/learn list` | List saved skills |
| `/learn show <name>` | View specific skill content |
| `/learn delete <name>` | Delete skill |
| `/learn search <keyword>` | Search skills |

---

## Best Practices

### Good Pattern Example
```markdown
# React Server Component Data Fetching

## Problem
Waterfall issue when fetching server data
in client components

## Solution
Fetch data in server component, pass as props
or use Suspense + parallel fetch

## Code Example
\`\`\`tsx
// ✅ Good: Server Component
async function Page() {
  const data = await fetchData()
  return <ClientComponent data={data} />
}
\`\`\`
```

### Pattern to Avoid
```markdown
# Fix Typo in Config  ❌
## Problem
Build failure due to typo
## Solution
Fix typo

→ No reuse value, do not save
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/excatt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
