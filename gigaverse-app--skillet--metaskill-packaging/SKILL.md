---
name: metaskill-packaging
description: Package skills, agents, commands, and hooks as Claude Code plugins. Use when creating plugins, packaging skills for distribution, setting up plugin structure, dogfooding plugins, or when user mentions "plugin structure", "plugin.json", "package plugin", "distribute plugin", "marketplace", "dogfood", "install plugin", "plugin placement", "--plugin-dir". Use when this capability is needed.
metadata:
  author: gigaverse-app
---

# Plugin Building and Packaging

Package your skills, agents, commands, and hooks as distributable plugins.

## Plugin Structure

**CRITICAL RULE:** Only `plugin.json` goes inside `.claude-plugin/`. All components go at the plugin ROOT:

```
my-plugin/                      <- Plugin name = neutral noun
├── .claude-plugin/
│   └── plugin.json             # ONLY this file here!
├── commands/                   # Slash commands (*.md) - imperative verbs
├── agents/                     # Agent definitions (*.md) - role nouns
├── skills/                     # Skills (*/SKILL.md) - ending in -ing
├── hooks/                      # Event handlers (hooks.json)
├── .mcp.json                   # MCP servers (optional)
├── .lsp.json                   # LSP servers (optional)
└── README.md
```

```
# ❌ WRONG - components inside .claude-plugin/
.claude-plugin/
├── plugin.json
├── commands/         ← NO!
└── skills/           ← NO!

# ✅ CORRECT - components at plugin root
.claude-plugin/
└── plugin.json
commands/             ← YES!
skills/               ← YES!
```

## plugin.json Manifest

**Required fields:**

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "What this plugin does"
}
```

**With recommended metadata:**

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "What this plugin does",
  "author": {
    "name": "Your Name",
    "email": "you@example.com"
  },
  "license": "MIT",
  "keywords": ["keyword1", "keyword2"],
  "repository": "https://github.com/user/repo",
  "homepage": "https://github.com/user/repo#readme"
}
```

**See `references/plugin-json-schema.md` for the complete field reference.**

## Naming Conventions

**See `/metaskill-naming` for the full naming convention.**

Quick reference:

| Component | Form | Example |
|-----------|------|---------|
| Plugin name | Neutral noun | metaskill, codeforge, datakit |
| Skills | -ing (gerund) | metaskill-authoring, metaskill-triggering |
| Agents | Role noun | metaskill-trigger-tester, metaskill-analyzer |
| Commands | Imperative verb | /metaskill-create, /quick-start |

**Plugin name = Common prefix of all atoms**

```
# ✅ GOOD - neutral noun prefix, correct suffixes
metaskill/
├── skills/
│   ├── metaskill-authoring/     # -ing
│   └── metaskill-triggering/    # -ing
├── agents/
│   └── metaskill-trigger-tester.md  # role noun
└── commands/
    └── quick-start.md           # imperative

# ❌ BAD - verb-form prefix
skill-authoring/
├── skills/
│   └── skill-authoring-trigger/  # prefix already -ing!
```

**No type postfixes:**

```
# ❌ BAD - redundant type postfix
skills/code-review-skill/
agents/tester-agent.md
commands/lint-command.md

# ✅ GOOD - no type postfix
skills/code-reviewing/
agents/tester.md
commands/lint.md
```

## Dogfooding Approaches

### Quick Iteration (Active Development)

```bash
claude --plugin-dir ./my-plugin
```

- Loads plugin immediately
- Restart Claude Code to pick up changes
- Best for rapid iteration

### Marketplace Testing (Pre-Release)

```bash
# Add your repo as a local marketplace (once)
/plugin marketplace add /path/to/your/repo

# Install the plugin
/plugin install your-repo@my-plugin

# After changes, reinstall to test
/plugin uninstall your-repo@my-plugin
/plugin install your-repo@my-plugin
```

- Tests the full installation flow
- Verifies the user experience
- Use before releasing

## Plugin Placement in Repos

### Single Plugin at Repo Root

For a repo that IS the plugin:

```
my-plugin-repo/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── my-plugin-authoring/
├── agents/
└── README.md
```

### Plugin Inside a Project (Dogfooding)

For internal tooling within a larger project:

```
my-project/
├── src/
├── tests/
├── .claude/                  # Project's Claude config
│   └── settings.json
└── plugins/
    └── my-internal-plugin/
        ├── .claude-plugin/
        │   └── plugin.json
        └── skills/
```

Load with: `claude --plugin-dir ./plugins/my-internal-plugin`

### Multiple Plugins in One Repo

Use `marketplace.json` to reference multiple plugins:

```
my-repo/
├── .claude-plugin/
│   └── marketplace.json      # References plugins below
├── plugins/
│   ├── plugin-a/
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   └── skills/
│   └── plugin-b/
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── agents/
└── README.md
```

**marketplace.json** (required fields):

```json
{
  "name": "my-marketplace",
  "owner": {
    "name": "Your Name"
  },
  "plugins": [
    { "name": "plugin-a", "source": "./plugins/plugin-a" },
    { "name": "plugin-b", "source": "./plugins/plugin-b" }
  ]
}
```

**With full metadata:**

```json
{
  "name": "my-marketplace",
  "owner": {
    "name": "Your Name",
    "email": "you@example.com"
  },
  "metadata": {
    "description": "Description of your marketplace",
    "version": "1.0.0"
  },
  "plugins": [
    {
      "name": "plugin-a",
      "source": "./plugins/plugin-a",
      "description": "What plugin-a does",
      "version": "1.0.0",
      "author": { "name": "Your Name", "email": "you@example.com" },
      "license": "MIT",
      "keywords": ["keyword1", "keyword2"],
      "category": "development"
    }
  ]
}
```

**See `references/marketplace-json-schema.md` for the complete field reference.**

Users can then:
```bash
/plugin marketplace add /path/to/my-repo
/plugin install my-marketplace@plugin-a
```

## Internal vs External Plugins

### Internal (Dogfooding within Repo)

Place in a `plugins/` or `tools/` directory:

```
my-project/
├── plugins/
│   └── internal-tooling/     # For this project only
│       ├── .claude-plugin/
│       │   └── plugin.json
│       └── skills/
```

- Not meant for distribution
- Project-specific utilities
- Load with `--plugin-dir`

### External (Open Source / Distribution)

Option A: Dedicated plugin repo
```
my-plugin/                    # Repo IS the plugin
├── .claude-plugin/
│   └── plugin.json
└── skills/
```

Option B: Plugin marketplace repo
```
my-plugins/                   # Repo contains multiple plugins
├── .claude-plugin/
│   └── marketplace.json
└── plugins/
    ├── plugin-a/
    └── plugin-b/
```

## Plugin Components Reference

| Directory | Contents | Naming Pattern |
|-----------|----------|----------------|
| `.claude-plugin/` | `plugin.json` only | N/A |
| `skills/` | `*/SKILL.md` | prefix-action-ing |
| `agents/` | `*.md` | prefix-role-noun |
| `commands/` | `*.md` | imperative-verb |
| `hooks/` | `hooks.json` | N/A |
| `.mcp.json` | MCP servers | N/A |
| `.lsp.json` | LSP servers | N/A |

## Common Mistakes

### Components in Wrong Location

```
# ❌ WRONG
.claude-plugin/
├── plugin.json
└── skills/          # NO! Skills outside .claude-plugin/

# ✅ CORRECT
.claude-plugin/
└── plugin.json
skills/              # YES! At plugin root
```

### Missing plugin.json

```
# ❌ WRONG - no manifest
my-plugin/
└── skills/

# ✅ CORRECT - has manifest
my-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
```

### Verb-Form Prefix

```
# ❌ WRONG - prefix is already -ing
skill-authoring/
├── skills/
│   └── skill-authoring-triggering/  # Double verb!

# ✅ CORRECT - neutral noun prefix
metaskill/
├── skills/
│   └── metaskill-triggering/        # Noun + -ing
```

## Related Skills

- For naming conventions, see `/metaskill-naming`
- For skill structure and writing, see `/metaskill-authoring`
- For skill group patterns, see `/metaskill-grouping`
- For trigger optimization, see `/metaskill-triggering`
- To test if triggers work, use the `metaskill-trigger-tester` agent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
