---
name: metaskill-grouping
description: Create skill groups (multiple related skills packaged as a plugin). Use when creating plugins, organizing multiple related skills, building skill families, packaging tools together, or when user mentions "plugin", "multiple skills", "related skills", "skill group", "skill family", "organize skills", "cross-reference", "package skills", "shared agents". ALWAYS consider this pattern when someone asks to "create a skill" - they often need a skill GROUP packaged as a plugin. Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Skill Groups: The Superior Pattern for Complex Topics

**This skill IS the pattern it teaches.** The metaskill plugin demonstrates skill groups in action.

## The Key Questions

When someone asks to "create a skill", first ask:

> **1. What should I name this?**
> See `/metaskill-naming` for the naming convention (neutral noun prefix, -ing suffix).
>
> **2. Is this a SINGLE skill or a SKILL GROUP?**
> If the topic has 3+ distinct concerns users might approach differently, you need a skill GROUP, not a monolithic skill.

## Why Skill Groups Beat Monolithic Skills

### The Problem with Monolithic Skills

```
big-skill/
  SKILL.md          <- One description (limited trigger opportunities)
  references/       <- OPTIONAL reading (often skipped)
    deep-topic-1.md
    deep-topic-2.md
```

**Issues:**
- Single description = limited trigger keywords
- References are OPTIONAL = deep knowledge often missed
- Skill body becomes huge and unfocused
- Hard to find specific information

### The Skill Group Solution

```
plugin-name/                        <- Neutral noun (e.g., metaskill, codeforge)
  skills/
    plugin-name-doing/              <- ends in -ing
    plugin-name-optimizing/         <- ends in -ing
    plugin-name-building/           <- ends in -ing
```

**Benefits:**
- N descriptions = N× more trigger opportunities
- Each skill body is MANDATORY when triggered (not optional like references)
- Cross-references teach Claude about sibling skills
- Users enter from multiple angles, always get relevant content

## Comparison Table

| Aspect | Monolithic Skill | Skill Group |
|--------|-----------------|-------------|
| Trigger opportunities | 1 description | N descriptions (3x more chances) |
| Body reading | MANDATORY but huge | MANDATORY and focused |
| Reference reading | OPTIONAL (often skipped) | N/A - body IS the content |
| Cross-referencing | Within single file | Across skills (learned dynamically) |
| Discoverability | Limited by single description | Multiple entry points |

## When to Use Skill Groups

**Use a skill group when:**
- Topic has 3+ distinct sub-concerns
- Users might approach from different angles (different trigger words)
- Deep knowledge in each area is important (not optional)
- Skills can stand alone but complement each other

**Use a single skill when:**
- Topic is truly atomic
- All users need all the information
- No natural sub-divisions exist

## The Cross-Reference Magic

When skill A triggers and its body says "See /skill-B for X", Claude:
1. MUST read skill A's body (triggered)
2. LEARNS about skill B's existence
3. May trigger skill B in future (better awareness)

This is **progressive disclosure across skill boundaries**!

## Real Example: This Plugin

```
metaskill/                          <- Plugin name (neutral noun)
  skills/
    metaskill-authoring/            <- "write skill", "create skill" (ends in -ing)
    metaskill-triggering/           <- "trigger", "description" (ends in -ing)
    metaskill-grouping/             <- "plugin", "skill group" (ends in -ing)
    metaskill-packaging/            <- "package", "distribute" (ends in -ing)
    metaskill-naming/               <- "name", "naming convention" (ends in -ing)
  agents/
    metaskill-trigger-tester.md     <- role noun (tester)
```

Each skill body cross-references the others:
- `/metaskill-authoring` says "For groups, see `/metaskill-grouping`"
- `/metaskill-triggering` says "For structure, see `/metaskill-authoring`"
- This skill says "For triggers, see `/metaskill-triggering`"

## Creating a Skill Group

### 1. Choose a Neutral Noun Prefix

See `/metaskill-naming` for the full convention. The plugin name = common prefix = neutral noun.

```
# ✅ GOOD prefixes (nouns)
metaskill, codeforge, datakit, testbench

# ❌ BAD prefixes (already verb-formed)
skill-authoring, code-reviewing, data-processing
```

### 2. Identify Sub-Concerns (with -ing names)

List all aspects of your topic. If 3+ have distinct trigger words, group them:

```
Example: "metaskill" plugin (meta-tooling for skills)
- metaskill-authoring (triggers: "write skill", "create skill")
- metaskill-triggering (triggers: "trigger", "description")
- metaskill-grouping (triggers: "plugin", "skill group")
- metaskill-packaging (triggers: "package", "distribute")
- metaskill-naming (triggers: "name", "naming convention")
```

### 3. Create Plugin Structure

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── my-plugin-doing/        <- ends in -ing
│   │   └── SKILL.md
│   ├── my-plugin-building/     <- ends in -ing
│   │   └── SKILL.md
│   └── my-plugin-testing/      <- ends in -ing
│       └── SKILL.md
├── agents/
│   └── my-plugin-checker.md    <- role noun
└── README.md
```

### 4. Cross-Reference in Every Skill Body

```markdown
## Related Skills

- For naming conventions, see `/my-plugin-naming`
- For building patterns, see `/my-plugin-building`
```

### 5. Verify Cross-References Work

Every skill should reference the other skills in the group.

## Skill Groups Are Best Packaged as Plugins

Skill groups naturally fit the plugin model because plugins can include MORE than just skills:

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json         # Manifest (required)
├── skills/                 # Multiple related skills (-ing names)
│   ├── my-plugin-authoring/
│   ├── my-plugin-optimizing/
│   └── my-plugin-building/
├── agents/                 # Shared helper agents (role nouns)
│   └── my-plugin-analyzer.md
├── commands/               # User-invocable commands (imperative verbs)
│   └── quick-start.md
├── hooks/                  # Event handlers
│   └── hooks.json
└── README.md
```

**Benefits of plugin packaging:**
- Skills can share agents (e.g., a testing plugin shares a test-runner agent)
- Commands provide quick entry points (`/quick-start`)
- Hooks can automate triggers (e.g., auto-invoke skill on file patterns)
- Single installation for the whole toolset
- Versioned and distributable

**For comprehensive plugin packaging guidance, see `/metaskill-packaging`.**

## Common Mistakes

**Don't make single skills too broad:**
```yaml
# ❌ Too broad - should be a group
description: Handles testing, mocking, fixtures, TDD, coverage, debugging...
```

**Don't make groups too granular:**
```yaml
# ❌ Too granular - just use references/
metaskill-yaml/       # Just YAML syntax?
metaskill-markdown/   # Just markdown tips?
```

**Don't use verb-form prefixes:**
```yaml
# ❌ BAD - prefix is already -ing
skill-authoring-trigger/    # "skill-authoring" is -ing!

# ✅ GOOD - neutral noun prefix
metaskill-triggering/       # "metaskill" is noun, "-triggering" is -ing
```

**Do find natural topic boundaries:**
```yaml
# ✅ Each has distinct triggers AND substantial content
metaskill-authoring/        # Structure and writing
metaskill-triggering/       # Trigger optimization
metaskill-grouping/         # The group pattern itself
```

## Related Skills

- For naming conventions, see `/metaskill-naming`
- For skill structure and writing, see `/metaskill-authoring`
- For trigger optimization, see `/metaskill-triggering`
- For plugin packaging and placement, see `/metaskill-packaging`
- To test if triggers work, use the `metaskill-trigger-tester` agent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
