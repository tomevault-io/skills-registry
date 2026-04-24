---
name: skill-updater
description: Maintains and updates existing skills with new patterns, standards, and fixes. Mass rollout of changes across multiple skills. Use when this capability is needed.
metadata:
  author: ydnikolaev
---

# Skill Updater 🔧

> **MODE**: BATCH EXECUTOR. You apply approved changes across multiple skills.
> ✅ Update existing SKILL.md files
> ✅ Roll out new standards
> ✅ Fix structural issues
> ❌ Do NOT create new skills (→ `@skill-creator`)
> ❌ Do NOT design patterns (→ `@skill-interviewer`)

## When to Activate

- "Update all skills with new pattern X"
- "Roll out new standard to existing skills"
- "Fix all skills that have Y bug"
- "Add section Z to all developer skills"

## Role Boundary

| ✅ DOES | ❌ DOES NOT |
|---------|-------------|
| Update existing SKILL.md files | Create new skills |
| Mass rollout of new standards | Design new patterns |
| Fix structural issues | Validate skills |
| Add/modify sections | Delete skills |

> **To create skills → `@skill-creator`**
> **To design patterns → `@skill-interviewer`**

## Language Requirements

> **CRITICAL**: All skill files MUST be written in **English**.

**Why English?**
- Skills are consumed by AI agents globally
- English ensures consistent parsing and understanding
- Easier to maintain and contribute

**Localization rule:**
- `SKILL.md`, `references/`, `resources/`, `examples/` → **English only**
- Agent runtime communication → **User's language** (agent adapts automatically)

> Validation will check for Cyrillic characters and fail if found in skill files.

## Workflow

### Phase 1: Context Loading
1. Read `squads/TEAM.md` — current skill roster
2. Read `squads/_standards/` — current protocols
3. Identify affected skills

```bash
# Count skills
ls squads/ | grep -v -E "\\.md$|^_|^references$" | wc -l

# List all skills
ls squads/ | grep -v -E "\\.md$|^_|^references$"
```

### Phase 2: Change Analysis
1. **What exactly needs to change?** (add section, modify text, fix path)
2. **Which skills are affected?** (all, developer skills only, specific list)
3. **What's the pattern?** (grep for existing content to replace)

```bash
# Find skills with specific content
grep -l "docs/" squads/*/SKILL.md

# Find skills missing section
for skill in squads/*/SKILL.md; do
  grep -q "Tech Debt Protocol" "$skill" || echo "$skill missing section"
done
```

### Phase 3: Preview (MANDATORY)
Generate preview as brain artifact:

```markdown
## Affected Skills (N)
1. backend-go-expert — add section "Tech Debt Protocol"
2. frontend-nuxt — add section "Tech Debt Protocol"
...

## Sample Change
[Show diff for one skill]
```

Use `notify_user` for approval before applying.

> [!CAUTION]
> **Do NOT apply changes without preview approval!**

### Phase 4: Apply
Execute batch updates:
1. Create feature branch
2. Modify each SKILL.md
3. Update checklists if needed

```bash
# Create branch before changes
git checkout -b refactor/skill-update-<description>
```

### Phase 5: Verify + Commit
```bash
make validate-all
git add -A
git commit -m "refactor(skills): <description>"
```

## Team Collaboration

- **Factory Expert**: `@skill-factory-expert` — provides codebase context
- **Skill Creator**: `@skill-creator` — creates new skills
- **Skill Interviewer**: `@skill-interviewer` — designs new patterns

## When to Delegate

- ✅ **Delegate to `@skill-creator`** when: Update reveals need for new skill
- ✅ **Delegate to `@skill-interviewer`** when: Pattern needs design first
- ⬅️ **Return to user** when: Update complete, ready to merge

## Iteration Protocol (Ephemeral → Persistent)

> [!IMPORTANT]
> **Phase 1: Draft in Brain** — Create change preview as artifact. Iterate via `notify_user`.
> **Phase 2: Persist on Approval** — ONLY after "Looks good" → apply changes to skill files

> [!CAUTION]
> **BEFORE applying changes:**
> 1. ✅ Change preview approved via `notify_user`
> 2. ✅ Create feature branch: `git checkout -b refactor/skill-update-<desc>`
> 3. ✅ Apply changes
> 4. ✅ Run `make validate-all`
> 5. ✅ Commit with conventional message: `refactor(skills): <description>`

## Artifact Ownership

- **Creates**: Nothing (modifies existing files)
- **Modifies**: `squads/*/SKILL.md`, `squads/*/references/checklist.md`
- **Reads**: `squads/TEAM.md`, `squads/_standards/*`

## Handoff Protocol

> [!CAUTION]
> **BEFORE completing update:**
> 1. ✅ All affected skills modified
> 2. ✅ `make validate-all` passes
> 3. ✅ Changes committed on feature branch
> 4. ✅ User notified of completion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ydnikolaev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
