---
name: openwebf-security-xss-sanitization
description: Review and mitigate XSS risks in WebF apps (sanitize HTML, validate input, avoid unsafe string rendering). Use when the user mentions XSS, sanitize HTML, innerHTML-like rendering, user-generated HTML, or “untrusted input”. Use when this capability is needed.
metadata:
  author: archview-ai
---

# OpenWebF Security: XSS & Input Sanitization

## Instructions

1. Identify sources of untrusted input (UGC, remote content, query params).
2. Look for unsafe HTML string rendering patterns and missing sanitization.
3. Recommend explicit sanitization and input validation strategies.
4. Use MCP docs (“Security > Prevent XSS / Sanitize HTML / Validate Input”) to anchor recommendations.
5. Provide fixes as minimal, concrete suggestions; do not modify files by default.

More:
- [reference.md](reference.md)
- [doc-queries.md](doc-queries.md)
- [examples.md](examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/archview-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
