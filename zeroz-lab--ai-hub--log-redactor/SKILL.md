---
name: log-redactor
description: Redact secrets and PII from logs before sharing or summarizing. Use when the user asks to sanitize/redact logs or remove emails, tokens, API keys, or passwords. Use when this capability is needed.
metadata:
  author: zeroz-lab
---

# Log Redactor Skill

## Workflow
1. Run the redaction script on the raw log.
2. Verify that obvious secrets and PII are masked.
3. Summarize the redacted content if requested.

## Script usage
- Run: `python scripts/redact_logs.py --input <path> --output <path>`
- If no output path is provided, print to stdout.

## Output format
Redacted log:
```
<redacted content>
```

Optional summary:
- ...

## Notes
- Do not invent values when masking.
- Keep original structure intact while replacing sensitive tokens.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zeroz-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
