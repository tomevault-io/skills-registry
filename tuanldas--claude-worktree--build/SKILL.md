---
name: build
description: Build the project, run vet, and verify the binary works. Use when checking if code compiles correctly. Use when this capability is needed.
metadata:
  author: tuanldas
---

# Build Project

## Steps

1. Run `go vet ./...` to check for suspicious code
2. Run `go build -o claude-worktree .` to compile
3. If build fails:
   - Read the failing source files
   - Identify and explain the compilation errors
   - Suggest fixes
4. If build succeeds:
   - Run `./claude-worktree --help` to verify the binary runs
   - Run `go mod tidy` to clean up dependencies
   - Clean up binary with `rm claude-worktree`
   - Report success

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tuanldas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
