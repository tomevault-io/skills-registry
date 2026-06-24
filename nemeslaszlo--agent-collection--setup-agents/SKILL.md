---
name: setup-agents
description: Set up the Agent Collection in a new project. Copies relevant agents and skills, prunes unnecessary ones, and generates the CLAUDE.md agent section. Use when the user says setup agents, add agents to this project, initialize agents, or is starting a new project. Use when this capability is needed.
metadata:
  author: nemeslaszlo
---

## Your Task

Set up the Agent Collection for this project. The user wants to configure agents and skills from their collection.

**Project type hint:** $ARGUMENTS

### Step 1: Analyze the Project

Understand the current project:
- What's the tech stack? (languages, frameworks, databases)
- What kind of project is it? (web app, API, mobile, monorepo)
- What already exists in `.claude/`?
- Is there an existing `CLAUDE.md`?

### Step 2: Recommend Agents

Based on the project type, recommend which agents to include. Use these guidelines:

| Project Type | Recommended Agents |
|---|---|
| **.NET + Angular Full-Stack** | Feature Planner, Backend Architect, Frontend Developer, Clean Code Architect, DevOps Automator, Security Scanner |
| **Backend API** | Feature Planner, Backend Architect, Clean Code Architect, Security Scanner, DevOps Automator |
| **Frontend SPA** | Feature Planner, Frontend Developer, UI Designer, Clean Code Architect |
| **Mobile App** | Feature Planner, Mobile App Builder, UI Designer, Backend Architect |
| **Startup/MVP** | Feature Planner, Rapid Prototyper, Backend Architect, UI Designer |
| **Enterprise** | Feature Planner, DeepDive, DeepCode, Backend Architect, Security Scanner, DevOps Automator |
| **AI/ML Project** | Feature Planner, AI Engineer, Backend Architect, Clean Code Architect |

**Feature Planner** is included in every set. Consider adding **DeepDive + DeepCode** to any project where bug investigation is needed.

Present the recommendation and ask the user to confirm or adjust before copying.

### Step 3: Copy Files

After confirmation:

1. Create `.claude/agents/` directory structure
2. Copy selected agent files from the Agent Collection
3. Copy relevant skills to `.claude/skills/`
4. Always copy: `plan-feature`, `implement`, `cross-review`, `fix-issues`, `investigate`, `review`, `security-scan`, `explore`

### Step 4: Generate CLAUDE.md Section

Add or update the `CLAUDE.md` file with:
- Active Agents list (with correct relative paths)
- Agent Collaboration Patterns table (only patterns relevant to included agents)
- Recommended Feature Workflow section
- Available Skills section

### Step 5: Project-Specific Customization

Ask the user if they want to customize any agents for the project:
- Adjust tech stack references (e.g., Clean Code Architect's .NET section)
- Add project-specific patterns or conventions
- Modify agent relationships

### Output

Tell the user:
1. What was copied and where
2. How to use the key workflows (`/plan-feature` → `/implement` → `/fix-issues`, `/investigate`, `/review`)
3. Any agents they might want to customize further

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nemeslaszlo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
