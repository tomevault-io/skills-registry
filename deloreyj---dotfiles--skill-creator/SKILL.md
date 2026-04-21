---
name: skill-creator
description: Guide for creating effective skills. This skill should be used when users want to create a new skill (or update an existing skill) that extends an agent's capabilities with specialized knowledge, workflows, or tool integrations. Use when this capability is needed.
metadata:
  author: deloreyj
---

# Skill Creator

This skill provides guidance for creating effective skills.

> **CRITICAL: YAML FRONTMATTER REQUIRED**
> Every SKILL.md **MUST** begin with YAML frontmatter on line 1. Without it, the skill will not load.
> ```yaml
> ---
> name: skill-name
> description: One-line description of what this skill does
> ---
> ```

## About Skills

Skills are modular, self-contained packages that extend an agent's capabilities by providing
specialized knowledge, workflows, and tools. Think of them as "onboarding guides" for specific
domains or tasks—they transform a general-purpose agent into a specialized agent
equipped with procedural knowledge that no model can fully possess.

### What Skills Provide

1. Specialized workflows - Multi-step procedures for specific domains
2. Tool integrations - Instructions for working with specific file formats or APIs
3. Domain expertise - Company-specific knowledge, schemas, business logic
4. Bundled resources - Scripts, references, and assets for complex and repetitive tasks

### Anatomy of a Skill

Every skill consists of a required SKILL.md file and optional bundled resources:

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required)
│   │   └── description: (required)
│   └── Markdown instructions (required)
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Node.js/Python/etc.)
    ├── references/       - Documentation intended to be loaded into context as needed
    └── assets/           - Files used in output (templates, icons, fonts, etc.)
```

#### Requirements (important)

- Skill should be combined into specific topics, for example: `cloudflare`, `cloudflare-r2`, `cloudflare-workers`, `docker`, `gcloud` should be combined into `devops`.
- Keep `SKILL.md` under ~500 lines. Move deep reference material into `references/` or `assets/` so agents can load it on demand.
- Keep scripts and referenced markdowns concise; split into multiple files to follow progressive disclosure.
- `description` in `SKILL.md` metadata should be concise but include enough use cases for reliable activation.
- **Referenced markdowns**:
  - Sacrifice grammar for the sake of concision when writing these files.
  - Can reference other markdown files or scripts as well.
- **Referenced scripts**:
  - Prefer Node.js or Python scripts for exposed automation; default to Node.js unless a compelling reason requires Python.
  - Prefer CLI-style scripts so agents can discover usage with `--help` and invoke them consistently.
  - Make sure scripts respect `.env` file follow this order: `process.env` > `.opencode/skill/${SKILL}/.env` > `.opencode/skill/.env` > `.opencode/.env`.
  - Create `.env.example` file to show the required environment variables.
  - Always write tests for these scripts.

**Why?**
Better **context engineering**: inspired from **progressive disclosure** technique of Agent Skills, when agent skills are activated, the agent will consider to load only relevant files into the context, instead of reading all long `SKILL.md` as before.

#### SKILL.md (required)

**File name:** `SKILL.md` (uppercase)  
**File size:** Under ~500 lines; split to `references/` if needed.

**YAML Frontmatter (REQUIRED - DO NOT SKIP)**

Every SKILL.md **MUST** begin with YAML frontmatter on line 1. No blank lines before it.

```yaml
---
name: skill-name
description: What this skill does and when to use it. Use third-person.
---
```

**Required fields:**
- `name` — hyphen-case identifier matching directory name
- `description` — activation trigger; be specific about WHEN to use

**Optional fields** (`license`, `compatibility`, `metadata`, `allowed-tools`):

```yaml
---
name: skill-name
description: What this skill does and when to use it.
license: Apache-2.0
compatibility: Requires node 20+ and sqlite3
metadata:
  author: example-org
allowed-tools: Bash(git:*) Read
---
```

**INVALID - Do NOT use:**
- XML-style tags (`<purpose>`, `<references>`, `<description>`)
- Missing `---` delimiters
- Frontmatter that doesn't start at line 1
- Blank lines before frontmatter

**Metadata Quality:** `name` and `description` determine skill activation. Be specific; use third-person ("This skill should be used when...").

#### Bundled Resources (optional)

##### Scripts (`scripts/`)

Executable code (Node.js/Python/etc.) for tasks that require deterministic reliability or are repeatedly rewritten.

- **When to include**: When the same code is being rewritten repeatedly or deterministic reliability is needed
- **Default language**: Use Node.js unless a compelling reason requires Python
- **Preferred shape**: Build CLI-style scripts with `--help` so agents can discover usage and invoke them consistently
- **Example**: `scripts/rotate_pdf.js` for PDF rotation tasks
- **Benefits**: Token efficient, deterministic, may be executed without loading into context
- **Note**: Scripts may still need to be read by the agent for patching or environment-specific adjustments

##### References (`references/`)

Documentation and reference material intended to be loaded as needed into context to inform the agent's process and thinking.

- **When to include**: For documentation that the agent should reference while working
- **Examples**: `references/finance.md` for financial schemas, `references/mnda.md` for company NDA template, `references/policies.md` for company policies, `references/api_docs.md` for API specifications
- **Use cases**: Database schemas, API documentation, domain knowledge, company policies, detailed workflow guides
- **Benefits**: Keeps SKILL.md lean, loaded only when agent determines it's needed
- **Best practice**: If files are large (>10k words), include grep search patterns in SKILL.md
- **Avoid duplication**: Information should live in either SKILL.md or references files, not both. Prefer references files for detailed information unless it's truly core to the skill—this keeps SKILL.md lean while making information discoverable without hogging the context window. Keep only essential procedural instructions and workflow guidance in SKILL.md; move detailed reference material, schemas, and examples to references files.

##### Assets (`assets/`)

Files not intended to be loaded into context, but rather used within the output the agent produces.

- **When to include**: When the skill needs files that will be used in the final output or storage that the CLI can read/write
- **Examples**: `assets/logo.png` for brand assets, `assets/slides.pptx` for PowerPoint templates, `assets/frontend-template/` for HTML/React boilerplate, `assets/font.ttf` for typography, `assets/knowledge.sqlite` for skill-local storage
- **Use cases**: Templates, images, icons, boilerplate code, fonts, sample documents that get copied or modified, SQLite databases for CLI-backed storage
- **Benefits**: Separates output resources from documentation, enables agent to use files without loading them into context

### Progressive Disclosure Design Principle

Skills use a three-level loading system to manage context efficiently:

1. **Metadata (name + description)** - Always in context (~100 words)
2. **SKILL.md body** - When skill triggers (<5k words)
3. **Bundled resources** - As needed by agent (Unlimited*)

*Unlimited because scripts can be executed without reading into context window.

## Skill Creation Process

To create a skill, follow the "Skill Creation Process" in order, skipping steps only if there is a clear reason why they are not applicable.

### Step 1: Understanding the Skill with Concrete Examples

Skip this step only when the skill's usage patterns are already clearly understood. It remains valuable even when working with an existing skill.

To create an effective skill, clearly understand concrete examples of how the skill will be used. This understanding can come from either direct user examples or generated examples that are validated with user feedback.

For example, when building an image-editor skill, relevant questions include:

- "What functionality should the image-editor skill support? Editing, rotating, anything else?"
- "Can you give some examples of how this skill would be used?"
- "I can imagine users asking for things like 'Remove the red-eye from this image' or 'Rotate this image'. Are there other ways you imagine this skill being used?"
- "What would a user say that should trigger this skill?"

To avoid overwhelming users, avoid asking too many questions in a single message. Start with the most important questions and follow up as needed for better effectiveness.

Conclude this step when there is a clear sense of the functionality the skill should support.

### Step 2: Planning the Reusable Skill Contents

To turn concrete examples into an effective skill, analyze each example by:

1. Considering how to execute on the example from scratch
2. Identifying what scripts, references, and assets would be helpful when executing these workflows repeatedly

Example: When building a `pdf-editor` skill to handle queries like "Help me rotate this PDF," the analysis shows:

1. Rotating a PDF requires re-writing the same code each time
2. A `scripts/rotate_pdf.js` script would be helpful to store in the skill

Example: When designing a `frontend-webapp-builder` skill for queries like "Build me a todo app" or "Build me a dashboard to track my steps," the analysis shows:

1. Writing a frontend webapp requires the same boilerplate HTML/React each time
2. An `assets/hello-world/` template containing the boilerplate HTML/React project files would be helpful to store in the skill

Example: When building a `big-query` skill to handle queries like "How many users have logged in today?" the analysis shows:

1. Querying BigQuery requires re-discovering the table schemas and relationships each time
2. A `references/schema.md` file documenting the table schemas would be helpful to store in the skill

To establish the skill's contents, analyze each concrete example to create a list of the reusable resources to include: scripts, references, and assets.

### Step 3: Initializing the Skill

At this point, it is time to actually create the skill.

Skip this step only if the skill being developed already exists, and iteration or packaging is needed. In this case, continue to the next step.

When creating a new skill from scratch, always run the `init_skill.js` script. The script conveniently generates a new template skill directory that automatically includes everything a skill requires, making the skill creation process much more efficient and reliable.

Usage:

```bash
scripts/init_skill.js <skill-name> --path <output-directory>
```

Tests:

```bash
scripts/quick_validate.test.js
scripts/package_skill.test.js
```

The script:

- Creates the skill directory at the specified path
- Generates a SKILL.md template with proper frontmatter and TODO placeholders
- Creates example resource directories: `scripts/`, `references/`, and `assets/`
- Adds example files in each directory that can be customized or deleted

CLI usage:

```bash
scripts/init_skill.js --help
```

After initialization, customize or remove the generated SKILL.md and example files as needed.

### Step 4: Edit the Skill

When editing the (newly-generated or existing) skill, remember that the skill is being created for an agent to use. Focus on including information that would be beneficial and non-obvious to the agent. Consider what procedural knowledge, domain-specific details, or reusable assets would help the agent execute these tasks more effectively.

#### Start with Reusable Skill Contents

To begin implementation, start with the reusable resources identified above: `scripts/`, `references/`, and `assets/` files. Note that this step may require user input. For example, when implementing a `brand-guidelines` skill, the user may need to provide brand assets or templates to store in `assets/`, or documentation to store in `references/`.

Also, delete any example files and directories not needed for the skill. The initialization script creates example files in `scripts/`, `references/`, and `assets/` to demonstrate structure, but most skills won't need all of them.

#### Update SKILL.md

**Writing Style:** Write the entire skill using **imperative/infinitive form** (verb-first instructions), not second person. Use objective, instructional language (e.g., "To accomplish X, do Y" rather than "You should do X" or "If you need to do X"). This maintains consistency and clarity for AI consumption.

To complete SKILL.md, answer the following questions:

1. What is the purpose of the skill, in a few sentences?
2. When should the skill be used?
3. In practice, how should the agent use the skill? All reusable skill contents developed above should be referenced so that the agent knows how to use them.

### Step 5: Packaging a Skill

Once the skill is ready, it should be packaged into a distributable zip file that gets shared with the user. The packaging process automatically validates the skill first to ensure it meets all requirements:

```bash
scripts/package_skill.js <path/to/skill-folder>
```

Optional output directory specification:

```bash
scripts/package_skill.js <path/to/skill-folder> ./dist
```

CLI usage:

```bash
scripts/package_skill.js --help
```

Tests:

```bash
scripts/quick_validate.test.js
scripts/package_skill.test.js
```

The packaging script will:

1. **Validate** the skill automatically, checking:
   - YAML frontmatter format and required fields
   - Skill naming conventions and directory structure
   - Description completeness and quality
   - File organization and resource references

2. **Package** the skill if validation passes, creating a zip file named after the skill (e.g., `my-skill.zip`) that includes all files and maintains the proper directory structure for distribution.

If validation fails, the script will report the errors and exit without creating a package. Fix any validation errors and run the packaging command again.

### Step 6: Iterate

After testing the skill, users may request improvements. Often this happens right after using the skill, with fresh context of how the skill performed.

**Iteration workflow:**
1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Identify how SKILL.md or bundled resources should be updated
4. Implement changes and test again

## Storage and CLI Patterns

Skills can ship their own storage and an agent-facing CLI. The spec allows data files in `assets/` and executables in `scripts/`.

Preferred runtime:

- Default to Node.js for the CLI and helpers; use Python only when there is a compelling reason.

Suggested layout:

```
skill-name/
├── SKILL.md
├── scripts/
│   └── skill-cli
├── assets/
│   └── knowledge.sqlite
└── references/
    └── SCHEMA.md
```

Notes:

- Put SQLite files in `assets/` and document read/write expectations.
- Make the CLI self-documenting with `--help`, but still include examples in `SKILL.md`.
- Call out runtime dependencies (`sqlite3`, `node`, etc.) in `compatibility`.
- If the CLI writes to storage, instruct the agent to confirm with the user first.

## Pre-Submission Checklist

Before packaging, verify:

- [ ] **SKILL.md starts with `---`** (YAML frontmatter, line 1, no blank lines before)
- [ ] **`name:` field present** and matches directory name
- [ ] **`description:` field present** with specific activation triggers
- [ ] **Closing `---`** after frontmatter
- [ ] **No XML-style tags** (no `<purpose>`, `<description>`, etc.)
- [ ] **SKILL.md under ~500 lines** (use references/ for details)
- [ ] **All referenced files exist** in scripts/, references/, or assets/

## References

- [Agent Skills Spec](./references/agent-skills-spec.md) - Complete format specification
- [Agent Skills Blog](./references/agent-skills-intro-blog.md) - Design philosophy and examples
- [Agent Skills Best Practices](./references/agent-skills-best-practices.md) - Authoring best practices

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deloreyj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
