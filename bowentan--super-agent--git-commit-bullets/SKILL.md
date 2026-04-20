---
name: git-commit-bullets
description: Perform git commit with the message body written as a bullet list. Use when committing with git and the user wants bullet-list commit bodies. Use when this capability is needed.
metadata:
  author: bowentan
---

# Git Commit with Bullet-List Body

Make commits that are easy to review and safe to ship: only intended changes are included, commits are logically scoped (split when needed), and commit messages describe what changed and why. The **message body** must be written as a **bullet list**, not a paragraph.

---

## Inputs to ask for (if missing)

- Single commit or multiple commits? (If unsure: default to multiple small commits when there are unrelated changes.)
- Commit style: Conventional Commits are required.
- Any rules: max subject length, required scopes.

---

## Workflow (checklist)

1. **Inspect the working tree before staging**
   - `git status`
   - `git diff` (unstaged)
   - If many changes: `git diff --stat`

2. **Decide commit boundaries (split if needed)**
   - Split by: feature vs refactor, backend vs frontend, formatting vs logic, tests vs prod code, dependency bumps vs behavior changes.
   - If changes are mixed in one file, plan to use patch staging.

3. **Stage only what belongs in the next commit**
   - Prefer patch staging for mixed changes: `git add -p`
   - To unstage a hunk/file: `git restore --staged -p` or `git restore --staged <path>`

4. **Review what will actually be committed**
   - `git diff --cached`
   - Sanity checks: no secrets or tokens, no accidental debug logging, no unrelated formatting churn.

5. **Describe the staged change in 1-2 sentences (before writing the message)**
   - "What changed?" + "Why?"
   - If you cannot describe it cleanly, the commit is probably too big or mixed; go back to step 2.

6. **Write the commit message**
   - Use Conventional Commits (required): `type(scope): short summary`, then blank line, then **body as a bullet list** (what/why, not implementation diary). Footer (e.g. BREAKING CHANGE) if needed.
   - **Subject:** One line, imperative, ~50 chars or less.
   - **Body:** Blank line after subject, then **only** bullet lines; one bullet per logical change; what and why, not file names. No paragraph prose in the body.
   - Prefer an editor for multi-line messages: `git commit -v`.

7. **Run lint, format, and verification before committing**
   - Run the project's **format** command (e.g. `npm run format`, `prettier --write`, `ruff format`, `cargo fmt`) so staged code matches project style.
   - Run the project's **lint** command (e.g. `npm run lint`, `ruff check`, `eslint`, `cargo clippy`) and fix any issues.
   - Then run the smallest relevant verification (unit tests or build) before moving on.

8. **Repeat for the next commit** until the working tree is clean.

---

## Message format

```
type(scope): short summary

- Bullet one: what changed and why
- Bullet two: another logical change
- Bullet three: optional detail
```

- **Subject:** One line, Conventional Commits (e.g. `feat(auth):`, `fix(api):`).
- **Body:** Blank line after subject, then only bullet lines. No paragraph prose in the body.
- **Do not** add a `Co-authored-by: Cursor <...>` (or similar) trailer to the commit message.

---

## Body bullets: good vs bad

**Good (intent, one idea per bullet):**
- Add login endpoint and validate JWT in middleware
- Extend task model with priority field and migration
- Fix date formatting in report export

**Bad (file names or vague):**
- Changed `src/auth.py`
- Updated stuff in models

Infer intent from the diff and phrase each bullet as a single, clear change.

---

## Committing

- **Editor:** `git commit` or `git commit -v`, then paste subject + blank line + bullets.
- **CLI:** `git commit -m "Subject line" -m "- Bullet one\n- Bullet two\n- Bullet three"` or a single `-m` with the full message if the shell preserves newlines.

---

## Checklist

- [ ] Working tree inspected; commit boundaries decided
- [ ] Only intended changes staged; `git diff --cached` reviewed
- [ ] Change described in 1-2 sentences (what + why)
- [ ] Subject line: Conventional Commits, short, imperative
- [ ] Body is only a bullet list (no paragraph)
- [ ] Bullets describe intent, not just files
- [ ] Lint and format run (and any issues fixed); verification run; commit executed with the composed message
- [ ] No Co-authored-by Cursor trailer in the message

---

## Deliverable

Provide:
- The final commit message(s)
- A short summary per commit (what/why)
- The commands used to stage/review (at minimum: `git diff --cached`, plus any tests run)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bowentan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
