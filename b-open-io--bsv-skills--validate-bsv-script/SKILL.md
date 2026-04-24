---
name: validate-bsv-script
description: This skill should be used when the user asks to "validate BSV script", "analyze locking script", "parse unlocking script", "check script opcodes", or needs to validate and analyze Bitcoin scripts using @bsv/sdk. Use when this capability is needed.
metadata:
  author: b-open-io
---

# Validate BSV Script

Validate and analyze BSV scripts for correctness, identify script types, and check for security issues.

## When to Use

- Validate a locking or unlocking script
- Identify script type (P2PKH, P2PK, multisig, etc.)
- Analyze script opcodes
- Check for potential security issues

## Usage

```bash
# Validate a script from hex
bun run skills/validate-bsv-script/scripts/validate.ts <script-hex>

# Specify script type
bun run skills/validate-bsv-script/scripts/validate.ts <script-hex> --type locking

# JSON output
bun run skills/validate-bsv-script/scripts/validate.ts <script-hex> --json

# Show help
bun run skills/validate-bsv-script/scripts/validate.ts --help
```

## Status

**Complete** - All tests passing, ready for use

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/b-open-io) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
