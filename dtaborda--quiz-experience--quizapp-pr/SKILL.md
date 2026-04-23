---
name: quizapp-pr
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

## PR Creation Process

1. **Analyze changes**: `git diff main...HEAD` so you understand *all* work since the branch diverged.
2. **Determine affected packages**: frontend (Next.js), backend (Express API), shared (schemas/types), docs, infra.
3. **Load relevant Skills + AGENTS**: follow the instructions in each package and `docs/developer-guide/introduction.mdx`.
4. **Fill template sections** with Context/Description/Steps to review/Checklist.
5. **Create PR** with `gh pr create` (use a heredoc for the body to keep formatting intact).

## PR Template Structure

```markdown
### Context

{Why this change? Reference docs/use-cases.md and link the GitHub issue with `Closes #XX`}

### Description

{Summary of changes and dependencies}

### Steps to review

{How to test/verify the changes}

### Checklist

<details>

<summary><b>Community Checklist</b></summary>

- [ ] Issue is tracked in https://github.com/quizapphq/quiz-experience/issues and assigned to me (or I volunteered in the thread/Slack).
- [ ] I followed `docs/developer-guide/introduction.mdx` and loaded the Skills for each touched package.

</details>

- [ ] Conventional Commit-style PR title (`feat(frontend): ...`).
- [ ] `pnpm lint`, `pnpm test`, and `pnpm format` ran locally.
- [ ] Screenshots/videos added for UI changes (mobile/tablet/desktop) when visual output changes.
- [ ] Docs updated (e.g., `docs/use-cases.md`, `docs/wireframes.yaml`) if behavior changed.
- [ ] Shared schemas/types updated + consumers refactored when contracts changed.
- [ ] LocalStorage/attempt lifecycle rules validated for quiz domain tweaks.
- [ ] CHANGELOG entries added if the affected package maintains one.

#### Frontend (if applicable)
- [ ] App Router + shadcn/ui patterns follow `quizapp-ui` Skill.
- [ ] Responsive screenshots/videos included when UI differs.
- [ ] Zustand store updates covered by tests under `frontend/`.

#### Backend (if applicable)
- [ ] Inputs/outputs validated with shared Zod schemas.
- [ ] Fixtures/docs updated when quiz JSON/data changes.
- [ ] Supertest coverage added/updated for new routes.

#### Shared (if applicable)
- [ ] `shared/` schemas + types versioned intentionally (avoid breaking existing consumers without coordination).
- [ ] Tests updated to reflect schema changes.

### License

By submitting this pull request, I confirm that my contribution is made under the terms of the Apache 2.0 license.
```

## Component-Specific Rules

| Component | CHANGELOG | Extra Checks |
|-----------|-----------|--------------|
| Frontend | `frontend/CHANGELOG.md` (if present) | Screenshots for all breakpoints, state persistence verified |
| Backend | `backend/CHANGELOG.md` | Update docs for new endpoints, run Supertest suites |
| Shared | `shared/CHANGELOG.md` | Ensure types/schemas synced with consumers |
| Docs | n/a | Link back to updated references, regenerate static assets if needed |
| Infra | `infra/CHANGELOG.md` | Confirm Docker/devbox instructions still work |

## Commands

```bash
# Check current branch status
git status
git log main..HEAD --oneline

# View full diff
git diff main...HEAD

# Create PR with heredoc for body
gh pr create --title "feat(frontend): description" --body "$(cat <<'EOF'
### Context
...
EOF
)"

# Create draft PR
gh pr create --draft --title "feat: description"
```

## Title Conventions

Follow Conventional Commits + scope prefixes for the touched package:
- `feat(frontend):` new UI feature
- `fix(backend):` bug fix in Express API
- `docs(intro):` documentation change
- `chore(shared):` maintenance/refactor
- `test(frontend):` new or updated tests
- `infra(devbox):` infra changes

## Before Creating PR

1. ✅ `pnpm test`, `pnpm lint`, and `pnpm format` succeed (or package-scoped equivalents via `pnpm --filter`).
2. ✅ Turborepo caches updated (run `pnpm dev`/build commands touched by the change).
3. ✅ CHANGELOG/docs touched where needed.
4. ✅ Branch rebased on latest `main` (no merge commits unless required).
5. ✅ Commits are clean, descriptive, and follow repo conventions.

## Resources

- **docs/developer-guide/introduction.mdx** – onboarding + PR expectations.
- **AGENTS.md** – package-specific guardrails.
- **skills/** – load `quizapp-ui`, `quizapp-api`, `quizapp-domain`, etc., depending on the files you touch.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
