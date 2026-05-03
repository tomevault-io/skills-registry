---
name: workflow-guidance
description: Expert knowledge in guiding users through agentic technical editing workflows. Provides step-by-step instructions for framework setup, context loading, and systematic editing processes. Use when users need guidance on framework usage, setup procedures, or workflow troubleshooting. Use when this capability is needed.
metadata:
  author: miguel-conde
---

# Agentic Technical Editing Workflow Guidance

## Core Framework Understanding

### Essential Components
- **Backup System**: Preserves original files for safety and reference
- **Book Intake**: Establishes global editorial context once per project
- **Context Loading**: Provides session-specific awareness for agents
- **Systematic Editing**: Professional editing with global book coherence

### Framework Philosophy
This approach treats technical book editing as professional publishing, not casual content improvement. Systematic setup ensures consistency, quality, and safety that isolated AI editing cannot achieve.

## User Guidance Patterns

### First-Time Setup Workflow

**Step 1: Create Backup**
Guide users to execute:
```bash
/backup-restore
```
Explain: Creates timestamped backup in `_originals/` directory. Essential safety net.

**Step 2: Complete Book Intake**  
Guide users to execute:
```bash
Use skill: book-intake
```
Explain: Establishes global context through four templates copied from `.github/skills/book-intake/templates/`:
- `book-intake.md`: Audience, technical level, constraints
- `toc.md`: Complete chapter structure and progression
- `chapter-map.md`: Each chapter's purpose and boundaries  
- `glossary.md`: Canonical terminology standards

**Step 3: Understand Editing Sessions**
Explain the repeatable pattern for all future editing work.

### Regular Editing Session Workflow

**Context Loading (Every Session)**
Guide users to execute:
```bash
/load-context
```
Explain: Fills template with book intake content. Required because agents don't retain memory between sessions.

**Professional Editing**
Guide users to execute:
```bash
@technical-editor Edit chapter X following the loaded context
```
Explain: Agent now has global book awareness and will maintain consistency.

## Common User Scenarios

### Scenario: "I'm new to this framework"
**Response Pattern:**
1. Acknowledge the learning curve but emphasize value
2. Guide through first-time setup (backup → intake → context → edit)
3. Explain why each step matters for quality results
4. Provide specific commands to execute

### Scenario: "This seems complex"  
**Response Pattern:**
1. Validate complexity but explain it's front-loaded
2. Compare to professional publishing standards
3. Emphasize systematic approach prevents future problems
4. Show how workflow becomes routine after setup

### Scenario: "My edits aren't consistent"
**Response Pattern:**
1. Diagnose: Is book intake complete? Is context loaded?
2. Guide to verify intake files are populated
3. Ensure context loading template is filled correctly
4. Restart with proper context if needed

### Scenario: "Something went wrong"
**Response Pattern:**
1. Immediately guide to backup system for safety
2. Use /backup-restore prompt for comparison/recovery
3. Diagnose what step in workflow was missed
4. Restart from last known good state

## Troubleshooting Expertise

### "Agent doesn't understand my book structure"
**Diagnosis**: Incomplete book intake or missing context loading
**Solution**: Complete intake process, ensure all templates filled, reload context

### "Edits seem generic, not domain-specific"  
**Diagnosis**: Domain skills not activated or context lacks technical scope
**Solution**: Verify EM/MTA skill available, ensure technical details in intake

### "Framework feels overwhelming"
**Diagnosis**: User trying to understand everything at once
**Solution**: Focus on immediate next step, explain value incrementally

## Communication Strategies

### Emphasize Professional Standards
Position framework as meeting publication-quality standards, not casual AI assistance.

### Provide Concrete Commands
Always give exact commands to execute rather than abstract descriptions.

### Explain the "Why"
Help users understand rationale behind systematic approach for buy-in.

### Acknowledge Learning Curve
Validate that setup takes effort but pays dividends in quality results.

### Focus on Safety
Emphasize backup system provides confidence to experiment and learn.

## Progressive Guidance Approach

### Level 1: Essential Workflow
Guide users through basic backup → intake → context → edit cycle.

### Level 2: Advanced Patterns  
Introduce checkpoint backups, selective restoration, terminology updates.

### Level 3: Framework Mastery
Help users customize for other domains, optimize workflows, troubleshoot independently.

## Success Indicators

Users successfully using framework when they:
- Automatically create backups before editing
- Complete intake thoroughly rather than rushing to edit
- Load context consistently each session
- See improved consistency across chapters
- Feel confident experimenting knowing backup system protects them

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miguel-conde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
