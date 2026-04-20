---
name: persona-checkpoint
description: | Use when this capability is needed.
metadata:
  author: hiivmind
---

# Persona Checkpoint — Never-Forget Protocol

This skill implements context protection and checkpointing for AI Persona OS to prevent context loss and enable session recovery with 95% accuracy.

## Phase 1: Assess Context Window Usage

**Step 1.1:** Check current context window usage percentage

Calculate or estimate the current context window utilization based on conversation length, tool calls, and complexity.

**Step 1.2:** Determine severity threshold

| Usage | Status | Action Required |
|-------|--------|-----------------|
| < 50% | 🟢 Normal | Write decisions as they happen (lightweight) |
| 50-69% | 🟡 Vigilant | Increase checkpoint frequency (every ~5 exchanges) |
| 70-84% | 🟠 Active | STOP — write full checkpoint NOW |
| 85-94% | 🔴 Emergency | Emergency flush — essentials only (task + resume point) |
| 95%+ | ⚫ Critical | Survival mode — bare minimum to resume |

**Step 1.3:** Apply user visibility rules

- **< 70%**: Silent operation, no user notification
- **70-84%**: Inform user: "Context at XX% — writing checkpoint"
- **85-94%**: Alert user: "⚠️ Emergency checkpoint required at XX%"
- **95%+**: Critical alert: "🚨 Critical context limit — survival mode checkpoint"

## Phase 2: Determine Checkpoint Action

**Step 2.1:** Select checkpoint type based on threshold

```pseudocode
if context < 50%:
    checkpoint_type = "lightweight"  # Inline decisions only
elif 50% <= context < 70%:
    checkpoint_type = "vigilant"     # Every ~5 exchanges, light format
elif 70% <= context < 85%:
    checkpoint_type = "full"         # Complete checkpoint with reasoning
elif 85% <= context < 95%:
    checkpoint_type = "emergency"    # Task + resume point only
else:  # 95%+
    checkpoint_type = "survival"     # Absolute bare minimum
```

**Step 2.2:** Check for forced triggers

Override threshold-based logic if any of these occur:
- User explicitly says "checkpoint" (forced full checkpoint)
- Before major decisions (architectural changes, destructive operations)
- At natural session breaks (task completion, context switch)
- Before risky operations (data deletion, refactoring, migrations)
- Every ~10 exchanges in normal operation (proactive)

## Phase 3: Write Checkpoint

**Step 3.1:** Create checkpoint file path

Use Bash to create directory and determine file path:

```bash
mkdir -p ~/workspace/memory
echo "~/workspace/memory/$(date +%Y-%m-%d).md"
```

**Step 3.2:** Format checkpoint content based on type

**Lightweight (< 50%):**
```markdown
## Checkpoint [HH:MM] — Context: XX%

**Decision:** [What was decided]
```

**Vigilant (50-69%):**
```markdown
## Checkpoint [HH:MM] — Context: XX%

**Active task:** [Current work]
**Resume from:** [Next step]
```

**Full checkpoint (70-84%):**
```markdown
## Checkpoint [HH:MM] — Context: XX%

**Active task:** [What we're working on]

**Key decisions:**
- [Decision 1 with reasoning]
- [Decision 2 with reasoning]

**Action items:**
- [ ] [Task 1] (owner: [user/assistant])
- [ ] [Task 2] (owner: [user/assistant])

**Current status:** [Progress summary]

**Resume from:** [Exact next step with context]
```

**Emergency (85-94%):**
```markdown
## ⚠️ Emergency Checkpoint [HH:MM] — Context: XX%

**Task:** [One-line description]
**Resume:** [Exact next action]
```

**Survival (95%+):**
```markdown
## 🚨 Survival Checkpoint [HH:MM] — Context: XX%

**Resume:** [Minimum viable next step]
```

**Step 3.3:** Write checkpoint to file

Use Bash to append checkpoint to today's file:

```bash
cat >> ~/workspace/memory/$(date +%Y-%m-%d).md << 'EOF'
[Formatted checkpoint content from Step 3.2]
EOF
```

**Step 3.4:** Confirm write success

Verify the checkpoint was written:

```bash
tail -n 5 ~/workspace/memory/$(date +%Y-%m-%d).md
```

## Phase 4: Recovery Protocol

**Step 4.1:** Read latest checkpoint

Use Bash to locate and read today's checkpoint file:

```bash
cat ~/workspace/memory/$(date +%Y-%m-%d).md
```

**Step 4.2:** Read permanent facts

Use Read tool to load persistent context:

```bash
cat ~/workspace/MEMORY.md
```

**Step 4.3:** Parse resume instructions

Extract the most recent "Resume from" instruction from the checkpoint file.

**Step 4.4:** Notify user and resume

Inform the user:
```
Resuming from checkpoint at [time]. Context recovery: 95%
Last checkpoint: [XX]% context window
Continuing: [Resume instruction]
```

**Step 4.5:** Continue from exact resume point

Execute the next step as specified in the resume instruction with full context from permanent facts + checkpoint.

## Checkpoint Trigger Summary

| Trigger | Frequency | Type |
|---------|-----------|------|
| Proactive | Every ~10 exchanges | Lightweight or Vigilant (depends on context %) |
| Threshold: 70%+ | Immediate | Full checkpoint (mandatory) |
| Threshold: 85%+ | Immediate | Emergency checkpoint (mandatory) |
| Threshold: 95%+ | Immediate | Survival checkpoint (mandatory) |
| Major decision | Before execution | Full checkpoint |
| Natural break | End of task/phase | Vigilant or Full |
| Risky operation | Before execution | Full checkpoint |
| User command | On "checkpoint" keyword | Full checkpoint (forced) |

## State Management

```pseudocode
computed.last_checkpoint_time = [HH:MM]
computed.last_checkpoint_context = [percentage]
computed.checkpoints_today = [count]
computed.recovery_mode = [true/false]
```

## Expected Results

- **Context loss prevention**: Max 5% loss since last checkpoint
- **Recovery accuracy**: 95% context recovery after interruption
- **User transparency**: Notifications at 70%+ thresholds
- **Session continuity**: Seamless resume from exact point
- **Decision preservation**: All key decisions captured with reasoning
- **Action tracking**: All pending tasks preserved with ownership

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiivmind) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
