---
name: coverage-boost
description: Generate tests to hit uncovered code paths Use when this capability is needed.
metadata:
  author: openclaw
---

# Coverage Boost

Your coverage is at 67% and PM wants 80%. This tool finds uncovered lines and generates tests for them.

## Quick Start

```bash
npx ai-coverage-boost ./src/utils/helpers.ts
```

## What It Does

- Analyzes existing test coverage
- Identifies uncovered code paths
- Generates targeted tests for gaps
- Focuses on edge cases you missed

## Usage Examples

```bash
# Boost coverage for a file
npx ai-coverage-boost ./src/utils/validators.ts

# Target a specific function
npx ai-coverage-boost ./src/api.ts --function processOrder

# Generate Jest tests
npx ai-coverage-boost ./src --framework jest
```

## What It Generates

- Unit tests for uncovered branches
- Edge case tests
- Error handling tests
- Boundary condition tests

## Requirements

Node.js 18+. OPENAI_API_KEY required. Existing coverage report helps.

## License

MIT. Free forever.

---

**Built by LXGIC Studios**

- GitHub: [github.com/lxgicstudios/ai-coverage-boost](https://github.com/lxgicstudios/ai-coverage-boost)
- Twitter: [@lxgicstudios](https://x.com/lxgicstudios)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
