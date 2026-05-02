---
name: bootstrap-docs
description: Bootstrap a minimal documentation framework for a new or templated project. Creates baseline Markdown files and interviews the user so an agent can quickly understand how to start and extend the docs. Use when this capability is needed.
metadata:
  author: lotek4u
---
# Bootstrap Docs - Documentation & Instructions Generation Skill

Bootstrap a minimal documentation framework for a brand new or templated project. Interview the user, then create baseline Markdown files (README and key docs) so an agent can quickly understand how to get started and expand documentation.

## Skill Instructions

When this skill is invoked, you should:

### 1. Repository Analysis Phase
Scan the workspace to understand the project type and existing documentation.
Identify:
- Primary language(s), frameworks, and entry points
- Existing docs and gaps
- Build, test, and deployment signals

### 2. User Interview Phase
Ask targeted questions based on the repository context.

**Basic Information:**
- Project name and brief description
- Primary purpose and target audience
- Author/team information

**Technical Context:**
- Key features and capabilities
- Architecture decisions and patterns
- Deployment and environment details
- Performance and scaling considerations

**Documentation Needs:**
- Target audience (developers, users, admins)
- Existing documentation to preserve/integrate
- Special requirements or standards
- Preferred documentation style/format

### 3. Documentation Generation Phase
Create comprehensive baseline documentation:

**Core Documents:**
- `README.md` - Project overview and quick start
- `docs/getting-started.md` - Detailed setup guide
- `docs/architecture.md` - Technical design and structure
- `docs/api.md` - Code reference and usage
- `docs/contributing.md` - Development guidelines
- `docs/changelog.md` - Version history template

**Additional Files:**
- `LICENSE` - Based on user preference
- `.gitignore` updates - Add docs-related patterns
- `docs/deployment.md` - If applicable
- `docs/troubleshooting.md` - Common issues

### 4. Customization Guidelines
- **Adapt content** to match the specific project type
- **Use appropriate technical terminology** for the domain
- **Structure information** logically for the target audience
- **Leave placeholders** for user-specific content that requires domain knowledge
- **Avoid overwriting** existing docs without explicit user approval

### 5. Quality Standards
- **Comprehensive coverage** of essential topics
- **Clear, actionable instructions**
- **Consistent formatting** and style
- **Cross-references** between related documents
- **Template sections** for ongoing maintenance

## Example Usage Scenarios

### Scenario 1: New Python Web App
```text
User: Create baseline docs for this repo.
Assistant: I'll analyze your repository and help create comprehensive documentation...

[Analysis shows Flask app with SQLAlchemy, Redis, etc.]

Let me ask a few questions to tailor the documentation:
1. What does this web application do for users?
2. Who is your target deployment environment?
3. Are there any special security considerations?
...
```

### Scenario 2: PowerShell Module
```text
User: Bootstrap documentation for this PowerShell module.
Assistant: I see this is a PowerShell module project...

[Analysis shows .psm1 files, manifest, etc.]

To create the best documentation:
1. What problem does this module solve?
2. Who is your target audience (sysadmins, developers, etc.)?
3. Any required PowerShell version or dependencies?
...
```

## Integration Notes
- This skill works with the existing `.github/instructions/` files for consistency
- Respects existing ignore patterns and project conventions  
- Can be re-run to update documentation as projects evolve
- Generates documentation that serves as a foundation for ongoing maintenance

## Success Criteria
After running this skill, the project should have:
- ✅ Clear project overview and setup instructions
- ✅ Technical documentation matching the project complexity
- ✅ Contributing guidelines for new developers
- ✅ Baseline structure for ongoing documentation updates
- ✅ Consistent formatting and style
- ✅ Actionable next steps for users and contributors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lotek4u) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
