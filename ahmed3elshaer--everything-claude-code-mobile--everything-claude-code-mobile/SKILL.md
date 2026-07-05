---
name: continuous-learning-v2
description: Instinct-based learning system with confidence scoring for mobile patterns. Automatically extracts and evolves patterns. Use when this capability is needed.
metadata:
  author: ahmed3elshaer
---

# Continuous Learning v2

Instinct-based learning with confidence scoring.

## Instinct Structure

```json
{
  "id": "compose-state-hoisting",
  "type": "pattern",
  "description": "Always hoist state to caller in Composables",
  "confidence": 0.85,
  "examples": [...],
  "context": "jetpack-compose",
  "lastUsed": "2026-02-02"
}
```

## Confidence Scoring

| Score | Meaning |
|-------|---------|
| 0.0-0.3 | Experimental |
| 0.3-0.6 | Validated |
| 0.6-0.8 | Established |
| 0.8-1.0 | Best practice |

Confidence increases with:
- Successful application
- User acceptance
- Consistency across sessions

## Commands

```bash
/instinct-status        # View with confidence
/instinct-import <file> # Import from others
/instinct-export        # Export for sharing
/evolve                 # Cluster related instincts
```

## Mobile-Specific Instincts

Pre-configured patterns for:
- Compose recomposition optimization
- MVI state management
- Koin module organization
- Ktor error handling
- Espresso test patterns

---

**Remember**: Instincts evolve. Low confidence patterns may become best practices.

---
> Source: [ahmed3elshaer/everything-claude-code-mobile](https://github.com/ahmed3elshaer/everything-claude-code-mobile) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-05 -->
