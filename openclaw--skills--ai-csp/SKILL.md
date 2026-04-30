---
name: csp-gen
description: Generate Content Security Policy headers from your codebase Use when this capability is needed.
metadata:
  author: openclaw
---

# CSP Generator

Scan your app and generate a proper Content Security Policy. Stop breaking your site with overly strict rules.

## Quick Start

```bash
npx ai-csp ./src
```

## What It Does

- Scans for external resources (scripts, styles, images)
- Identifies inline scripts that need hashes
- Generates a working CSP header
- Explains each directive

## Usage Examples

```bash
# Scan and generate CSP
npx ai-csp ./public ./src

# Generate for specific strictness
npx ai-csp ./src --strict

# Output as meta tag
npx ai-csp ./src --format meta
```

## Output Formats

- HTTP header format
- HTML meta tag
- Next.js config
- Nginx config snippet

## Requirements

Node.js 18+. OPENAI_API_KEY required.

## License

MIT. Free forever.

---

**Built by LXGIC Studios**

- GitHub: [github.com/lxgicstudios/ai-csp](https://github.com/lxgicstudios/ai-csp)
- Twitter: [@lxgicstudios](https://x.com/lxgicstudios)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
