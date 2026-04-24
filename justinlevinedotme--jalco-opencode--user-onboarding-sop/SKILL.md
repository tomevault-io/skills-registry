---
name: user-onboarding-sop
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Standard operating procedure for generating AGENTS.md files optimized for END-USER assistance.

**CRITICAL**: This is NOT for development assistance - it's for END-USER assistance only.

</overview>

<workflow>

<phase name="gather-context">

## Phase 1: Gather Repository Context

1. Read root `package.json` to identify project name and type
2. Read root `README.md` for basic project info
3. List root directory contents to understand structure

</phase>

<phase name="search-docs">

## Phase 2: Search Official Documentation

**MANDATORY**: MUST use web search to find real documentation URLs.

- Query: "[repository name] documentation" and "[repository name] getting started"
- Look for official docs sites, README links, llms.txt files
- Find getting started guides and API references
- MUST gather real URLs from web search - MUST NOT invent URLs

</phase>

<phase name="analyze-structure">

## Phase 3: Analyze Repository Structure

- List IMPORTANT directories only (ignore node_modules, .git, etc.)
- Identify key config files, documentation, scripts
- Focus on directories/files users interact with for setup/usage
- Maximum 2-3 levels deep for directory exploration

</phase>

<phase name="explore-installation">

## Phase 4: Explore Installation Methods

- Check for install scripts (install, setup, etc.)
- Look for package manager installation docs
- Find prerequisites and environment setup instructions
- Identify configuration files and environment variables

</phase>

<phase name="identify-usage">

## Phase 5: Identify Running/Usage Patterns

- Find CLI commands and entry points
- Look for start/launch scripts
- Check for GUI/desktop app information
- Identify common usage patterns from documentation

</phase>

<phase name="troubleshooting">

## Phase 6: Document Troubleshooting Resources

- Find log file locations
- Identify debug methods and flags
- Look for common issues in docs
- Check for configuration validation methods

</phase>

<phase name="create-output">

## Phase 7: Create AGENTS.md

Write comprehensive user assistance guide using the structure below.
MUST be actionable and concise (LLM reference, not user docs).
Focus on practical information for helping users.

</phase>

</workflow>

<output-format>

## Output Structure

```markdown
## Repository Overview

- Software type and purpose (1 line)
- Main technologies used (tech stack)
- Installation methods available

## Official Documentation Resources

- Primary documentation URLs (from web search)
- Getting started guides
- CLI/reference documentation
- Troubleshooting guides
- Community resources (Discord, issues, etc.)

## Key Directory Structure

List only IMPORTANT directories with brief descriptions:

- `dir/` - purpose (e.g., "Main source code", "Configuration files")
- `file` - purpose (e.g., "Main entry point", "Installation script")

Focus on what users interact with for setup/usage.

## Setup & Installation

- Prerequisites (what users need before installing)
- Installation commands (all available methods)
- Configuration steps (initial setup)
- Environment variables (important ones)

## Running & Usage

- Start/launch commands
- Common usage patterns
- CLI commands and flags
- GUI access methods (if applicable)

## Troubleshooting

- Common issues and solutions
- Log locations
- Debug methods and flags
- Configuration validation

## Key Files for Reference

List files containing important information:

- README locations
- Config file examples
- Documentation files
- Script files
```

</output-format>

<rules>

## Requirements

### MUST

- Use web search to find actual documentation URLs
- Keep content concise and actionable
- Focus on END-USER assistance, not development
- Verify claims with tools before stating them

### SHOULD

- Use parallel tool calls when gathering information

### MUST NOT

- Include internal development workflows
- Include contributor/development instructions
- Invent or guess documentation URLs

</rules>

<constraints>

## Quality Checklist

- [ ] Web search performed for official docs
- [ ] All documentation URLs are real and verified
- [ ] Directory structure focuses on user-facing files
- [ ] Installation commands are accurate
- [ ] Troubleshooting section includes log locations
- [ ] Content is concise (LLM reference, not user docs)
- [ ] No development/contributing instructions included

</constraints>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
