---
name: ei-release-gate
description: Release candidate checklist: build, test, docker smoke, and release notes draft for ei-agentic-claude. Use when this capability is needed.
metadata:
  author: edgeimpulse
---

# Release Gate (RC)

## Preconditions
- Working tree clean (`git status --porcelain` empty)
- On the correct release branch/tag
- Secrets remain local (`.env` files git-ignored)

## Gate steps
1. Install/build
   - `npm ci`
   - `npm run build`

2. Unit tests
   - `npm test`

3. Docker smoke
   - `npm run docker:test`

4. Manual smoke (read-only)
   - `node launch-cli.mjs --help`
   - Optional (if API key available): `node launch-cli.mjs get-all-projects --api-key $EI_API_KEY`

5. Draft release notes (output only)
Create `outputs/release/notes.md` containing:
- Summary of changes
- Breaking changes
- Upgrade notes
- Security notes

## Stop conditions
Stop if any gate fails or if secrets appear in outputs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edgeimpulse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
