---
name: setup-react-doctor
description: React health scoring via react-doctor with Stop hook to fail on score regression. Use when setting up react-doctor or preventing UI quality regressions. Use when this capability is needed.
metadata:
  author: malinskibeniamin
---

# Setup React Doctor

Repo/code changes: run `/deslop` before commit, push, PR, or merge.
- **react-doctor** codebase health score (0-100)
- Stop hook run doctor on changed files; failures, low scores, and warnings block
- No downgrade-to-allow loop; doctor errors are stop-gaps
- Config disable biome-overlapping rules

## Steps

### 1. Install
```bash
bun add -D react-doctor --yarn
```

### 2. Package.json
```json
{ "scripts": { "doctor": "react-doctor ." } }
```

### 3. Config (`react-doctor.config.json`)
```json
{ "ignore": { "rules": ["react-hooks/exhaustive-deps", "react/no-nested-component"] } }
```

### 4. Hook
Copy `scripts/react-doctor-stop.sh` -> `.claude/hooks/`. `chmod +x`. Add to Stop.

### 5. Verify
- [ ] `bun run doctor` work
- [ ] Stop hook executable

---
> Source: [malinskibeniamin/skills](https://github.com/malinskibeniamin/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
