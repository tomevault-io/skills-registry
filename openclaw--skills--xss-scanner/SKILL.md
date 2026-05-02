---
name: xss-scanner
description: Detect cross-site scripting vulnerabilities in your frontend code before they ship. Use when this capability is needed.
metadata:
  author: openclaw
---
# XSS Scanner

Detect cross-site scripting vulnerabilities in your frontend code before they ship.

## Quick Start

```bash
npx ai-xss-check
```

## What It Does

- Scans JavaScript/TypeScript for XSS vulnerabilities
- Detects unsafe innerHTML, eval, and DOM manipulation
- Identifies unescaped user input in templates
- Checks React dangerouslySetInnerHTML usage
- Provides fix suggestions for each finding

## Usage

```bash
# Scan current directory
npx ai-xss-check

# Scan specific files
npx ai-xss-check ./src/components
```

## When to Use

- Before security audits
- Reviewing third-party code
- Setting up CI security gates
- Training junior devs on XSS prevention

## Part of the LXGIC Dev Toolkit

One of 110+ free developer tools from LXGIC Studios. No paywalls, no sign-ups.

**Find more:**
- GitHub: https://github.com/lxgic-studios
- Twitter: https://x.com/lxgicstudios
- Substack: https://lxgicstudios.substack.com
- Website: https://lxgicstudios.com

## License

MIT. Free forever.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
