---
name: format-python
description: description: Format Python code with Black Use when this capability is needed.
metadata:
  author: y1feng200156
---
---
name: format-python
description: Format Python code with Black
---

# Python Format Skill

## 📋 Overview

Use **Black** to automatically format Python code, an uncompromising code formatter:

- 🎨 **Unified style**: Automatic team code style consistency
- ⚡ **Fast execution**: Extremely fast formatting speed
- 🔒 **Deterministic**: Same code always produces same result
- 🔧 **Zero config**: Works out of the box

## 🔧 Prerequisites

| Tool | Min Version | Check Command | Installation |
|------|-------------|---------------|--------------|
| Python | 3.8+ | `python --version` | [python.org](https://python.org) |
| Black | 22.0+ | `black --version` | `pip install black` |

## 🚀 Usage

### Method 1: AI Assistant

```
"Use format-python to format my code"
```

### Method 2: Run Script Directly

```powershell
# Windows
.\.agent\skills\format-python\scripts\format.ps1

# Linux/Mac
./.agent/skills/format-python/scripts/format.sh
```

### Method 3: With Parameters

```powershell
# Check without modifying (preview mode)
.\.agent\skills\format-python\scripts\format.ps1 -Check

# Specify directory
.\.agent\skills\format-python\scripts\format.ps1 -Path ".\src"
```

## 🎯 What It Formats

- ✅ Indentation standardization (4 spaces)
- ✅ Line length limit (default 88 characters)
- ✅ String quote unification
- ✅ Bracket and comma normalization
- ✅ Import statement formatting

## ⚙️ Configuration

```toml
# pyproject.toml
[tool.black]
line-length = 88
target-version = ['py38']
include = '\.pyi?$'
extend-exclude = '''
/(
  \.git
  | \.venv
  | build
  | dist
)/
'''
```

## 🔗 Related Resources

- [Black Documentation](https://black.readthedocs.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/y1feng200156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
