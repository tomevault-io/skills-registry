---
name: format-js
description: description: Format JavaScript/TypeScript code with Prettier Use when this capability is needed.
metadata:
  author: y1feng200156
---
---
name: format-js
description: Format JavaScript/TypeScript code with Prettier
---

# JavaScript/TypeScript Format Skill

## 📋 Overview

Use **Prettier** to automatically format JavaScript and TypeScript code:

- 🎨 **Consistent style**: Unified formatting across multiple file types
- ⚡ **Fast execution**: Millisecond-level formatting speed
- 🔧 **Out of the box**: Reasonable default configuration
- 🌈 **Wide support**: JS, TS, JSX, TSX, JSON, CSS, etc.

## 🔧 Prerequisites

| Tool | Min Version | Check Command | Installation |
|------|-------------|---------------|--------------|
| Node.js | 16+ | `node --version` | [nodejs.org](https://nodejs.org) |
| Prettier | 2.8+ | `prettier --version` | `npm install -g prettier` |

## 🚀 Usage

### Method 1: AI Assistant

```
"Use format-js to format my JavaScript code"
```

### Method 2: Run Script Directly

```powershell
# Windows
.\.agent\skills\format-js\scripts\format.ps1

# Linux/Mac
./.agent/skills/format-js/scripts/format.sh
```

### Method 3: With Parameters

```powershell
# Check without modifying
.\.agent\skills\format-js\scripts\format.ps1 -Check

# Format specific file types
.\.agent\skills\format-js\scripts\format.ps1 -Extensions "js,ts,jsx,tsx"
```

## 🎯 What It Formats

- ✅ Indentation and spacing
- ✅ Quote unification (single/double)
- ✅ Line length limits
- ✅ Semicolon add/remove
- ✅ Bracket and comma normalization
- ✅ Arrow function formatting

## ⚙️ Configuration

```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80,
  "arrowParens": "avoid"
}
```

## 🔗 Related Resources

- [Prettier Documentation](https://prettier.io/)
- [Prettier Playground](https://prettier.io/playground/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/y1feng200156) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
