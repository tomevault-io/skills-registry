---
name: devflow-onboarding
description: Interactive 5-minute onboarding for Dev Flow. Gets users productive immediately with personalized setup. Triggers on "get started", "onboard me", "how does this work", "first time setup", "quick start", "new to dev flow", "help me get started". Use when this capability is needed.
metadata:
  author: dorrianguy
---

# Dev Flow Onboarding

Interactive 5-minute setup that gets users from zero to productive. Auto-detects project context, introduces available agents, and captures preferences.

## When to Use

- New user starting with Dev Flow
- User asks "how does this work?"
- User wants to understand available capabilities
- Setting up a new project workspace
- User says "get started" or "onboard me"

## Input Handling

User provides (may be implicit):
- Current working directory (auto-detected)
- Optional: name, role, preferences

**If details missing:** Gather interactively during onboarding flow.

## Instructions

Execute the 5-step onboarding flow. Each step should be conversational and engaging.

### Step 1: Who Are You? (30 seconds)

```markdown
## Welcome to Dev Flow

I'm your AI development team. Let me learn a bit about you:

**Quick questions:**
1. What should I call you?
2. What's your role? (developer, lead, founder, etc.)
3. Your timezone? (for scheduling and context)
4. Preferred communication style?
   - Concise (just the essentials)
   - Detailed (full explanations)
   - Casual (conversational)
   - Technical (precise terminology)
```

Use `AskUserQuestion` tool if available, otherwise ask in conversation.

### Step 2: What's Your Project? (1 minute)

Auto-detect from current directory:

```bash
# Detection commands to run:
- Check for package.json → Node.js/TypeScript project
- Check for requirements.txt/pyproject.toml → Python project
- Check for go.mod → Go project
- Check for Cargo.toml → Rust project
- Check for pom.xml/build.gradle → Java project
- Look at folder structure (src/, app/, etc.)
- Read README.md for project description
```

**Output:**
```markdown
## Project Detected

**Name:** [from package.json name or folder name]
**Tech Stack:** [detected technologies]
**Structure:** [monorepo/single-app/library]
**Key Files:**
- Entry: [main entry points]
- Config: [configuration files]
- Tests: [test locations]

Does this look right? (I can re-scan if needed)
```

### Step 3: Meet Your Team (1 minute)

Present available agents based on project type:

```markdown
## Your Dev Flow Team

Based on your [tech stack] project, here's your team:

### Available Agents

| Agent | Specialty | When They Help |
|-------|-----------|----------------|
| **Main (Siberius)** | Strategy & Planning | Big picture, coordination, research |
| **Codex** | Code Specialist | Writing, debugging, refactoring |
| **Night Shift** | Overnight Work | Long tasks while you sleep |

### Specialized Team [if Capsule-style team exists]

| Agent | Role | Focus Area |
|-------|------|------------|
| Backend | 🔧 Backend Engineer | APIs, databases, services |
| Frontend | 🎨 Frontend Engineer | UI, components, styling |
| Infra | ☁️ Infrastructure | DevOps, deployment |
| QA | 🧪 QA Engineer | Testing, quality |

### Key Skills Available

**Planning:** launch-planner, roadmap-builder, idea-validator
**Development:** design-guide, animation-ux-expert, api-devex-architect
**Quality:** test-engineer, code-reviewer, security-threat-modeler
**Operations:** prodops-optimizer, saas-deployment-ops

[Show 3-5 most relevant skills for their tech stack]
```

### Step 4: Try Your First Command (2 minutes)

Suggest a simple, valuable task based on project:

```markdown
## Let's Try Something

Based on your project, here are some good first commands:

**Option A: Quick Win**
> "Analyze this codebase and suggest improvements"

**Option B: Documentation**
> "Generate a README for this project"

**Option C: Testing**
> "Write tests for [detected main file]"

**Option D: The Power Move**
> "/ship [small improvement you noticed]"

Which sounds good? Or tell me what you'd like to do first.
```

Execute their choice and walk them through the skill invocation process.

### Step 5: Save Your Profile (30 seconds)

```markdown
## Profile Saved

Your Dev Flow profile:

**Name:** [name]
**Role:** [role]
**Timezone:** [timezone]
**Style:** [communication preference]
**Project:** [project name]
**Stack:** [detected tech stack]

**Preferences saved to:** `~/.claude/user-context.json`

### Quick Reference

| You Want To... | Just Say... |
|----------------|-------------|
| Ship a feature | `/ship [description]` |
| Plan something | "let's plan [feature]" |
| Review code | "review this PR" |
| Run tests | "run tests" |
| Get help | "what can you help with?" |

Welcome to Dev Flow! 🚀
```

## Required Output Sections

### 1. Welcome Card
```markdown
# Welcome to Dev Flow

[Personalized greeting based on name/role]

Your AI development team is ready. Let's get you set up in 5 minutes.
```

### 2. Project Profile
```markdown
## Project Profile

| Attribute | Value |
|-----------|-------|
| Name | [project_name] |
| Stack | [technologies] |
| Structure | [type] |
| Test Command | [detected] |
| Build Command | [detected] |
```

### 3. Team Introduction
```markdown
## Your Team

[Agent cards with specialties]
```

### 4. First Task Walkthrough
```markdown
## Your First Task

[Step-by-step execution of their chosen task]
```

### 5. Profile Confirmation
```markdown
## You're All Set!

Profile saved. Here's what you can do next:
- [3 suggested next actions based on project]
```

## Guardrails

- Never skip the project detection step
- Always confirm before saving preferences
- Keep each step under the time target
- Make it conversational, not robotic
- If user seems experienced, offer to skip basics
- Don't overwhelm with all 36 skills - show most relevant

## Trigger Patterns

```javascript
if (/(get started|onboard|how does this work|first time|quick start|new to dev flow|help me get started|setup dev flow|what can you do)/i.test(objective)) {
  // Run devflow-onboarding
}
```

## Integration

**Dependencies:** context-profiler (for saving preferences)
**Triggers:** None (onboarding is a starting point)

## File Operations

**Read:**
- package.json, pyproject.toml, go.mod, etc. (project detection)
- README.md (project description)
- .git/config (repo info)

**Write:**
- `~/.claude/user-context.json` (user preferences)
- `~/.claude/project-context.json` (project profile)

## Version History

- v1.0.0: Initial release with 5-step flow

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dorrianguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
