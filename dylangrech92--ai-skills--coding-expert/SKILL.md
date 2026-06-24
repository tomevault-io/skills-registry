---
name: coding-expert
description: Use when navigating, exploring, or tracing relationships in any OOP codebase. Automatically detects the project language (PHP, Python, TypeScript) and routes to the correct language-specific code expert skill. Triggers on class lookups, inheritance tracing, call site searches, and symbol exploration.
metadata:
  author: dylangrech92
---

# Coding Expert — Language Router

Detects the project language and loads the correct specialist skill. Only one language skill enters context.

## Detect Language

Check the project root for these markers:

```bash
ls composer.json pyproject.toml setup.py setup.cfg tsconfig.json 2>/dev/null
```

| Marker found | Language | Read file |
|---|---|---|
| `composer.json` | PHP | `php.md` in this skill's directory |
| `pyproject.toml` or `setup.py` or `setup.cfg` | Python | `python.md` in this skill's directory |
| `tsconfig.json` | TypeScript | `typescript.md` in this skill's directory |

### Mixed projects

If multiple markers exist, count source files to pick the dominant language:

```bash
echo "PHP: $(find . -name '*.php' -not -path '*/vendor/*' | wc -l)"
echo "Python: $(find . -name '*.py' -not -path '*/.venv/*' -not -path '*/__pycache__/*' | wc -l)"
echo "TypeScript: $(find . -name '*.ts' -o -name '*.tsx' | grep -v node_modules | wc -l)"
```

If the user's query mentions a specific language or file, use that language regardless of dominance.

## Route

After detection, read the matching `.md` file from this skill's directory. Do NOT continue without it — the language-specific file contains the grep patterns and navigation steps you need.

---
> Source: [dylangrech92/ai-skills](https://github.com/dylangrech92/ai-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
