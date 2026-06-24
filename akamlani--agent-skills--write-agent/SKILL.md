---
name: write-agent
description: Update, Synchronize, and Generate documentation for this repository. Used when the user needs to auto update documentation: (1) Update @README.md (2) Update @AGENTS.md which @CLAUDE.md or @GEMINI.md links within Use when this capability is needed.
metadata:
  author: akamlani
---

# Auto Documentation
Auto updates Documentation and Context

## README File
1.  **Directory Structure**: Update the README.md in the 'Directory Structure' section based on the up to date directory structure.  The 'build' directory represents a separate cloned repositories outside of this one.
- Only required to include information relative to the repository directory structure
- Align all right hand comments to the same indent level for readability
- For Directories, only include important directories, it is not required to include all nested directories.
  - Ensure to include the agent specific directories (.claude, .gemini, etc.) and top-level subdirectoriesfor discovery
- For Filenames, only include important filenames, it is not required to include all filenames.
  - Ensure to include all agent specific files (AGENTS.md, CLAUDE.md, GEMINI.md, etc.) for discovery
2. **Quick Links**: Update Quick Links based on the Markdown Headers as appropriate
3. **Docs Folder**: Update files in the @./docs folder after major milestones and major additions to the project
4. **Links**: Ensure that all links in the README.md are up to date and not broken.
- This includes links to files, directories, and external links.

## Startup Context Files
All Context files (@CLAUDE.md, @GEMINI.md, etc.) should update the @AGENTS.md file, instead of their individual files.  Based off the @README.md file at minimal.  These context files should not be overly verbose, while still providing contextual information.

---
> Source: [akamlani/agent-skills](https://github.com/akamlani/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
