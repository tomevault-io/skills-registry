---
name: tui-audit
description: Scans Go source code for TUI accessibility violations. Use when this capability is needed.
metadata:
  author: riggd
---

# TUI Audit Skill

This skill provides tools to audit the codebase for Bricks-DS TUI standards compliance.

## Capabilities
- Detect hardcoded ANSI escape codes.
- Detect direct usage of `fmt.Print` / `fmt.Println` to stdout (instead of `fmt.Fprint(os.Stderr)` for UI).

## Usage
Run the audit script to generate a JSON report of violations:

```bash
go run .agent/skills/tui-audit/scripts/audit.go
```

## Output
The script outputs a JSON array of violations:
```json
[
  {
    "file": "path/to/file.go",
    "line": 10,
    "severity": "CRITICAL",
    "message": "Hardcoded ANSI detected"
  }
]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riggd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
