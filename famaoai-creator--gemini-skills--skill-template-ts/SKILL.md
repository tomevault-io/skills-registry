---
name: skill-name
description: Input file path Use when this capability is needed.
metadata:
  author: famaoai-creator
---

# __SKILL_NAME__

## Overview

Briefly describes the activity this skill performs in the third person. Focus on what it *does* (e.g., "Analyzes source code complexity").

## Usage

```bash
pnpm build
pnpm start -- [options]
```

## Runtime Policy

- This template is the default path for new skills.
- Keep `package.json` as `type: "module"`.
- Use package imports such as `@agent/core` and `@agent/core/secure-io`.
- Do not add source-adjacent shadow `.js` files.
- If CommonJS is absolutely required, switch to `skill-template-cjs` intentionally.

## Progressive Disclosure

- **[Detailed Reference](./REFERENCE.md)**: Full API documentation and advanced configuration.
- **[Examples](./EXAMPLES.md)**: Common use cases and input/output samples.

## Knowledge Protocol

- This skill adheres to the `knowledge/orchestration/knowledge-protocol.md`. It automatically integrates Public, Confidential (Company/Client), and Personal knowledge tiers, prioritizing the most specific secrets.
- Adheres to the [Sovereign Shield] write governance policy.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/famaoai-creator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
