---
name: continuous-learning
description: Pattern extraction and skill generation for mobile development sessions. Automatically learns from your coding patterns. Use when this capability is needed.
metadata:
  author: ahmed3elshaer
---

# Continuous Learning for Mobile

Extract patterns from your mobile development sessions to create reusable skills.

## How It Works

1. **Session Hooks** - Post-session analysis extracts patterns
2. **Pattern Storage** - Patterns saved to `.claude/instincts/`
3. **Skill Evolution** - Related patterns cluster into skills

## Pattern Types

- **Compose patterns** - UI component structures, state management
- **Architecture decisions** - Module organization, layer patterns
- **Error handling** - API error mapping, fallback strategies
- **Testing patterns** - Test structure, mocking approaches
- **Build patterns** - Dependency management, configuration

## Commands

```bash
/learn                  # Extract patterns now
/instinct-status        # View learned patterns
/instinct-export        # Export for sharing
/evolve                 # Cluster into skills
```

## Integration

Runs automatically via hooks:
- `PreCompact` - Saves session context
- `Stop` - Extracts patterns from session

---

**Remember**: The more you code, the smarter your agent becomes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahmed3elshaer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
