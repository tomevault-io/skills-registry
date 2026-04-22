---
name: research
description: Verify sources before presenting findings. Use when asked to research links or documentation. Use when this capability is needed.
metadata:
  author: spences10
---

# Verified Research

## Quick Start

1. Fetch actual source content (don't trust snippets)
2. Verify claims before presenting
3. Report failures explicitly

## Tool Priority

1. **GitHub repos** → `gh api` via Bash
2. **npm packages** → `npmx.dev` API via WebFetch (see [npm-package-research.md](references/npm-package-research.md))
3. **Doc pages** → `tavily_extract_process`
4. **Quick answers** → `ai_search` (perplexity/kagi_fastgpt/exa_answer)
5. **Discovery** → `web_search` or `github_search`
6. **Fallback** → Clone repo via subagent

## Core Rules

- **Never present unverified findings** - fetch actual content first
- **Partial data ≠ success** - try next tool, report failures
- **No source substitution** without user consent
- **Flag contradictions** - don't silently pick one source

## References

- [verification-patterns.md](references/verification-patterns.md) - Source conflicts, cutoff handling
- [ai-search-providers.md](references/ai-search-providers.md) - exa_answer, perplexity, kagi usage
- [hallucination-prevention.md](references/hallucination-prevention.md) - CoVe, atomic facts
- [repo-cloning-pattern.md](references/repo-cloning-pattern.md) - Subagent clone workflow
- [partial-data-failures.md](references/partial-data-failures.md) - Rate limits, fallbacks
- [npm-package-research.md](references/npm-package-research.md) - npmx.dev type docs, version resolution, fallbacks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spences10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
