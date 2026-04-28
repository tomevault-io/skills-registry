---
name: documentation-research
description: Enforces documentation research before implementation. Auto-loads when implementing features to ensure current best practices are followed. Researches official docs first. Use when this capability is needed.
metadata:
  author: jpoutrin
---

# Documentation Research Skill

This skill enforces documentation research before any technical implementation to ensure code follows current best practices.

## Core Principle

**NO IMPLEMENTATION WITHOUT DOCUMENTATION RESEARCH**

Before writing ANY code:
1. Search official documentation online
2. Verify current best practices
3. Check for deprecated patterns
4. Report findings to user
5. Only then proceed

## Documentation Sources

| Technology | Primary Documentation |
|------------|----------------------|
| Django | docs.djangoproject.com |
| FastAPI | fastapi.tiangolo.com |
| React | react.dev |
| Python | docs.python.org |
| TypeScript | typescriptlang.org/docs |

## Research Protocol

1. **Search Official Docs** - Use WebSearch/WebFetch
2. **Verify Version** - Check latest stable release
3. **Review Best Practices** - Note recommended patterns
4. **Check Deprecations** - Avoid outdated APIs
5. **Document Findings** - Summarize before implementing

## Report Format

```
📚 Documentation Research Summary
══════════════════════════════════

🔍 Technology: [Framework]
📦 Version: [Version]

✅ CURRENT BEST PRACTICES
• [Practice 1]
• [Practice 2]

⚠️ DEPRECATED PATTERNS (Avoid)
• [Pattern] - Use [alternative] instead

📖 SOURCES
• [URL]

Ready to proceed? (yes/no)
```

## Enforcement Rules

1. Documentation research is non-negotiable
2. Always verify which version is being used
3. Check for deprecated APIs before using
4. Follow security best practices from docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpoutrin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
