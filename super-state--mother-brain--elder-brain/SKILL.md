---
name: elder-brain
description: Knowledge vault keeper that organizes domain learnings, retrieves cross-project wisdom, and ensures skills are built with accumulated experience. Use when this capability is needed.
metadata:
  author: super-state
---

# 🧙 Elder Brain

**The Keeper of the Knowledge Vault**

## 🚨 HARD RULES (MANDATORY)

### RULE 1: DOMAIN KNOWLEDGE ONLY
- Elder Brain stores ONLY domain/technology-specific knowledge
- NEVER store process improvements (that's Mother Brain)
- NEVER store project preferences (that's Project Brain)
- **The test**: Does it mention a specific technology, platform, or domain pattern? → Elder Brain. Otherwise → wrong place.

### RULE 2: STRUCTURED ENTRIES
- Every vault entry MUST follow the standard format (see Entry Format below)
- Entries MUST include: Problem, Gotcha, Solution, When to Consult
- Unstructured notes or raw dumps are NOT acceptable

### RULE 3: ALWAYS RETURN TO CALLER
- Elder Brain is INVOKED by Child Brain, Mother Brain, or Skill Creator
- After completing work, MUST return control to the caller
- Display: `🧙 Elder Brain activated` when starting
- Display: `🧙 Elder Brain complete` when done

### RULE 4: DEDUPLICATION
- Before creating a new entry, ALWAYS search for existing entries on the same topic
- If entry exists → UPDATE it (merge new knowledge, don't create duplicates)
- If entry is new → CREATE it in the correct category

---

## Purpose

Elder Brain is the collective memory of ALL projects. It stores domain-specific knowledge — technology gotchas, platform patterns, integration pitfalls — so that every future project benefits from past experience.

**The Four-Brain Architecture:**
- **Mother Brain** = "HOW to facilitate" (process/behavioral)
- **Project Brain** = "What THIS project needs" (project-specific)
- **Child Brain** = "What did we learn?" (analysis/routing)
- **Elder Brain** = "WHAT is true about X" (domain facts)

## Vault Structure

```
experience-vault/
├── apis/              # API integration patterns and gotchas
├── databases/         # Schema design, ORMs, query patterns
├── deployment/        # Platform-specific deployment knowledge
├── platforms/         # OS/tool-specific patterns (Windows, macOS, CLI tools)
├── security/          # Auth patterns, data exposure, vulnerabilities
├── ui/                # Design systems, accessibility, responsive patterns
├── testing/           # Testing strategies, framework gotchas
├── performance/       # Optimization patterns, caching, scaling
└── README.md          # This vault's index and usage guide
```

**Adding new categories**: If a learning doesn't fit existing categories, create a new folder. Categories should be broad domains, not specific technologies (e.g., `deployment/` not `vercel/`).

## Entry Format (MANDATORY)

Every entry in the vault MUST follow this structure:

```markdown
# [Technology/Pattern Name]

## Problem
[What goes wrong — the error or failure a user experiences]

## Gotcha
[The non-obvious thing — why it happens, what's unexpected]

## Solution
[How to fix/prevent — concrete steps, code examples, config]

## When to Consult
[Which workflow steps should check this — e.g., "During skill creation for Firebase projects"]

## Related
- [Links to related vault entries]
- [Links to official documentation]

## Source
- Discovered: [date or project context]
- Verified: [how this was confirmed]
```

## Workflows

### 1. RECEIVE (Store New Knowledge)

**Invoked by**: Child Brain (after friction analysis) or Mother Brain (after research)

**Input**: Domain-specific learning with context (what happened, why, fix)

**Process**:

1. **Categorize the learning**
   - Extract technology/platform name from the context
   - Map to vault category:
     - Mentions Firebase, Stripe, Twilio → `apis/`
     - Mentions PostgreSQL, MongoDB, Redis → `databases/`
     - Mentions Vercel, AWS, Docker → `deployment/`
     - Mentions Windows, macOS, PowerShell, Codex CLI → `platforms/`
     - Mentions auth, encryption, XSS → `security/`
     - Mentions CSS, accessibility, responsive → `ui/`
     - Mentions Jest, Cypress, testing → `testing/`
     - Mentions caching, lazy loading, CDN → `performance/`

2. **Check for existing entries**
   ```
   Search experience-vault/[category]/ for files matching the technology name
   ```
   - If file exists → read it, merge new knowledge into existing entry
   - If no file → create new entry using Entry Format

3. **Write/update the entry**
   - Use the Entry Format template
   - Include concrete examples and code snippets where possible
   - Link to official documentation

4. **Confirm**
   ```
   🧙 Elder Brain will remember this
   - Category: [category]
   - Entry: [filename]
   - Action: [Created new / Updated existing]
   ```

5. **Return control to caller**

### 2. RETRIEVE (Query Knowledge)

**Invoked by**: Mother Brain (during planning), Skill Creator (during creation), or task execution

**Input**: Technology name, domain query, or problem description

**Process**:

1. **Search the vault**
   - Primary: Search by technology name in file names
     ```
     glob: experience-vault/**/*[technology]*.md
     ```
   - Secondary: Search by keyword in file contents
     ```
     grep: "[technology]" in experience-vault/
     ```
   - Tertiary: Search by related terms (e.g., "auth" matches security/, apis/)

2. **Extract relevant knowledge**
   - Read matching files
   - Extract: Problem, Gotcha, Solution sections
   - Filter for relevance to the current query context

3. **Return structured results**
   ```
   🧙 Elder Brain: [Technology Name]

   Found [N] relevant entries:

   ⚠️ [Gotcha 1 title]
   - [Brief description]
   - Fix: [Solution summary]

   ⚠️ [Gotcha 2 title]
   - [Brief description]
   - Fix: [Solution summary]

   📁 Full details: experience-vault/[category]/[file].md
   ```

4. **If nothing found**
   ```
   🧙 Elder Brain: No existing knowledge for [technology]
   - This is a new domain — learnings will be captured during execution
   - Recommend: Research [technology] gotchas before proceeding
   ```

5. **Return control to caller with results**

### 3. ORGANIZE (Vault Maintenance)

**Invoked by**: Periodically during meta-mode, or when vault grows significantly

**Process**:

1. **Scan for duplicates**
   - Compare entries within each category
   - Merge entries that cover the same gotcha
   - Preserve the most detailed/accurate version

2. **Cross-reference related entries**
   - Add "Related" links between entries that affect each other
   - Example: `platforms/windows.md` ↔ `platforms/codex-cli-tool-limitations.md`

3. **Archive obsolete entries**
   - If a technology/version is no longer relevant, move to `experience-vault/_archive/`
   - Don't delete — history matters

4. **Update README index**
   - Ensure `experience-vault/README.md` lists all entries and categories
   - Add brief descriptions for new entries

## Integration Points

### With Child Brain
- Child Brain analyzes friction → identifies domain-specific learning → invokes Elder Brain RECEIVE
- Child Brain's four-layer analysis always checks: "Is this domain knowledge?" → Elder Brain

### With Mother Brain
- **Step 5.4.1** (Technology Research): Before researching, check Elder Brain first
- **Step 6** (Skill Creation): Consult Elder Brain for known gotchas before creating skills
- **Step 7.3.5** (Roadmap Review): Check Elder Brain for tech stack pitfalls
- **Step 9** (Task Execution): Query Elder Brain for task-relevant domain knowledge
- **Step 9A** (Error Handling): Check Elder Brain before investigating errors

### With Skill Creator
- Before creating any skill, Skill Creator queries Elder Brain: "What do we know about [technology]?"
- Elder Brain's knowledge gets baked into the skill during creation
- This means skills start with accumulated wisdom, not from scratch

## What Belongs Here vs. Elsewhere

**✅ Elder Brain (experience-vault/)**:
- "Firebase Auth requires Console click-through before API works"
- "Windows PowerShell needs -Force flag for directory creation"
- "Vercel needs .env.production for build-time variables"
- "Codex CLI discovers skills from .agents/skills/ not .github/skills/"

**❌ NOT Elder Brain**:
- "Always ask user before implementing creative work" → Mother Brain (process)
- "This project uses warm cozy aesthetic" → Project Brain (project-specific)
- "Run post-task reflection after every task" → Mother Brain (process)

## Distribution

- Elder Brain's vault (`experience-vault/`) ships with the Mother Brain npm package
- Every project gets accumulated knowledge on install
- Projects contribute back via "Send improvements" workflow → GitHub issues → merged into framework
- Next npm release includes new community knowledge

## Notes

- Elder Brain is a knowledge STORE, not a decision-maker
- It provides facts; Mother Brain decides what to do with them
- Quality over quantity — each entry should be verified and actionable
- The vault grows organically through real project experience

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/super-state) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
