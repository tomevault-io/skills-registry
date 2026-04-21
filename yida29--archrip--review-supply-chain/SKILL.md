---
name: review-supply-chain
description: Review supply-chain and code-execution risks Use when this capability is needed.
metadata:
  author: yida29
---

# Supply-chain / execution safety review

Review the codebase for supply-chain risk and untrusted-code execution paths.

## Focus areas
- Where the tool **executes** external commands (npm, npx, git, shell)
- Where it **downloads/installs** dependencies at runtime
- Template copying / vendored code: how integrity/origin is verified
- Lockfiles and reproducibility (`npm ci` vs `npm install`)
- Symlink/path traversal risks when executing within project directories

## Output format
1. **Attack surface inventory** (commands + cwd + trust boundary)
2. **High-risk findings** (with file paths)
3. **Minimal mitigations** (ordered)
   - pin versions/lockfile, avoid `npx`, run `npm run`, symlink rejection, integrity checks

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yida29) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
