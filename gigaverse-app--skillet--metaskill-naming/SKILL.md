---
name: metaskill-naming
description: Brainstorm and validate names for plugins, skills, agents, and commands. Use when naming a new plugin, choosing atom names, validating naming conventions, or when user mentions "name plugin", "name skill", "naming convention", "brainstorm names", "what should I call", "plugin name", "good name for". Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Naming Plugins and Atoms

Brainstorm and validate names for plugins and their components (atoms = skills/agents/commands).

## The Core Convention

### Verb Form by Type

| Type | Grammatical Form | Meaning | Suffix Pattern |
|------|-----------------|---------|----------------|
| **Skill** | Gerund (-ing) | "the activity of..." | -ing |
| **Agent** | Agent noun | "one who does..." | -er, -or, role noun |
| **Command** | Imperative | "do this" | base verb |

### The Golden Rule

**Plugin name = Common prefix of all atoms = Neutral noun**

```
✅ GOOD prefix (noun):
   skillsmith-    → skillsmith-authoring, skillsmith-tester, /skillsmith-create
   codeforge-     → codeforge-reviewing, codeforge-reviewer, /codeforge-review
   datakit-       → datakit-processing, datakit-processor, /datakit-process

❌ BAD prefix (already verb-formed):
   skill-authoring-  → skill-authoring-??? (prefix is already -ing!)
   code-review-      → code-review-??? (prefix looks like imperative!)
```

### No Type Postfixes

```
✅ GOOD: metaskill-authoring, metaskill-tester
❌ BAD:  metaskill-authoring-skill, metaskill-tester-agent
```

The directory structure already indicates the type.

## Brainstorming Process

### Step 1: Identify the Domain

What is this plugin about? Write 3-5 words describing the core activity:
- "creating skills for Claude Code"
- "reviewing code for security"
- "managing database migrations"

### Step 2: Find Neutral Noun Candidates

Transform the domain into neutral noun forms:

| Domain | Noun Candidates |
|--------|-----------------|
| creating skills | skillsmith, skillforge, skillcraft, skillkit |
| reviewing code | codeforge, codescan, codeguard, codewatch |
| managing databases | datakit, dataforge, dbsmith, migrator |

**Naming patterns that work:**
- **-smith**: One who forges (skillsmith, codesmith)
- **-forge**: Place where things are made (skillforge, codeforge)
- **-craft**: The craft of making (skillcraft, codecraft)
- **-kit**: Toolkit for (skillkit, datakit)
- **-bench**: Workbench for (skillbench, codebench)
- **-works**: Workshop (skillworks, dataworks)
- **meta-**: Meta-level tools (metaskill, metadata)
- **-guard/-watch**: Protective monitoring (codeguard, datawatch)

### Step 3: Generate Atom Names

For each candidate prefix, generate the full set:

```
Prefix: skillsmith
├── Skills (-ing):
│   ├── skillsmith-authoring
│   ├── skillsmith-triggering
│   └── skillsmith-packaging
├── Agents (role noun):
│   ├── skillsmith-trigger-tester
│   └── skillsmith-quality-checker
└── Commands (imperative):
    ├── /skillsmith-create
    └── /skillsmith-test
```

### Step 4: Validate Names

Check each name against:

- [ ] **Prefix is neutral noun** - not a verb form
- [ ] **Skills end in -ing** - gerund form
- [ ] **Agents end in role noun** - -er/-or or natural role
- [ ] **Commands end in imperative** - base verb
- [ ] **No type postfixes** - no -skill, -agent, -command
- [ ] **Pronounceable** - can say it out loud
- [ ] **Memorable** - distinctive enough to remember
- [ ] **No conflicts** - doesn't clash with existing plugins

### Step 5: Test with Full Table

| Prefix | Skill | Agent | Command |
|--------|-------|-------|---------|
| skillsmith | skillsmith-authoring | skillsmith-author | /skillsmith-author |
| | skillsmith-triggering | skillsmith-trigger-tester | /skillsmith-trigger |
| | skillsmith-grouping | skillsmith-group-designer | /skillsmith-group |

Does the table look consistent? Do the names feel natural?

## Example: This Plugin

**Domain:** "meta-tooling for creating Claude Code skills"

**Brainstorming:**
- skillsmith, skillforge, skillcraft → good but generic
- metaskill → captures "meta" aspect, clear noun
- skillet → fun but might confuse with cooking

**Chosen:** `metaskill`

| Type | Name | Follows Convention? |
|------|------|---------------------|
| Plugin | metaskill | ✓ Neutral noun |
| Skill | metaskill-authoring | ✓ Ends in -ing |
| Skill | metaskill-triggering | ✓ Ends in -ing |
| Skill | metaskill-grouping | ✓ Ends in -ing |
| Skill | metaskill-packaging | ✓ Ends in -ing |
| Skill | metaskill-naming | ✓ Ends in -ing |
| Agent | metaskill-trigger-tester | ✓ Ends in role noun |

## Common Pitfalls

### 1. Verb-Form Prefix

```
❌ skill-authoring-trigger    # "skill-authoring" is already -ing
✅ metaskill-triggering       # "metaskill" is noun, "-triggering" is -ing
```

### 2. Awkward -ing Forms

When -ing sounds unnatural, rephrase the activity:

```
❌ metaskill-trigger          # Missing -ing
❌ metaskill-triggering       # Could mean "firing events"
✅ metaskill-triggering       # Context: "configuring triggers" (acceptable)
✅ metaskill-trigger-tuning   # Alternative: explicit "tuning"
```

### 3. Conflicting Meanings

Test if the name could mean something else:
- "triggering" → firing events? or configuring triggers?
- Context usually disambiguates, but consider alternatives if confusing

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────────┐
│                    NAMING CONVENTION                            │
├─────────────────────────────────────────────────────────────────┤
│  Plugin Name = Common Prefix = NEUTRAL NOUN                     │
│                                                                 │
│  ┌──────────┬────────────────────┬─────────────────────────┐   │
│  │   Type   │   Suffix Form      │   Example               │   │
│  ├──────────┼────────────────────┼─────────────────────────┤   │
│  │ Skill    │ -ing (gerund)      │ metaskill-authoring     │   │
│  │ Agent    │ -er/-or (role)     │ metaskill-trigger-tester│   │
│  │ Command  │ imperative verb    │ /metaskill-create       │   │
│  └──────────┴────────────────────┴─────────────────────────┘   │
│                                                                 │
│  ❌ NO type postfixes: -skill, -agent, -command                 │
│  ❌ NO verb-form prefixes: reviewing-, authoring-               │
└─────────────────────────────────────────────────────────────────┘
```

## Related Skills

- For skill structure and writing, see `/metaskill-authoring`
- For trigger optimization, see `/metaskill-triggering`
- For skill group patterns, see `/metaskill-grouping`
- For plugin packaging, see `/metaskill-packaging`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
