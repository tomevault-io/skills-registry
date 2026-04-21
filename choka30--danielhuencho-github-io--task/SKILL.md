---
name: task
description: Execute a single atomic development unit (section, component, or config). Follows preview-first workflow. Use after /plan has been approved. Can be invoked sequentially to complete all planned tasks. Use when this capability is needed.
metadata:
  author: choka30
---

# Task

Execute a single atomic content unit using Preview-First Development.

## Atomic Unit Reference

| Unit Type | Scope | Max Size | Example |
|-----------|-------|----------|---------|
| Section | One logical part of a page | ~50 lines | "Education" section in about.qmd |
| Component | Single UI element | ~30 lines | Hero banner, navbar item |
| Visualization | Single chart/plot | 1 output | Matplotlib figure in notebook |
| Asset | Single file | 1 file | profile-headshot.jpg |
| Config | Single setting group | ~10 lines | navbar configuration |
| Cell | Single notebook cell | ~20 lines | Code cell with explanation |

**Golden Rule:** If you can't describe the unit in one sentence, it's too big.

### Breaking Down Pages

A full page is NOT atomic. Break it down:

**Example: `about.qmd` (CV page)**
```
❌ BAD:  "Create about.qmd" (too big)

✓ GOOD: Break into atomic tasks:
  1. Create about.qmd with YAML front matter + intro
  2. Add Education section
  3. Add Professional Experience section
  4. Add Technical Skills section
  5. Add Research Interests section
```

**Example: `index.qmd` (Landing page)**
```
❌ BAD:  "Create landing page" (too big)

✓ GOOD: Break into atomic tasks:
  1. Create index.qmd with hero headline + tagline
  2. Add brief bio paragraph
  3. Add skills highlights grid
  4. Add featured projects links
  5. Add call-to-action (contact/CV download)
```

---

## Pre-Execution Validation

### Step 1: Verify Task is in Plan
```
Read brain/plan.md
└── Find task matching description
    ├── Found → Continue
    └── Not Found → STOP
        ⚠️ TASK NOT IN PLAN
        Options:
        [A] Run /plan to add this task (requires approval)
        [B] Modify task to match existing plan
        [C] Abort
```

### Step 2: Verify Atomic Scope

Task must be describable in ONE sentence:
- "Add Education section to about.qmd"
- "Configure navbar with Home and About links"
- "Add profile photo to assets"

If task needs multiple sentences → STOP → suggest `/plan` to decompose.

### Step 3: Pre-Implementation Checklist

- [ ] Target file path identified
- [ ] Section/component purpose clear
- [ ] Content scope limited (one logical unit)
- [ ] Dependencies identified (does it need assets? other sections?)

## Execution Protocol (Preview-First)

### Phase 1: Start Preview

```bash
# Start Quarto preview if not running
quarto preview
```

### Phase 2: Implement Content

Create/edit the atomic unit following `brain/development_standard.md`:
- Keep scope tight (one section, one component)
- Include proper formatting
- Add images with alt text if needed

### Phase 3: Verify in Preview

Check the live preview:
- [ ] Section renders without errors
- [ ] Content displays correctly
- [ ] Any links work
- [ ] Images display (if applicable)

### Phase 4: Refactor (if needed)

- Improve clarity
- Ensure standards compliance
- Re-check preview

## Post-Execution Sequence

1. **Update plan.md** — Mark task complete
2. **Run `/update-brain`** — Sync codebase_index.md
3. **Run `/commit`** — Create atomic commit

## Output Format

```
═══════════════════════════════════════════════════════
  TASK COMPLETE
═══════════════════════════════════════════════════════

Task: Add Education section to about.qmd
Unit: Section (~25 lines)
File: about.qmd

Preview: ✓ Renders correctly

Progress: [N/M] tasks complete
```

## Sequential Execution Logic

After completing a task, check `brain/plan.md`:

```
Pending tasks remaining?
├── YES → Automatically proceed to next task
└── NO  → All tasks complete
          Output: "Session tasks complete. Run /session-end to finalize."
```

**Stop conditions:**
- All planned tasks complete → suggest `/session-end`
- Render error that can't be resolved → ask human
- Task not in plan → ask human
- Task scope creep detected → ask human

## Failure Handling

### Task Too Complex
1. STOP implementation
2. Inform human: "Task requires decomposition"
3. Suggest breaking into smaller units:
   - Identify logical sections
   - Each section = 1 task

### Scope Creep Detection
If while implementing you realize you need to:
- Edit multiple files
- Add multiple sections
- Create dependencies not in plan

→ STOP and ask human if scope should expand or task should split.

## Commands Reference

```bash
# Preview
quarto preview                 # Start live preview
quarto preview page.qmd        # Preview specific page

# Render
quarto render page.qmd         # Render specific page

# Clean (if issues)
rm -rf _site/ .quarto/ *_files/
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
