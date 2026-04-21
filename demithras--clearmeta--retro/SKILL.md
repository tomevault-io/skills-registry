---
name: retro
description: Skill & agent-centric retrospective with auto-collection, entity extraction, and skill suggestions. Invoke when: user calls /retro, finishes significant work, or wants to reflect on tooling effectiveness. Captures skill gaps, friction, agent evaluation, extracts entities, and creates improvement issues. Use when this capability is needed.
metadata:
  author: demithras
---

# /retro — Skill & Agent Centric Retrospective

> **TL;DR**: Auto-collect session data (skills, agents, friction), analyze tooling effectiveness, and create actionable improvement issues. The "R" in CLEAR.

---

## Quick Reference

```
/retro                  # Full retro with auto-collection
/retro <issue-id>       # Retro focused on specific work
/retro --quick          # Minimal prompts, max automation
/retro --verbose        # Include all collected data in output
```

---

## Core Philosophy

**Retro is about tooling, not feelings.** *(model)*

Traditional retros ask "what went well/badly?" — subjective and hard to act on. This skill focuses on **tooling effectiveness**:
- Which skills helped? Which caused friction?
- Which agents worked? What's missing?
- What can we automate for next time?

**Key insight**: Every retro feeds `/skill-farm` — the system improves itself.

---

## What Gets Auto-Collected

/retro automatically analyzes the session for:

| Data Type | Source | Purpose |
|-----------|--------|---------|
| **Skills invoked** | `<command-name>` tags, state blocks | Skill usage patterns |
| **Skill outcomes** | State blocks, completion indicators | Success/failure analysis |
| **Agents spawned** | Task tool calls | Agent effectiveness |
| **Friction events** | Tool errors, retries, workarounds | Pain point identification |
| **Entities** | Skills, agents, tools, patterns | Knowledge graph population |

**No manual logging required** — the skill reconstructs this from conversation context.

---

## Entity Extraction

Entities are extracted and persisted to `obsidian-vault/entities/` for knowledge graph building.

### Entity Types

| Type | Source | Example |
|------|--------|---------|
| **skill** | `/skill-name` invocations | `/meta`, `/retro` |
| **agent** | Task tool calls | `Explore`, `general-purpose` |
| **tool** | Tool invocations | `Read`, `Write`, `Bash` |
| **pattern** | Repeated behaviors | "parallel agents", "CLEAR check" |
| **concept** | Key terms mentioned | "L-labels", "AX", "state blocks" |

### Auto-Extraction Rules

```
EXTRACT_ENTITIES():
    entities = []

    # Skills → entity files
    FOR EACH skill IN collected_skills:
        entities.push({
            type: "skill",
            name: skill.skill_name,
            context: skill.outcome,
            frequency: COUNT occurrences
        })

    # Agents → entity files
    FOR EACH agent IN collected_agents:
        entities.push({
            type: "agent",
            name: agent.subagent_type,
            context: agent.description,
            frequency: COUNT occurrences
        })

    # Patterns → extract from repeated behaviors
    IF detected_patterns:
        FOR EACH pattern IN detected_patterns:
            entities.push({
                type: "pattern",
                name: pattern.name,
                context: pattern.description
            })

    RETURN entities
```

### Entity Persistence

```
PERSIST_ENTITIES(entities):
    vault_path = "obsidian-vault/entities"

    FOR EACH entity IN entities:
        filename = SLUGIFY(entity.name) + ".md"
        filepath = vault_path + "/" + filename

        IF EXISTS(filepath):
            # Update existing entity
            content = Read(filepath)
            UPDATE frequency in frontmatter
            ADD new occurrence to table
            Write(filepath, updated_content)
        ELSE:
            # Create new entity from template
            content = GENERATE from templates/entity.md
            FILL IN:
                - entity_type: entity.type
                - name: entity.name
                - description: entity.context
                - frequency: 1
                - last_seen: TODAY
            Write(filepath, content)
```

---

## Data Collection Schema

```yaml
session_data:
  skills:
    - skill_name: string        # e.g., "clarify", "action"
      invoked_at: timestamp
      arguments: string | null
      outcome: "success" | "partial" | "failed" | "interrupted"
      friction_signals:
        - type: "retry" | "workaround" | "abandoned"
          description: string

  agents:
    - subagent_type: string     # e.g., "general-purpose", "Explore"
      description: string
      spawned_at: timestamp
      outcome: "success" | "failed" | "timeout"
      parent_skill: string | null

  friction:
    - tool: string
      error_type: string
      recovery: "retry" | "workaround" | "abandoned" | "asked_user"
      context: string
```

---

## Evaluation Frameworks

### Skill Evaluation

| Analysis | Question | Output |
|----------|----------|--------|
| **Gaps** | What skills were needed but don't exist? | New skill proposals |
| **Friction** | Where did existing skills slow things down? | Improvement suggestions |
| **Coverage** | What should existing skills do better? | Missing capabilities |

### Agent Evaluation

| Analysis | Question | Output |
|----------|----------|--------|
| **Skill Needs** | Which skills should agents have access to? | Agent tool additions |
| **Missing Agents** | What specialized agents would help? | New agent proposals |
| **Orchestration** | How can agent coordination improve? | Pattern improvements |

---

## Output Format

### UX Layer (Human-Readable)

```markdown
## Retro: [Session/Feature Name]

**Session Summary**: X skills | Y agents | Z friction events

---

### 🎯 What Worked
- [Auto-generated + user additions]

### ⚠️ What Didn't Work
- [Auto-generated + user additions]

### 🔄 Patterns Discovered
- [Extracted patterns]

---

### 🛠️ Skill Evaluation

#### Gaps (Missing Skills)
| Gap | Evidence | Suggested Skill | Priority |
|-----|----------|-----------------|----------|

#### Friction (Existing Skills)
| Skill | Issue | Suggested Fix |
|-------|-------|---------------|

#### Coverage (Skill Improvements)
| Skill | Missing Capability | Use Case |
|-------|-------------------|----------|

---

### 🤖 Agent Evaluation

#### Skill Needs
| Agent Type | Needed Skill | Rationale |
|------------|--------------|-----------|

#### Missing Agents
| Proposed Agent | Use Case | Skills Needed |
|----------------|----------|---------------|

#### Orchestration
| Current Pattern | Improvement |
|-----------------|-------------|

---

### 🔍 Entities Extracted

| Entity | Type | File |
|--------|------|------|
| /meta | skill | [[entities/meta]] |
| Explore | agent | [[entities/explore-agent]] |

**New**: 2 | **Updated**: 1

---

### 💡 Skill Suggestions

| Type | Name/Target | Reason |
|------|-------------|--------|
| New skill | /canvas-gen | Repeated agent pattern (2x) |
| Improve | /meta | Missing "quick mode" capability |

**Actions**: `/skill-farm create canvas-gen` | `/skill-farm improve meta`

---

### 📋 Issues Created
- meta-xxx: [SKILL] skillname: fix description
- meta-yyy: [NEW SKILL] proposal

Ready for → next session | /skill-farm | entities viewable in Obsidian
```

### AX Layer (Agent-Parseable)

```
<!-- retro:state
session_id: "..."
summary: {skills_used: X, agents_spawned: Y, friction_events: Z, entities_extracted: N}
collected_data: {
  skills: [...],
  agents: [...],
  friction: [...]
}
skill_evaluation: {
  gaps: [{description, evidence, suggested_skill, priority}],
  friction: [{skill_name, issue, suggested_fix}],
  coverage: [{skill_name, missing_capability, use_case}]
}
agent_evaluation: {
  skill_needs: [{agent_type, needed_skill, rationale}],
  missing_agents: [{description, use_case, skills_needed}],
  orchestration: [{current_pattern, improvement}]
}
entities_extracted: [
  {type: "skill", name: "/meta", file: "entities/meta.md"},
  {type: "agent", name: "Explore", file: "entities/explore-agent.md"}
]
skill_suggestions: [
  {type: "new_skill", name: "canvas-gen", reason: "Repeated agent pattern", command: "/skill-farm create canvas-gen"},
  {type: "improve_skill", skill: "meta", capability: "quick mode", command: "/skill-farm improve meta"}
]
issues_created: ["meta-xxx", "meta-yyy"]
handoff: {
  skill_farm: {applicable: true, issues: [...], suggestions: [...]},
  entities: {created: [...], updated: [...]}
}
/retro:state -->
```

---

## Agent Execution Guide

```
1. PARSE input and flags:
   IF --quick flag → minimal_prompts = true
   IF --verbose flag → show_raw_data = true
   IF <issue-id> provided → scope = specific_issue
   ELSE → scope = full_session

2. COLLECT session data:
   2.1 COLLECT_SKILLS():
       skills = []

       # Pattern 1: Command tags
       FOR EACH match of /<command-name>\/(\w+)<\/command-name>/ in conversation:
           skills.push({skill_name: match[1], source: "command"})

       # Pattern 2: State blocks
       FOR EACH match of /<!-- (\w+):state[\s\S]*?\/\1:state -->/:
           skill = skills.find(s => s.skill_name == match[1])
           IF skill:
               skill.outcome = PARSE state for "met: true" → "success" ELSE "partial"
           ELSE:
               skills.push({skill_name: match[1], outcome: "success", source: "state_block"})

       RETURN deduplicate(skills)

   2.2 COLLECT_AGENTS():
       agents = []

       FOR EACH Task tool call in conversation:
           agents.push({
               subagent_type: EXTRACT subagent_type param,
               description: EXTRACT description param,
               outcome: IF result message exists → "success" ELSE "unknown"
           })

       RETURN agents

   2.3 COLLECT_FRICTION():
       friction = []

       # Look for error patterns
       FOR EACH tool error or retry pattern:
           friction.push({
               tool: identified_tool,
               error_type: categorize_error(),
               recovery: detect_recovery_action()
           })

       RETURN friction

3. EVALUATE skills:
   3.1 ANALYZE_GAPS():
       gaps = []

       # Friction with workaround = potential skill gap
       FOR EACH f IN friction WHERE f.recovery == "workaround":
           gaps.push({
               description: f.context,
               evidence: [f],
               suggested_skill: INFER from workaround pattern,
               priority: "medium"
           })

       RETURN gaps

   3.2 ANALYZE_FRICTION():
       skill_friction = []

       FOR EACH skill IN skills WHERE skill.outcome != "success":
           skill_friction.push({
               skill_name: skill.skill_name,
               issue: INFER from friction_signals or outcome,
               suggested_fix: GENERATE improvement suggestion
           })

       RETURN skill_friction

   3.3 ANALYZE_COVERAGE():
       coverage = []

       # Agent work that could be a skill
       FOR EACH agent IN agents WHERE agent.description matches repeatable pattern:
           coverage.push({
               skill_name: INFER target skill,
               missing_capability: agent.description,
               use_case: INFER from context
           })

       RETURN coverage

4. EVALUATE agents:
   4.1 ANALYZE_SKILL_NEEDS():
       needs = []

       FOR EACH agent failure or limitation:
           needs.push({
               agent_type: agent.subagent_type,
               needed_skill: INFER from failure context,
               rationale: "Would have helped with X"
           })

       RETURN needs

   4.2 ANALYZE_MISSING_AGENTS():
       missing = []

       # Repeated patterns that could be specialized agents
       FOR EACH repeated_pattern IN agent_descriptions:
           missing.push({
               description: "Specialized agent for " + pattern,
               use_case: pattern_context,
               skills_needed: INFER required tools
           })

       RETURN missing

   4.3 ANALYZE_ORCHESTRATION():
       orchestration = []

       # Sequential work that could be parallel
       IF detected sequential agents without dependencies:
           orchestration.push({
               current_pattern: "Sequential execution",
               improvement: "Parallel execution possible"
           })

       RETURN orchestration

5. PROMPT user (unless --quick):
   IF NOT minimal_prompts:
       AskUserQuestion([
           {
               question: "Any additional observations about what worked well?",
               header: "Worked",
               options: [
                   {label: "Skills were helpful", description: "The skills invoked did their job"},
                   {label: "Agents were effective", description: "Subagents completed work successfully"},
                   {label: "Smooth workflow", description: "No significant friction"},
                   {label: "Nothing to add", description: "Auto-collected data is sufficient"}
               ],
               multiSelect: true
           },
           {
               question: "Any friction points the system missed?",
               header: "Friction",
               options: [
                   {label: "Skill was confusing", description: "Had to re-read or retry"},
                   {label: "Missing capability", description: "Wanted something that doesn't exist"},
                   {label: "Agent failed", description: "Subagent didn't complete as expected"},
                   {label: "None missed", description: "Auto-collection captured everything"}
               ],
               multiSelect: true
           }
       ])

       INCORPORATE user_responses into evaluation

6. EXTRACT and PERSIST entities:
   entities = EXTRACT_ENTITIES()  # See Entity Extraction section
   PERSIST_ENTITIES(entities)      # Write to obsidian-vault/entities/

   # Track what was extracted
   entities_created = []
   entities_updated = []
   FOR EACH entity IN entities:
       IF entity was new:
           entities_created.push(entity.name)
       ELSE:
           entities_updated.push(entity.name)

7. CREATE beads issues:
   issues_created = []

   # High/medium priority gaps → new skill issues
   FOR EACH gap IN gaps WHERE gap.priority IN ["high", "medium"]:
       priority_num = IF gap.priority == "high" THEN 1 ELSE 2
       result = Bash("bd create --title='[NEW SKILL] {gap.suggested_skill}' --type=feature --priority={priority_num}")
       issues_created.push(EXTRACT issue_id from result)

   # Friction fixes → improvement issues
   FOR EACH friction IN skill_friction:
       result = Bash("bd create --title='[SKILL] {friction.skill_name}: {friction.suggested_fix}' --type=task --priority=2")
       issues_created.push(EXTRACT issue_id from result)

   # Agent needs → agent issues
   FOR EACH need IN skill_needs:
       result = Bash("bd create --title='[AGENT] {need.agent_type}: add {need.needed_skill}' --type=task --priority=3")
       issues_created.push(EXTRACT issue_id from result)

9. GENERATE output:
   9.1 OUTPUT UX layer:
       - Session summary with counts
       - What worked / What didn't (auto + user)
       - Skill evaluation tables
       - Agent evaluation tables
       - Entities extracted section
       - Issues created list

   9.2 OUTPUT AX layer:
       <!-- retro:state ... -->

10. CLEAR check:
    OUTPUT "📋 CLEAR: C✓ L✓ E✓ A✓ R✓ | Retro complete"

    IF issues_created.length > 0:
        OUTPUT "→ /skill-farm can process {issues_created.length} improvement issues"

    IF entities_created.length > 0:
        OUTPUT "→ {entities_created.length} new entities in obsidian-vault/entities/"

11. ERROR recovery:
   IF collection fails → WARN, proceed with partial data
   IF bd create fails → WARN, output markdown-only (no persistence)
   IF no data collected → prompt user for manual input
```

---

## Integration Notes

### Downstream: /skill-farm

Improvement issues feed into `/skill-farm`:

```
/retro → [SKILL] issues → /skill-farm → improved skills
```

### Auto Skill Suggestions

/retro automatically detects patterns that indicate new skills should be created:

| Pattern | Detection | Suggested Action |
|---------|-----------|------------------|
| **Repeated workaround** | Same friction type 2+ times | New skill to automate |
| **Agent doing skill work** | Agent description matches skill pattern | Formalize as skill |
| **Manual multi-step** | User did 3+ steps that could be one command | New skill to bundle |
| **Coverage gap** | Existing skill lacks needed capability | Skill improvement |

**Detection Logic**:

```
SUGGEST_SKILLS():
    suggestions = []

    # Pattern 1: Repeated workarounds → new skill
    workaround_groups = GROUP friction BY context_similarity
    FOR EACH group WHERE group.count >= 2:
        suggestions.push({
            type: "new_skill",
            name: INFER from workaround pattern,
            reason: "Repeated workaround detected ({group.count}x)",
            evidence: group.items
        })

    # Pattern 2: Agent work → formalize as skill
    FOR EACH agent IN agents:
        IF agent.description matches /implement|create|generate|validate/:
            IF similar_agent_spawned_before(agent):
                suggestions.push({
                    type: "new_skill",
                    name: INFER from agent.description,
                    reason: "Repeated agent pattern could be a skill",
                    evidence: [agent]
                })

    # Pattern 3: Coverage gaps
    FOR EACH friction IN skill_friction:
        IF friction.issue indicates "missing capability":
            suggestions.push({
                type: "improve_skill",
                skill: friction.skill_name,
                capability: friction.issue,
                reason: "Skill coverage gap"
            })

    RETURN suggestions
```

**Output Section**:

```markdown
### 💡 Skill Suggestions

| Type | Name/Target | Reason | Action |
|------|-------------|--------|--------|
| New skill | /canvas-gen | Repeated agent pattern | `/skill-farm create canvas-gen` |
| Improve | /meta | Missing "quick mode" | `/skill-farm improve meta` |

Run suggested commands or dismiss: [Create All] [Skip]
```

---

## When to Run /retro

**Good times to run**:
- End of work session
- After significant friction encountered
- Want to capture patterns for reuse
- Closing significant beads issues

**Skip when**:
- Trivial single-step tasks
- Pure exploration with no execution

---

## Examples

### Example 1: After Significant Work

```
/retro

## Retro: Export Feature Implementation

**Session Summary**: 2 skills | 3 agents | 1 friction event

### 🎯 What Worked
- /meta analysis helped choose approach
- Parallel agent execution saved time (tasks 2 & 3)

### ⚠️ What Didn't Work
- Agent prompt was too vague, needed retry

### 🛠️ Skill Evaluation

#### Friction
| Skill | Issue | Suggested Fix |
|-------|-------|---------------|
| - | Agent prompt unclear | Add prompt templates |

### 📋 Issues Created
- meta-abc: [SKILL] Add agent prompt templates

📋 CLEAR: C✓ L✓ E✓ A✓ R✓ | Retro complete
```

### Example 2: Quick Mode

```
/retro --quick

## Retro: Session Summary

**Auto-collected**: 2 skills | 1 agent | 0 friction

No significant issues detected. Session was smooth.

📋 CLEAR: C✓ L✓ E✓ A✓ R✓
```

---

## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Run after every tiny task | Reserve for significant work |
| Skip issue creation | Persist actionable improvements |
| Ignore agent evaluation | Both skills AND agents matter |
| Over-prompt the user | Auto-collect first, ask only if needed |

---

## Error Handling

| Condition | Recovery |
|-----------|----------|
| No session data found | Prompt user for manual reflection |
| bd create fails | Output markdown, skip persistence |
| Collection incomplete | Proceed with partial data, note gaps |
| User skips all prompts | Generate from auto-collected only |

---

## CLEAR Check (Required Exit)

Every `/retro` invocation ends with:

```
📋 CLEAR: C✓ L✓ E✓ A✓ R✓ | Retro complete
```

If issues were created:
```
→ /skill-farm can process N improvement issues
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2026-01-26 | Major rewrite: skill-centric + agent-centric, auto-collection, beads issues |
| 1.0.0 | 2026-01-21 | MVP: list closed, reflect, adaptive prompts, dual storage |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demithras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
