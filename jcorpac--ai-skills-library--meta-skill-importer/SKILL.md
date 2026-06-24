---
name: meta-skill-importer
description: Automated workflows for importing and adapting external skills from third-party repositories (e.g., Google Gemini Skills) into your local library. Use when this capability is needed.
metadata:
  author: jcorpac
---

# External Skill Importer

## Overview
The **Skill Importer** streamlines the process of integrating external expertise into your AI Skills Library. It handles the cloning, filtering, and structural adaptation required to make third-party skills compatible with this repository's standards.

## Capabilities

### 1. Automated Import
Use the `scripts/import_skill.ps1` utility to surgically extract a specific skill from a remote Git repository.

**Usage:**
```powershell
./scripts/import_skill.ps1 -RepoUrl "<git-repo-url>" -SkillPath "<relative-path-to-skill>" -DestCategory "<local-category>"
```

**Example:**
```powershell
./scripts/import_skill.ps1 -RepoUrl "https://github.com/google-gemini/gemini-skills" -SkillPath "skills/gemini-api-dev" -DestCategory "api"
```

### 2. Adaptation Standards
When importing skills, this tool ensures:
- **Metadata Preservation**: Retains original `SKILL.md` or `README.md`.
- **Clean History**: accessible as a fresh addition to your library.
- **Attribution**: Reminds you to add source attribution to the main registry.

## Workflow

1.  **Identify**: Find a valuable skill in an external repo (e.g., `google-gemini/gemini-skills`).
2.  **Import**: Run the import script with the specifics of the target skill.
3.  **Register**: Add the new entry to your root `README.md` with an attribution note.
4.  **Install**: Run `root/install_skills.ps1` to link it to your CLI agent.

## Source Attribution
Always respect the license of the source repository. When adding to `README.md`, use the standard attribution format:
`*Imported from [repository-name](url)*`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
