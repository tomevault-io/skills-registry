---
name: codeeagle-review
description: Review code changes and diffs against codebase conventions using CodeEagle's knowledge graph. Use for code review, convention checking, and identifying missing tests. Use when this capability is needed.
metadata:
  author: imyousuf
---

# CodeEagle Review

Run the CodeEagle code review agent to review changes against codebase conventions.

## Usage

Use the Bash tool to run:
```
codeeagle agent review "$ARGUMENTS"
```

To review a specific git diff or PR reference:
```
codeeagle agent review --diff <ref>
```

## Prerequisites
- CodeEagle must be installed and on PATH
- Project must be initialized (`codeeagle init`) with an indexed graph (`codeeagle sync`)

## Notes
- Reviews diffs against codebase conventions and patterns
- Flags deviations from established patterns
- Identifies missing tests for changed code paths
- Highlights complexity hotspots in modified code
- Performs security pattern checks (auth, input validation, secrets)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imyousuf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
