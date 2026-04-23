---
name: project-setup
description: Initialize new ADLC project with standard directory structure, documentation templates, and git branch Use when this capability is needed.
metadata:
  author: dylpickledev
---

# Project Setup Skill

Automates the initialization of new ADLC projects following standard conventions.

## Purpose

Reduce 10-15 minutes of manual setup to 30 seconds by:
- Creating standard project directory structure
- Generating README, spec.md, context.md from templates
- Creating feature branch with proper naming
- Initializing task tracking structure

## Usage

This skill is invoked when starting a new project via `/start` command or when explicitly requested.

**Trigger phrases**:
- "Set up new project for [name]"
- "Initialize project structure"
- "Create new ADLC project"

## Workflow Steps

### 1. Gather Project Information

**Required inputs**:
- `project_name`: Project identifier (lowercase-hyphenated)
- `project_title`: Human-readable title
- `project_type`: Type (feature, fix, research, refactor)
- `description`: Brief 1-2 sentence description
- `issue_number`: (optional) GitHub issue number if from `/start [issue#]`

**Ask user if not provided**:
```
I need some information to set up your project:
- Project name (lowercase-hyphenated):
- Project title:
- Type (feature/fix/research/refactor):
- Brief description:
```

### 2. Create Directory Structure

**Execute**:
```bash
mkdir -p projects/active/{project_name}
mkdir -p projects/active/{project_name}/tasks
```

**Structure created**:
```
projects/active/{project_name}/
├── README.md           # Navigation hub
├── spec.md            # Project specification
├── context.md         # Working context
└── tasks/             # Agent coordination
    └── current-task.md
```

### 3. Generate README.md

**Use template**: `.claude/skills/project-setup/templates/README.template.md`

**Populate with**:
- `{project_title}`
- `{description}`
- `{issue_number}` (if exists, link to GitHub issue)
- Current date
- Project structure overview
- Quick links section (placeholder)

**Create file**: `projects/active/{project_name}/README.md`

### 4. Generate spec.md

**Use template**: `.claude/skills/project-setup/templates/spec.template.md`

**Populate with**:
- `{project_title}`
- `{description}`
- `{project_type}`
- Standard sections: Goals, Requirements, Implementation Plan, Success Criteria

**Create file**: `projects/active/{project_name}/spec.md`

### 5. Generate context.md

**Use template**: `.claude/skills/project-setup/templates/context.template.md`

**Populate with**:
- `{project_name}`
- Current date
- Git branch (will be created in next step)
- Status: "Planning"
- Empty sections for: Current Focus, Blockers, Decisions

**Create file**: `projects/active/{project_name}/context.md`

### 6. Generate current-task.md

**Use template**: `.claude/skills/project-setup/templates/current-task.template.md`

**Populate with**:
- `{project_name}`
- Current date
- Empty task assignments

**Create file**: `projects/active/{project_name}/tasks/current-task.md`

### 7. Create Git Branch

**Branch naming convention**:
- Feature: `feature/{project_name}`
- Fix: `fix/{project_name}`
- Research: `research/{project_name}`
- Refactor: `refactor/{project_name}`

**Execute**:
```bash
git checkout -b {branch_type}/{project_name}
```

**Verify**:
```bash
git status
```

### 8. Update context.md with Branch

**Edit** `projects/active/{project_name}/context.md`:
- Add branch name to Git Branch field
- Confirm status

### 9. Output Summary

**Display to user**:
```
✅ Project initialized: {project_title}

📁 Location: projects/active/{project_name}/
🌿 Branch: {branch_type}/{project_name}

📝 Next steps:
1. Review and update spec.md with detailed requirements
2. Update context.md with current focus
3. Begin implementation following ADLC workflow

Files created:
- README.md (navigation hub)
- spec.md (requirements and goals)
- context.md (working state tracking)
- tasks/current-task.md (agent coordination)

Use /switch to navigate between projects.
```

## Error Handling

### Project Already Exists
**Check**: `projects/active/{project_name}` directory exists
**Action**: Ask user to choose different name or confirm overwrite

### Git Branch Already Exists
**Check**: Branch `{branch_type}/{project_name}` exists
**Action**: Suggest alternative name or confirm checkout existing branch

### Template Missing
**Check**: Required templates exist
**Action**: Use inline fallback templates (minimal versions)

## Quality Standards

**Every project setup must**:
- ✅ Create all 4 core files (README, spec, context, current-task)
- ✅ Use consistent naming conventions
- ✅ Create valid git branch
- ✅ Populate templates with provided information
- ✅ Verify all files created successfully

## Integration with ADLC Workflow

### Called by `/start` command
```
/start [issue#] → Fetches issue details → Invokes project-setup skill → Project ready
```

### Called manually
```
User: "Set up project for dashboard-optimization"
→ Invokes project-setup skill
→ Asks for missing details
→ Creates structure
```

## Template Locations

- **README.template.md**: Navigation hub template
- **spec.template.md**: Project specification template
- **context.template.md**: Working context template
- **current-task.template.md**: Task tracking template

Templates use `{variable}` syntax for substitution.

## Success Metrics

**Time savings**: 10-15 minutes manual → 30 seconds automated
**Consistency**: 100% projects follow standard structure
**Error reduction**: Eliminate missing files, inconsistent naming

---

**Version**: 1.0.0
**Last Updated**: 2025-10-21
**Maintainer**: ADLC Platform Team

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dylpickledev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
