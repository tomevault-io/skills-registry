---
name: skill-vettr
description: Static analysis security scanner for third-party OpenClaw skills. Detects eval/spawn risks, malicious dependencies, typosquatting, and prompt injection patterns before installation. Use when vetting skills from ClawHub or untrusted sources. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# skill-vettr v2.0.1

Security scanner for third-party OpenClaw skills. Analyses source code, dependencies, and metadata before installation using tree-sitter AST parsing and regex pattern matching.

## Commands

- `/skill:vet --path <directory>` — Vet a local skill directory
- `/skill:vet-url --url <https://...>` — Download and vet from URL
- `/skill:vet-clawhub --skill <slug>` — Fetch and vet from ClawHub

## Detection Categories

| Category | Method | Examples |
|----------|--------|----------|
| Code execution | AST | eval(), new Function(), vm.runInThisContext() |
| Shell injection | AST | exec(), execSync(), spawn("bash"), child_process imports |
| Dynamic require | AST | require(variable), require(templateString) |
| Prototype pollution | AST | __proto__ assignment |
| Prompt injection | Regex | Instruction overrides, control tokens (in string literals) |
| Homoglyph attacks | Regex | Cyrillic/Greek lookalike characters in identifiers |
| Encoded names | Regex | Unicode/hex-escaped "eval", "exec" |
| Credential paths | Regex | .ssh/, .aws/, keychain path references |
| Network calls | AST | fetch() with literal URLs (checked against allowlist) |
| Malicious deps | Config | Known bad packages, lifecycle scripts, git/http deps |
| Typosquatting | Levenshtein | Skill names within edit distance 2 of targets |
| Dangerous permissions | Config | shell:exec, credentials:read in SKILL.md |

## Limitations

> ⚠️ **This is a heuristic scanner with inherent limitations. It cannot guarantee safety.**

- **Static analysis only** — Cannot detect runtime behaviour (e.g., code that fetches malware after install)
- **Evasion possible** — Sophisticated obfuscation or multi-stage string construction can evade detection
- **JS/TS only** — Binary payloads, images, and non-text files are skipped
- **Limited network detection** — Only detects `fetch()` with literal URL strings; misses axios, http module, dynamic URLs
- **No sandboxing** — Does not execute or isolate target code
- **Comment scanning** — Prompt injection detection scans string literals, not comments

For high-security environments, combine with sandboxing and manual source review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
