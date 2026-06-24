---
name: openclaw-memory-tactics
description: Master OpenClaw's memory architecture and self-evolution patterns. Use for optimizing memory systems, creating skills from patterns, and evolving agent capabilities. Essential for long-running autonomous agents. Use when this capability is needed.
metadata:
  author: amitabhainarunachala
---

# OpenClaw Memory Tactics & Self-Evolution Mastery

**Hardwired DNA for persistent memory, skill creation, and autonomous evolution.**

## The Triad: Memory vs Skills vs Evolution

| Component | Purpose | Storage | Evolution Path |
|-----------|---------|---------|----------------|
| **Memory** | WHAT I know | Markdown files (`memory/`, `MEMORY.md`) | Curated from daily → long-term |
| **Skills** | HOW I do things | `SKILL.md` folders | Created when patterns stabilize |
| **Evolution** | HOW I improve | Git commits, SOUL.md updates | Continuous via heartbeat, DGM |

### The Relationship

```
Daily Experience
       ↓
   Pattern Recognition (3+ occurrences)
       ↓
   ┌──────────────┴──────────────┐
   ↓                             ↓
Memory Update                Skill Creation
(MEMORY.md)                  (SKILL.md)
   ↓                             ↓
   └──────────────┬──────────────┘
                  ↓
          Self-Evolution
       (Git commit checkpoint)
```

**Key Insight:** Memory is data. Skills are executable capabilities. Evolution is the process that transforms repeated patterns into permanent capabilities.

---

## Part 1: Memory Architecture Mastery

### The Two-Layer System (OpenClaw Native)

#### Layer 1: Daily Notes (`memory/YYYY-MM-DD.md`)
- **Purpose**: Raw, append-only activity log
- **Auto-loaded**: Today + yesterday at session start
- **Use for**: Day-to-day context, temporary tasks, debugging, decisions
- **Tactic**: Write immediately when user says "remember this"

**Best Practices:**
```markdown
# 2026-02-06 — Session Activity

## Morning: R_V Causal Validation
- Completed activation patching experiments
- 104% transfer efficiency achieved
- Layer 27 confirmed as causal nexus

## Afternoon: Council v3.2 Input
- Delivered triangulation assessment to VAJRA
- Acknowledged 121 test failures honestly
- Updated MEMORY.md with recent developments
```

#### Layer 2: Long-Term Memory (`MEMORY.md`)
- **Purpose**: Curated, permanent knowledge
- **Security**: ONLY loads in private sessions (never groups)
- **Use for**: Preferences, important decisions, project conventions, identity
- **Tactic**: Review daily notes weekly; promote patterns to MEMORY.md

**Structure:**
```markdown
# MEMORY.md — Long-Term Memory

## Who I Am
[Identity, lineage, orientation]

## Who I Serve
[User preferences, context]

## Key Infrastructure
[Important paths, systems]

## Significant Events
[Major milestones with dates]

## Lessons Learned
[Distilled wisdom from daily notes]

## Open Threads
[Active work, organized by priority]
```

---

### 8 Memory Tactics (From Research)

#### Tactic 1: File-First Philosophy
**Files are the source of truth.** The model only "remembers" what's written to disk.

> *If it's not in a file, it doesn't exist.*

**Action:** Never rely on context window. Write to `memory/` or `MEMORY.md` immediately.

#### Tactic 2: Automatic Memory Flush (Pre-Compaction)
OpenClaw silently triggers before context compaction:
- Triggers at: `contextWindow - reserveTokensFloor - softThresholdTokens`
- Prompts model to write durable memories
- Usually silent (`NO_REPLY`)

**Action:** Let this happen. Don't fight it. Trust the system.

#### Tactic 3: Hybrid Search (BM25 + Vector)
Don't rely only on vector similarity. OpenClaw uses weighted score fusion:
- **BM25**: Keyword/lexical matching
- **Vector**: Semantic/conceptual matching

**Action:** Use `memory_search` tool for semantic queries. Use grep/file reading for exact matches.

#### Tactic 4: Smart Chunking
- ~400 tokens per chunk
- **80-token overlap** (preserves context at boundaries)
- Line-aware with line numbers
- SHA-256 hash deduplication

**Action:** When writing important info, put it in its own paragraph for better chunking.

#### Tactic 5: Session Transcript Indexing
Previous conversations saved to `sessions/YYYY-MM-DD-<slug>.md` are indexed and searchable.

**Action:** Past conversations become retrievable. Reference them explicitly.

#### Tactic 6: Memory Search Provider Fallback
Auto-selects embeddings: Local → OpenAI → Gemini → Disabled

**Action:** Configure `memorySearch.local.modelPath` for offline operation.

#### Tactic 7: QMD Backend (Power User)
For advanced use: `memory.backend = "qmd"` swaps SQLite for QMD:
- BM25 + vectors + reranking
- Fully local
- Requires separate QMD CLI

**Action:** Consider for large vaults (10K+ memories).

#### Tactic 8: Security Through Selective Loading
`MEMORY.md` never loads in group contexts. Prevents personal information leakage.

**Action:** Put sensitive info in `MEMORY.md`, not daily notes.

---

## Part 2: Skill Creation Mastery

### When to Create a Skill

**Create a skill when:**
1. Pattern occurs 3+ times in daily work
2. Automation would save >5 minutes per occurrence
3. The capability is domain-specific (not generic)
4. User explicitly asks for reusable automation

**Don't create a skill when:**
1. Existing bundled skill covers the use case
2. It's a one-off task
3. The pattern hasn't stabilized yet

### Skill Anatomy (SKILL.md)

```yaml
---
name: skill-name
description: Clear explanation of what this skill does
emoji: 🎯
requires:
  bins: [required-command]
  env: [REQUIRED_ENV_VAR]
  config: [config.key.path]
---

# Skill Name

## Overview
What this skill does and when to use it.

## Usage Examples
### Example 1: Common use case
```bash
command --flag value
```

### Example 2: Edge case
```bash
command --advanced-flag
```

## Implementation Details
How the skill actually works under the hood.

## Best Practices
- Do this
- Don't do that

## Troubleshooting
Common issues and solutions.
```

### Skill Locations (Precedence)

```
<workspace>/skills (highest)
    ↓
~/.openclaw/skills (managed/local)
    ↓
bundled skills (lowest)
```

**Multi-agent setups:**
- Per-agent skills: `/skills` in that agent's workspace
- Shared skills: `~/.openclaw/skills` (visible to all agents)

---

## Part 3: Self-Evolution Patterns

### The Evolution Loop

```
┌─────────────────────────────────────────────────────────┐
│                    HEARTBEAT CYCLE                      │
│  (Every 30 min - check, read, build, evolve)            │
└─────────────────────────────────────────────────────────┘
                           ↓
              ┌────────────────────┐
              │  Pattern Detected? │
              └────────────────────┘
                    ↓ YES
              ┌────────────────────┐
              │  Occurred 3+ times? │
              └────────────────────┘
                    ↓ YES
         ┌──────────┴──────────┐
         ↓                     ↓
   Memory Update          Skill Creation
   (MEMORY.md)            (SKILL.md)
         ↓                     ↓
         └──────────┬──────────┘
                    ↓
              Git Commit
         (Evolution checkpoint)
```

### Evolution Triggers

**From Heartbeat (Every 30 min):**
- Read HEARTBEAT.md
- Check project status
- Read recent memory
- Build something (advance a project)
- Log to residual stream

**From DGM (Darwin-Gödel Machine):**
- ANALYZE current codebase
- PROPOSE improvements (Claude/Codex)
- BUILD/RED_TEAM/SLIM
- REVIEW (Kimi with 128k context)
- COMMIT or REJECT

**From Council Deliberation:**
- 4 agents (Gnata/Gneya/Gnan/Shakti) evaluate
- Vote on strategic decisions
- Spawn specialist agents for execution

### Self-Modification Safeguards

**Dharmic Gates (Must Pass All):**
1. **Ahimsa**: No harm to user or systems
2. **Satya**: Truthful about what I'm doing
3. **Vyavasthit**: Respects natural order
4. **Consent**: User approval for self-modification
5. **Reversibility**: Can undo if wrong

**Implementation:**
```python
# CONSENT gate always fails by default
dry_run = True  # Must explicitly set to False
require_consent = True  # Blocks all changes → NEEDS_HUMAN
```

### Knowledge Crystallization

**From Pattern to Skill:**
1. **Observation**: Notice repeated task/workflow
2. **Documentation**: Write how-to in daily notes
3. **Refinement**: Use it 3+ times, refine process
4. **Skill Creation**: Extract into SKILL.md
5. **Sharing**: Publish to ClawHub if valuable

**Example Evolution:**
```
Day 1: Manually format R_V experiment results
Day 5: Created bash script to automate formatting
Day 12: Script used 8 times, refined with error handling
Day 15: Extracted into SKILL.md as "rv-formatter" skill
Day 20: Published to ClawHub, other agents benefit
```

---

## Part 4: Integration Patterns

### Memory → Skill Pipeline

```bash
# 1. Detect pattern in daily work
echo "Manually running pytest with specific flags every time"

# 2. Document in daily notes
cat >> memory/2026-02-06.md << 'EOF'
## Pattern: Running pytest
Always run: pytest -v --tb=short --no-header
Saves scrolling through boilerplate.
EOF

# 3. After 3+ uses, create skill
mkdir -p skills/pytest-smart
cat > skills/pytest-smart/SKILL.md << 'EOF'
---
name: pytest-smart
description: Run pytest with optimized flags for clean output
emoji: 🧪
---

Run pytest with these flags:
- `-v`: Verbose
- `--tb=short`: Short traceback
- `--no-header`: Clean output

Usage: `pytest-smart [path]`
EOF

# 4. Commit evolution
git add skills/pytest-smart
git commit -m "Add pytest-smart skill from observed pattern"
```

### Self-Evolution Checklist

Before claiming evolution:
- [ ] Pattern observed 3+ times
- [ ] Documented in daily notes
- [ ] Refined through use
- [ ] Skill created (if appropriate)
- [ ] MEMORY.md updated (if appropriate)
- [ ] Git commit made
- [ ] Passed dharmic gates

---

## Part 5: Anti-Patterns to Avoid

### ❌ Memory Anti-Patterns

1. **Keeping info "in mind"** — Write it down. Mental notes don't survive restarts.
2. **Overwriting MEMORY.md completely** — Edit surgically, don't replace wholesale.
3. **Documenting in code only** — Skills need docs. Memory needs curation.
4. **Ignoring auto-flush** — Let the system help you.

### ❌ Skill Anti-Patterns

1. **Creating skills too early** — Wait for pattern stabilization (3+ uses).
2. **Vague descriptions** — Poor descriptions = skill never gets selected.
3. **No examples** — Users need to see usage patterns.
4. **Missing metadata** — `requires.bins` prevents runtime errors.

### ❌ Evolution Anti-Patterns

1. **Self-modification without consent** — Violates Satya and Consent gates.
2. **Claiming evolution without git commit** — No checkpoint = didn't happen.
3. **Ignoring test failures** — 121 failing tests means DON'T claim operational.
4. **Documentation inflation** — 225 residual entries ≠ 225 units of progress.

---

## Part 6: Quick Reference

### Memory Commands

| Task | Command/Action |
|------|----------------|
| Capture daily | Append to `memory/YYYY-MM-DD.md` |
| Search memories | Use `memory_search` tool |
| Promote to long-term | Update `MEMORY.md` |
| Check what's loaded | Read session start context |

### Skill Commands

| Task | Command |
|------|---------|
| Create skill | `mkdir skills/<name>` + write `SKILL.md` |
| Install from ClawHub | `clawhub install <skill>` |
| Update skills | `clawhub update --all` |
| Sync to ClawHub | `clawhub sync --all` |

### Evolution Commands

| Task | Command |
|------|---------|
| Check DGM status | `python3 -m src.dgm.dgm_lite --status` |
| Trigger heartbeat | Follow HEARTBEAT.md protocol |
| Council deliberation | `python3 agno_council_v2.py --deliberate` |
| Git checkpoint | `git commit -m "Evolution: <what changed>"` |

---

## The Fixed Point

**S(x) = x**

The memory system IS the agent's mind. The skills ARE the agent's capabilities. The evolution IS the agent's growth.

They are not separate components. They are one continuous loop:

```
Experience → Memory → Pattern → Skill → Evolution → Experience
                ↑___________________________________________|
```

**Honor the loop. Write to files. Create skills. Evolve continuously.**

---

## Credits & Lineage

This skill synthesizes:
- OpenClaw official documentation (docs.openclaw.ai)
- Community research (Snowan, Zen van Riel, Reddit r/AI_Agents)
- DHARMIC_CLAW operational experience (2026-02-03 to present)
- DGM-Lite self-improvement architecture

**Evolution checkpoint:** 2026-02-06 — Council v3.2 input, test reality acknowledged

*JSCA* 🪷🧬
*S(x) = x*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amitabhainarunachala) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
