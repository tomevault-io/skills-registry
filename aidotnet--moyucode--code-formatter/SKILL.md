---
name: code-formatter
description: 格式化源代码文件，支持Python、JavaScript、JSON等语言，使用行业标准格式化工具。 Use when this capability is needed.
metadata:
  author: aidotnet
---

# Code Formatter Tool

## Description
Format source code files using industry-standard formatters like Black for Python, with support for multiple languages.

## Trigger
- `/format` command
- User needs to format code
- User wants consistent code style

## Usage

```bash
# Format Python file
python scripts/code_formatter.py file.py

# Format with specific line length
python scripts/code_formatter.py file.py --line-length 100

# Format JSON
python scripts/code_formatter.py config.json

# Check without modifying
python scripts/code_formatter.py file.py --check

# Format directory
python scripts/code_formatter.py ./src/ --recursive
```

## Tags
`format`, `code`, `python`, `black`, `style`

## Compatibility
- Codex: ✅
- Claude Code: ✅

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aidotnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
