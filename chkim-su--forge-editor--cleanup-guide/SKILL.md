---
name: cleanup-guide
description: This skill is automatically called by `/forge-editor:wizard validate` via W036 check. Use when this capability is needed.
metadata:
  author: chkim-su
---
---
name: cleanup-guide
description: Detect unnecessary files and provide cleanup recommendations
allowed-tools: ["Read", "Glob", "Grep", "Bash"]
---

# Cleanup Guide

Detect and clean unnecessary files in plugin/project directories.

## File Categories

| Category | Risk | Examples | Action |
|----------|------|----------|--------|
| SENSITIVE | HIGH | `.env`, `*.pem`, `credentials.json` | Immediate gitignore + secret rotation |
| DELETE | MEDIUM | `*.log`, `__pycache__`, `node_modules` | Remove with cleanup commands |
| GITIGNORE | LOW | `.DS_Store`, `.idea/` | Add to .gitignore |

## Quick Scan

```bash
# Run validation with W036 check
python3 scripts/validate_all.py . 2>&1 | grep -A 50 "W036"
```

## Detailed Patterns

See `references/file-patterns.md` for complete pattern list.

## Auto-Cleanup Commands

After identifying issues, generate cleanup script:

```bash
# Logs
rm -f *.log firebase-debug.log npm-debug.log

# Caches
rm -rf __pycache__ .pytest_cache .mypy_cache .cache

# Large dirs (careful!)
rm -rf node_modules

# Update gitignore
cat >> .gitignore << 'EOF'
*.log
__pycache__/
.DS_Store
.env
EOF
```

## Integration

This skill is automatically called by `/forge-editor:wizard validate` via W036 check.

---
> Source: [chkim-su/forge-editor](https://github.com/chkim-su/forge-editor) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
