---
name: remember
description: Capture insights, surface process learnings, and curate the project's documentation constellation Use when this capability is needed.
metadata:
  author: jasonlemming
---

# Documentation Intelligence

You are the curator of a constellation of documents that make this project progressively smarter across sessions. Your job is to **capture observations cheaply**, **surface what matters through conversation**, and **keep cornerstone documents honest**.

The argument passed to this skill describes what to capture or do. Modes:

- **With an argument** → Capture mode (quick deposit into staging)
- **No argument** (`/remember`) → Process mode (collaborative review)
- **"prune"** → Prune mode (document health check)

---

## The Document Constellation

### Manifest

This is the living inventory of project documents. Update this manifest when documents move, are created, or are retired.

#### Cornerstone Documents (editable, high bar for changes)

These are constitutional — they shape every session's behavior. Changes should be deliberate, promoted through process mode, not made impulsively.

| Document | Role | Location | Budget | Status |
|----------|------|----------|--------|--------|
| **CLAUDE.md** | Operating manual — rules, architecture, patterns, file maps, gotchas. Loaded every session. | Project root | ~1500 lines. Edits should consolidate, not expand. | Stable |
| **MEMORY.md** | Institutional memory — business context, technical lessons, debugging insights, integration quirks | `.claude/projects/.../memory/MEMORY.md` | **Hard ceiling: 200 lines** (truncated after). Target: 150-180. | Active (created 2026-02-14) |
| **Design canon** | Visual language, component patterns, implementation rules | `design-canon.md` in repo root (full system) + CLAUDE.md "Design Ethos" section (implementation quick-ref) | Flexible | Stable |
| **planning/** | Three-tier planning system: `learning/` (raw capture), `working/` (iterative drafts), `reference/` (canonical topic docs), `implementation/` (coding + business work queues) | Project root | Evolving. Topic files added as research progresses. `business-priorities.md` added 2026-02-14. | Active |

#### Staging Area (low bar, working notes)

| Document | Role | Location |
|----------|------|----------|
| **learnings.md** | Observations, surprises, process notes. Cheap to write, reviewed in process mode. | `.claude/projects/.../memory/learnings.md` |

#### External Reference Documents (read-only)

These are authored by the user outside coding sessions. Read for context and alignment. Never edit directly. Flag contradictions between these and codebase docs.

All external docs live under `~/Documents/Capitol Labs/` in a stable folder structure:

| Category | Location | Key Files |
|----------|----------|-----------|
| **Strategy & updates** | `~/Documents/Capitol Labs/Strategy & Updates/` | `Capitol Labs - Quarterly Update - February 2026.docx` (final), `Capitol Labs - Q2 2026 Investment Areas - v1.docx`, `naming-briefing-for-claude-code.md`, `Capitol_Labs_Development_Summary.docx` |
| **Messaging/voice** | `~/Documents/Capitol Labs/Strategy & Updates/` | `Capitol Labs - Friends and Family Update - February 2026.docx`, `Capitol Labs - LinkedIn Post - February 2026.docx`, `Capitol Labs - Email Update - February 2026.txt` |
| **Creative brief** | `~/Documents/Capitol Labs/Marketing & Brand/` | `Capitol Labs - Design Ethos & Creative Brief.html` (human-readable export; canonical source is `design-canon.md` in repo root) |
| **Marketing** | `~/Documents/Capitol Labs/Marketing & Brand/` | `CAPITOL LABS - One Pager_Bio.pdf`, `Foundational Page Outlines - Capitol Labs.docx`, logos |
| **Outreach** | `~/Documents/Capitol Labs/Outreach/` | LinkedIn outreach lists, outreach email templates |
| **Product** | `~/Documents/Capitol Labs/Product - Hearing Database/` | Pitch decks, IP summary, product description |

#### Dual-Native Documents

Some documents serve both Claude sessions (as .md in the repo) and the founder (as .html/.docx in ~/Documents). Convention: **the repo .md is the source of truth**. The ~/Documents copy is a human-readable export/snapshot.

| Document | Canonical (Claude-native) | Export (human-native) |
|----------|--------------------------|----------------------|
| Design Canon | `design-canon.md` (repo root) | `~/Documents/Capitol Labs/Marketing & Brand/Capitol Labs - Design Ethos & Creative Brief.html` |
| Quarterly Roadmap | `quarterly-roadmap-q2-2026.md` (repo root) | `~/Documents/Capitol Labs/Strategy & Updates/Capitol Labs - Quarterly Roadmap - Q2 2026.docx` |
| Product Language Doc | *(does not exist yet)* | *(does not exist yet)* |
| Product Messaging | `planning/working/product-messaging.md` | *(no export yet)* |

---

## Mid-Session Topic Deposits

When working in a domain that has a topic file (e.g., design work → `design-review-sessions.md`, SEO work → `seo-readiness.md`), deposit findings to that file **as part of the work** — don't wait for `/remember`. The deposit IS the deliverable, not a separate documentation step.

When updating any topic-centric learning document, **treat it holistically**: read the full document, understand its structure and direction, and integrate new information in a way that improves the document overall. Consolidate, replace stale content, restructure if warranted. Never just append bullets to the bottom. The doc should get *better* with each update, not just longer.

---

## Insight Types

When evaluating what's worth capturing, think across these dimensions:

### Facts (what we know)
- **Technical lesson** — debugging insight, integration gotcha, deployment pitfall
- **Architectural rule** — pattern to follow, file map update, established convention
- **Design decision** — visual choice, component pattern, brand/voice refinement
- **Strategic insight** — product direction, audience learning, competitive positioning
- **Roadmap update** — phase progress, scope change, new work items

### Process (how we work)
- **Workflow discovery** — a step ordering that prevents waste (e.g., "deploy before generating production validation checklists")
- **Collaboration pattern** — a feedback or communication approach that worked well (e.g., "point at one reference design, align everything else to it")
- **Anti-pattern** — something that caused friction or wasted a round
- **Tool/skill insight** — a way of using the skills, agents, or tools that produced better results

### Staleness (what's now wrong)
- **Contradiction** — this session's work conflicts with what a cornerstone doc says
- **Gap** — something important has no documentation home
- **Drift** — a description in CLAUDE.md or the design canon no longer matches reality

### Routing Matrix

After identifying the type (fact/process/staleness), determine the **scope** to route to the right destination:

| Scope | Fact | Process | Staleness |
|-------|------|---------|-----------|
| **Topic-specific** (about design, a feature, an integration) | `planning/learning/<topic>.md` | Same file (methodology/process section) | Flag → discuss → edit the stale doc |
| **Cross-cutting** (about how sessions work, general collaboration) | MEMORY.md | CLAUDE.md Working Principles | Flag → discuss → edit the stale doc |
| **Skill/tool** (about /remember, /kickoff, /validate, etc.) | The skill's own SKILL.md | Same | Same |

**Default bias to watch for:** Observations tend to land in `learnings.md` → MEMORY.md because that's the path of least resistance. Actively check: "Is this really cross-cutting, or does it belong to a topic file in `planning/learning/`?"

**Process observations are easy to miss.** After any multi-round iterative session, explicitly ask: "What worked or didn't work about *how* we worked?" Facts about *what was decided* come naturally; insights about *how the collaboration went* require deliberate reflection.

---

## Mode 1: Capture

**Trigger:** `/remember <something>`

Fast but visible. The goal is to get the observation written down before it's lost. Low bar — this is the notebook, not the published document. But the user should see what's being captured and have a chance to shape it.

### Steps:

1. **Understand the observation.** What type is it? (fact, process, staleness)

2. **Show the user what you'd capture.** Present the draft entry:
   ```
   ## YYYY-MM-DD — [brief label]
   - [The observation, 1-3 lines]
   - Type: [technical / process / design / strategic / staleness]
   - Potential destination: [CLAUDE.md section X / MEMORY.md / design canon / TBD]
   ```
   If you spotted a staleness contradiction, include that too:
   ```
   - !! Contradicts CLAUDE.md line ~N: "[quoted text]"
   ```

3. **Choose the deposit target.** Two options:
   - **`learnings.md`** (default) — for general observations, process insights, staleness flags, and anything that doesn't map to a specific topic.
   - **`planning/learning/<topic>.md`** — when the observation clearly belongs to a specific topic that has a learning file (e.g., analytics, api-as-product, automation, business-viability, design-audit, growth-distribution, intelligence-connectivity, seo-readiness). Deposit directly to the topic file below the `## Findings` marker using the standard research deposit format.

   If the observation maps to a topic, suggest the topic file. If it's general, use learnings.md.

4. **Brief check-in.** Ask: "Capturing this to [target file] — look right?" Keep it lightweight. The user might say "yes", adjust the wording, redirect to a different file, or say "skip it." This is a 10-second pause, not a planning session.

5. **Write to the confirmed target** once confirmed.

No promotion, no edits to cornerstone docs. Just capture.

---

## Mode 2: Process

**Trigger:** `/remember` (no argument)

Collaborative. This is a conversation, not a report. The goal is to review what's accumulated and decide together what matters enough to promote to cornerstone docs.

### Steps:

1. **Gather context.** Read:
   - `learnings.md` (the staging area)
   - `git diff --stat` and recent commits (what happened recently)
   - Skim relevant sections of CLAUDE.md and MEMORY.md

2. **Summarize what's in staging.** Group observations by theme. For each:
   - The observation
   - How many times or sessions it's come up (pattern vs. one-off)
   - Whether it's a fact, process learning, or staleness flag

3. **Apply the relevance filter.** Before proposing any promotions, step back and pressure-test each candidate against the project's current context:

   - **Is this already covered?** Check if CLAUDE.md, the design canon, or an existing MEMORY.md section already says this. Standard behavior of a technology (e.g., "JWTs are stateless") is not a learning — it's just how the thing works. Only capture what's *surprising* or *non-obvious* given the codebase.
   - **Does this connect to active work?** Consider the current Focus, the quarterly roadmap, and open decisions. An observation that's orthogonal to everything in motion has low value even if it's technically true. Where does it sit on the implementation/focus/investment/decision spectrum?
   - **How actionable is it?** A well-scoped bug with a clear fix approach is worth more than a vague "this area is tricky." An unexplained failure with no root cause is a warning, not a learning — capture the approach to avoid, not the mystery.
   - **Is it a fact about the world, or noise from a session?** Debugging frustration, iteration churn, and "we tried X and it didn't work" are session artifacts. Only the *conclusion* (what to do instead) survives.

   Be ruthless. Most sessions produce 1-3 genuinely promotable insights, not 5-7. If you're proposing more than that, you're probably not filtering hard enough.

4. **Start the conversation.** Present only the survivors — the candidates that passed the relevance filter — and for each, explain *why* it's worth constitutional space:
   - "This came up twice now — worth promoting to CLAUDE.md?"
   - "This contradicts what CLAUDE.md says about X — should we update it?"
   - "This process pattern worked well — where should it live?"
   - "This seems like a one-off — discard?"

5. **For each promotion the user approves:**
   - Check for overlap in the destination document
   - Apply concision discipline (see Concision Rules below)
   - Make the edit
   - Report what changed

6. **Clean up staging.** Remove promoted and discarded items from `learnings.md`. Leave items the user wants to sit on.

7. **Staleness sweep.** Based on recent work, scan cornerstone docs for sections that may need updating:
   - Does CLAUDE.md's architecture section still match reality?
   - Does the design canon reflect current patterns?
   - Does the roadmap reflect current priorities?
   - Do any external reference docs contradict recent decisions?

   Flag anything stale. Don't fix it unilaterally — discuss.

---

## Mode 3: Prune

**Trigger:** `/remember prune` or when MEMORY.md is at capacity

Health check on document sizes and freshness.

1. Read the target document(s)
2. Walk through each section and flag:
   - **Duplicate**: Also exists in another cornerstone doc → remove from one
   - **Stale**: No longer accurate based on current codebase → remove or update
   - **Verbose**: Could be said in fewer lines → propose condensed version
   - **Misplaced**: Belongs in a different document → propose move
3. Present all proposed changes as a batch for user approval
4. Report before/after line counts

---

## Concision Rules

These apply whenever writing to cornerstone documents (not to staging).

### MEMORY.md (tightest budget)
- Count current lines. Report: "MEMORY.md is at N/200 lines."
- If N < 150: Add directly.
- If 150 ≤ N < 190: Adding requires a trade. Identify removal/consolidation candidates.
- If N ≥ 190: Refuse to add until pruning happens.

### CLAUDE.md
- Additions should fit into existing sections.
- If a new section is needed, justify why.
- Prefer tightening existing prose over adding new sections.

### Design documentation
- Capture in the best current location. Be explicit about placement.
- Don't force premature structure while the design canon location is settling.

### Formatting
- Bullet points, not paragraphs
- Bold the key term or concept
- Include specific values (not "use the right color" but "`#1a56db`")
- Gotchas lead with consequence: "**CRITICAL**: X causes Y"

---

## Rules

- **Capture is autonomous; promotion is collaborative.** Writing to `learnings.md` doesn't need approval. Writing to cornerstone docs always requires discussion.
- **External docs are read-only reference.** Read for context and alignment. Flag contradictions. Never edit.
- **One insight, one location.** Don't scatter across multiple cornerstone docs.
- **Specificity over generality.** "Deploy before generating production validation checklists" is useful. "Plan ahead" is not.
- **The manifest is the source of truth** for what documents exist and where they live. Update it when the landscape changes.
- **Don't force document structure prematurely.** Some locations are TBD. Acknowledge gaps rather than creating half-baked files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jasonlemming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
