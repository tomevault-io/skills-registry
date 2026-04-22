---
name: moai-workflow-templates
description: Enterprise template management with code boilerplates, feedback templates, and project optimization workflows Use when this capability is needed.
metadata:
  author: tjdwls101010
---

### Pattern 2: GitHub Feedback Templates

Concept: Structured templates for consistent GitHub issue creation.

6 Template Types: Bug Report, Feature Request, Improvement, Refactor, Documentation, Question/Discussion

Integration: Auto-triggered by `/moai:9-feedback` command.

Details: See [Feedback Templates](modules/feedback-templates.md) for all template types and usage.

---

### Pattern 3: Template Optimization & Smart Merge

Concept: Intelligently merge template updates while preserving user customizations.

Smart Merge Algorithm:
```python
def smart_merge(backup, template, current):
 """Three-way merge with intelligence."""

 # Extract user customizations from backup
 user_content = extract_user_customizations(backup)

 # Get latest template defaults
 template_defaults = get_current_templates()

 # Merge with priority
 merged = {
 "template_structure": template_defaults, # Always latest
 "user_config": user_content, # Preserved
 "custom_content": user_content # Extracted
 }

 return merged
```

Details: See [Template Optimizer](modules/template-optimizer.md) for complete workflow and examples.

---

### Pattern 4: Backup Discovery & Restoration

Concept: Automatic backup management with intelligent restoration.

Restoration Process:
```python
def restore_from_backup(backup_id: str):
 """Restore project from specific backup."""

 # Load backup metadata
 backup = load_backup(backup_id)

 # Validate backup integrity
 if not validate_backup_integrity(backup):
 raise BackupIntegrityError("Backup corrupted")

 # Extract user customizations
 customizations = extract_customizations(backup)

 # Apply to current project
 apply_customizations(customizations)
```

Details: See [Template Optimizer - Backup Restoration](modules/template-optimizer.md#restoration-process) for complete implementation.

---

### Pattern 5: Template Version Management

Concept: Track template versions and maintain update history.

Version Tracking:
```json
{
 "template_optimization": {
 "last_optimized": "2025-11-24T12:00:00Z",
 "backup_version": "backup-2025-10-15-v0.27.0",
 "template_version": "0.28.2",
 "customizations_preserved": [
 "language",
 "team_settings",
 "domains"
 ]
 }
}
```

Details: See [Template Optimizer - Version Tracking](modules/template-optimizer.md#version-tracking) for complete implementation.

---

## Module Reference

### Core Modules

- [Code Templates](modules/code-templates.md) - Boilerplate library, scaffold patterns, framework templates
- [Feedback Templates](modules/feedback-templates.md) - 6 GitHub issue types, usage examples, best practices
- [Template Optimizer](modules/template-optimizer.md) - Smart merge algorithm, backup restoration, version management

### Module Contents

Code Templates:
- FastAPI REST API template
- React component template
- Docker & CI/CD templates
- Template variables and scaffolding

Feedback Templates:
- Bug Report template
- Feature Request template
- Improvement, Refactor, Documentation, Question templates
- Integration with `/moai:9-feedback`

Template Optimizer:
- 6-phase optimization workflow
- Smart merge algorithm
- Backup discovery and restoration
- Version tracking and history

## Advanced Documentation

For detailed patterns and implementation strategies:

- [Code Templates Guide](modules/code-templates.md) - Complete template library
- [Feedback Templates](modules/feedback-templates.md) - Issue template reference
- [Template Optimizer](modules/template-optimizer.md) - Optimization and merge strategies

## Best Practices

### Core Requirements

- Use templates for consistent project structure
- Preserve user customizations during updates
- Create backups before major template changes
- Follow template structure conventions
- Document custom modifications
- Use smart merge for template updates
- Track template versions in config
- Test templates before production use

### Quality Standards

[HARD] Document all template default modifications before applying changes.
WHY: Template defaults serve as the baseline for all projects and undocumented changes create confusion and inconsistency across teams.
IMPACT: Without documentation, teams cannot understand why defaults deviate from standards, leading to maintenance issues and conflicting implementations.

[HARD] Create backups before executing template optimization workflows.
WHY: Template optimization involves structural changes that may be difficult to reverse without a clean restoration point.
IMPACT: Missing backups can result in permanent loss of user customizations, requiring manual reconstruction of project-specific configurations.

[HARD] Resolve all merge conflicts during template update workflows.
WHY: Unresolved conflicts create broken configurations that prevent proper template functionality.
IMPACT: Ignored conflicts lead to runtime errors, inconsistent behavior, and project instability requiring emergency fixes.

[SOFT] Maintain consistent template pattern usage throughout the project.
WHY: Mixing different template patterns creates cognitive overhead and makes the codebase harder to understand and maintain.
IMPACT: Inconsistent patterns reduce code predictability and increase onboarding time for new team members.

[HARD] Preserve complete customization history across all template updates.
WHY: Customization history provides an audit trail of project-specific decisions and enables rollback to previous states.
IMPACT: Lost history makes it impossible to understand why changes were made, preventing informed decisions about future modifications.

[HARD] Validate template functionality through testing before production deployment.
WHY: Untested templates may contain errors that only surface in production environments, causing system failures.
IMPACT: Production failures from untested templates result in downtime, data issues, and emergency rollbacks affecting users.

[SOFT] Design templates within reasonable complexity limits for maintainability.
WHY: Excessive template complexity makes them difficult to understand, modify, and debug when issues arise.
IMPACT: Overly complex templates slow down development velocity and increase the likelihood of errors during customization.

[HARD] Track template versions using the built-in version management system.
WHY: Version tracking enables understanding of template evolution, compatibility checking, and coordinated updates.
IMPACT: Without version tracking, teams cannot determine which template features are available or coordinate updates across projects safely.

## Works Well With

Agents:
- workflow-project - Project initialization
- core-planner - Template planning
- workflow-spec - SPEC template generation

Skills:
- moai-project-config-manager - Configuration management and validation
- moai-cc-configuration - Claude Code settings integration
- moai-foundation-specs - SPEC template generation
- moai-docs-generation - Documentation template scaffolding
- moai-core-workflow - Template-driven workflows

Commands:
- `/moai:0-project` - Project initialization with templates
- `/moai:9-feedback` - Feedback template selection and issue creation

## Workflow Integration

Project Initialization:
```
1. Select code template (Pattern 1)
 ↓
2. Scaffold project structure
 ↓
3. Apply customizations
 ↓
4. Initialize version tracking (Pattern 5)
```

Feedback Submission:
```
1. /moai:9-feedback execution
 ↓
2. Select issue type (Pattern 2)
 ↓
3. Fill template fields
 ↓
4. Auto-generate GitHub issue
```

Template Update:
```
1. Detect template version change
 ↓
2. Create backup (Pattern 4)
 ↓
3. Run smart merge (Pattern 3)
 ↓
4. Update version history (Pattern 5)
```

## Success Metrics

- Scaffold Time: 2 minutes for new projects (vs 30 minutes manual)
- Template Adoption: 95% of projects use templates
- Customization Preservation: 100% user content retained during updates
- Feedback Completeness: 95% GitHub issues with complete information
- Merge Success Rate: 99% conflicts resolved automatically

## Changelog

- v2.0.0 (2025-11-24): Unified moai-core-code-templates, moai-core-feedback-templates, and moai-project-template-optimizer into single skill with 5 core patterns
- v1.0.0 (2025-11-22): Original individual skills

---

Status: Production Ready (Enterprise)
Modular Architecture: SKILL.md + 3 core modules
Integration: Plan-Run-Sync workflow optimized
Generated with: MoAI-ADK Skill Factory

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjdwls101010) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
