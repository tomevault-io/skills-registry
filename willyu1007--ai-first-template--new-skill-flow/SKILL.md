---
name: new-skill-flow
description: Step-by-step flow for skill scaffolding. Keywords: skill, flow. Use when this capability is needed.
metadata:
  author: willyu1007
---
# Flow: New Skill Package

This doc contains the step-by-step flow. For inputs, tools, outputs, and safety, see:
`/.system/skills/ssot/repo/scaffolding/new-skill/SKILL.md`.

---

## Step-by-Step Flow (AI + Human)

### Step 1: Collect Parameters (AI 鈫?Human)

AI asks human for:
1. Skill name (kebab-case; what users will type to trigger it)
2. `description` (single-line; include trigger keywords)
3. Optional: group (default `repo`)
4. Optional: scope (`repo` or `module`)
5. If scope=`module`: module_id

**Human checkpoint**: Confirm parameters before proceeding.

### Step 2: Validate Inputs (AI)

AI validates:
- `skill_name` is kebab-case and <= 64 chars
- `description` is <= 500 chars
- Target SSOT path does not already exist:

### Step 3: Preview Changes (AI)

AI runs orchestrator with `--dry-run`:

```bash
python scripts/devops/scaffold/new_skill.py \
  --skill-name <skill-name> \
  --description "<description>" \
  --group <group> \
  --scope <repo|module> \
  --module-id <module-id> \
  --dry-run
```

**Human checkpoint**: Approve planned file paths and skill naming.

### Step 4: Apply Scaffold (AI)

AI runs orchestrator (apply mode):

```bash
python scripts/devops/scaffold/new_skill.py \
  --skill-name <skill-name> \
  --description "<description>" \
  --group <group>
```

### Step 5: Validate (AI)

1. Ensure wrappers are generated (unless `--skip-regenerate` was used).
2. Run wrapper consistency check:
   ```bash
    python scripts/devops/skills/sync_skills.py --check --target repo --publish-set repo_minimal
   ```

### Step 6: Record Outcome (AI)

Record in the nearest relevant workdocs:
- created SSOT path
- regenerated wrappers
- any follow-up edits needed for the skill content

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
