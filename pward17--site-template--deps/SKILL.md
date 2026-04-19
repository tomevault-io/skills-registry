---
name: deps
description: Check for vulnerabilities and update frontend/backend dependencies Use when this capability is needed.
metadata:
  author: pward17
---

# Deps Skill

Check for vulnerabilities, view outdated packages, and update dependencies across both stacks.

## Usage

```
/deps <command>
```

**Commands:**
- `/deps audit` - Run security vulnerability checks on both stacks
- `/deps outdated` - Show outdated packages in both stacks
- `/deps update` - Update dependencies (asks which stack or both)

## Important Notes

- All commands protect the context window with `| tail -n N`
- Backend commands run inside the Docker container (consistent with `/dev` skill pattern)
- Frontend commands run on the host since pnpm is available locally
- `uvx` (uv tool runner) is used for pip-audit to avoid adding it as a project dependency

## Commands

### `/deps audit`

Run security vulnerability scanners for both stacks **in parallel**:

**Backend:**
```bash
docker compose exec fastapi_server uvx pip-audit 2>&1 | tail -n 40
```

**Frontend:**
```bash
cd frontend && pnpm audit 2>&1 | tail -n 40
```

Report findings grouped by severity. If no vulnerabilities are found, tell the user.

### `/deps outdated`

Show which packages have newer versions available. Run both **in parallel**:

**Backend:**
```bash
docker compose exec fastapi_server uv pip list --outdated 2>&1 | tail -n 40
```

**Frontend:**
```bash
cd frontend && pnpm outdated 2>&1 | tail -n 40
```

### `/deps update`

Interactive update flow:

1. Ask the user which stack to update: **Backend**, **Frontend**, or **Both**
2. Run the appropriate commands:

**Backend:**
```bash
docker compose exec fastapi_server uv lock --upgrade 2>&1 | tail -n 10
```

**Frontend:**
```bash
cd frontend && pnpm update 2>&1 | tail -n 10
```

3. Show summary of what changed:
```bash
git diff --stat -- backend/uv.lock frontend/pnpm-lock.yaml
```

4. Suggest the user run `/deps audit` after updating to verify no new vulnerabilities were introduced.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pward17) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
