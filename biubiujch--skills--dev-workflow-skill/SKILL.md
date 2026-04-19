---
name: dev-workflow-skill
description: Development workflow skill for complete project lifecycle - requirements, task planning, development tracking, testing, committing, and deployment. Use when user mentions requirements, tasks, development progress, testing, git commits, or deployment. Use when this capability is needed.
metadata:
  author: biubiujch
---

# Dev Workflow Skill - Development Workflow Management

A comprehensive skill for managing the complete development lifecycle: from requirements to deployment. Integrates project management with CI/CD automation.

## When to Use

- When user needs to organize and analyze project requirements
- When user needs to create development plans and tasks
- When user needs to track development progress
- When user needs to run tests and quality checks
- When user needs to commit code and create pull requests
- When user needs to deploy to different environments
- When requirements change and need impact evaluation

## Complete Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    Development Workflow                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Requirements    2. Planning      3. Development             │
│  ┌───────────┐     ┌───────────┐    ┌───────────┐              │
│  │ Collect   │ ──→ │ Break down│ ──→│ Implement │              │
│  │ Analyze   │     │ Prioritize│    │ Track     │              │
│  │ Archive   │     │ Estimate  │    │ Progress  │              │
│  └───────────┘     └───────────┘    └───────────┘              │
│                                           │                     │
│                                           ▼                     │
│  6. Deploy         5. Commit        4. Testing                 │
│  ┌───────────┐     ┌───────────┐    ┌───────────┐              │
│  │ Deploy to │ ←── │ Git commit│ ←──│ Run tests │              │
│  │ Env       │     │ Create PR │    │ Lint check│              │
│  │ Verify    │     │ Review    │    │ Validate  │              │
│  └───────────┘     └───────────┘    └───────────┘              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## First-Time Setup

**Important**: Before first use, grant script execution permissions:

```bash
chmod +x <skill-path>/scripts/*.sh
```

Where `<skill-path>` is the skill installation path, for example:
- Project-level: `.cursor/skills/dev_workflow_skill`
- User-level: `~/.cursor/skills/dev_workflow_skill`

## File Storage Location

Data files are stored in the project root directory:

```
<project-root>/
├── .pm/                         # Project management data
│   ├── requirements.md          # Requirements document
│   ├── implements.json          # Tasks and progress
│   ├── references.json          # Reference materials index
│   ├── changelogs/              # Change request records
│   └── refs/                    # Reference materials
│       ├── docs/
│       ├── images/
│       └── links.md
└── .cicd/                       # CI/CD data
    ├── config.json              # CI/CD configuration
    ├── history.json             # Deployment history
    └── environments.json        # Environment configurations
```

## Modules

This skill consists of the following modules. See references for detailed documentation:

| Module | Description | Reference |
|--------|-------------|-----------|
| Requirements | Collect, analyze, archive requirements | [requirements.md](./references/requirements.md) |
| Tasks | Task breakdown, planning, tracking | [tasks.md](./references/tasks.md) |
| Testing | Run tests, quality checks | [testing.md](./references/testing.md) |
| Git | Commit, push, create PR | [git.md](./references/git.md) |
| Deploy | Deploy to environments | [deploy.md](./references/deploy.md) |

## Task Status Flow

Extended status to support full development lifecycle:

```
pending → in_progress → testing → committing → deploying → completed
       ↘ cancelled
```

| Status | Description |
|--------|-------------|
| `pending` | Not started |
| `in_progress` | Development in progress |
| `testing` | Running tests/quality checks |
| `committing` | Creating commit/PR |
| `deploying` | Deploying to environment |
| `completed` | Successfully deployed or done |
| `cancelled` | Cancelled |

## Quick Reference

### Initialize Project

```bash
bash scripts/init_project.sh <project-root>
```

### Requirements & References

```bash
# Add reference material
bash scripts/add_reference.sh <project-root> '<ref-json>'

# View references
bash scripts/show_references.sh <project-root> [type]
```

### Task Management

```bash
# Add task
bash scripts/add_task.sh <project-root> '<task-json>'

# Update task status
bash scripts/update_task.sh <project-root> <task-id> <status>

# Modify task properties
bash scripts/modify_task.sh <project-root> <task-id> '<modifications-json>'

# View progress
bash scripts/show_status.sh <project-root>
```

### Change Management

```bash
# Create change request
bash scripts/create_change.sh <project-root> '<change-json>'

# Apply change
bash scripts/apply_change.sh <project-root> <change-id>

# View change history
bash scripts/show_changes.sh <project-root>
```

### CI/CD Operations

```bash
# Run tests for task
bash scripts/run_tests.sh <project-root> [task-id]

# Commit task changes
bash scripts/commit_task.sh <project-root> <task-id> [message]

# Deploy task
bash scripts/deploy_task.sh <project-root> <task-id> <environment>

# View deployment history
bash scripts/show_deployments.sh <project-root> [environment]
```

## Commands

### `/dev_workflow_skill` - Start Development Workflow

After starting, follow this process:

1. **First use**: Run `init_project.sh` to initialize, ask user for requirements
2. **Existing project**: Show current progress and available actions

### Available Operations

| Operation | Description | Module |
|-----------|-------------|--------|
| `init` | Initialize project | Core |
| `ref` | Add reference material | Requirements |
| `refs` | View reference materials | Requirements |
| `add` | Add new task | Tasks |
| `update` | Update task status | Tasks |
| `modify` | Modify task properties | Tasks |
| `status` | View current progress | Tasks |
| `change` | Create change request | Tasks |
| `apply` | Apply change | Tasks |
| `history` | View change history | Tasks |
| `test` | Run tests | Testing |
| `commit` | Commit task changes | Git |
| `deploy` | Deploy to environment | Deploy |
| `deployments` | View deployment history | Deploy |

## Automated Workflow

When user requests full automation for a task:

### Step 1: Development Complete
User indicates task development is complete.

### Step 2: Auto Test
```bash
bash scripts/run_tests.sh <project-root> <task-id>
```
- Run configured test commands
- Update task status to `testing`
- If tests pass, proceed to commit

### Step 3: Auto Commit
```bash
bash scripts/commit_task.sh <project-root> <task-id>
```
- Stage relevant changes
- Generate commit message based on task
- Update task status to `committing`
- If commit succeeds, proceed to deploy (if configured)

### Step 4: Auto Deploy (if applicable)
```bash
bash scripts/deploy_task.sh <project-root> <task-id> <environment>
```
- Deploy to configured environment
- Update task status to `deploying`
- Record deployment in history
- Update task status to `completed`

## CI/CD Configuration

The `.cicd/config.json` file defines test, git, and deployment settings. The skill auto-detects project type during initialization.

### Supported Project Types

| Project Type | Detection | Test Command | Lint Command |
|--------------|-----------|--------------|--------------|
| Node.js | `package.json` | `npm test` | `npm run lint` |
| Python | `pyproject.toml`, `pytest.ini` | `pytest` | `flake8` / `ruff` |
| Rust | `Cargo.toml` | `cargo test` | `cargo clippy` |
| Go | `go.mod` | `go test ./...` | `golint ./...` |
| Java/Kotlin | `pom.xml`, `build.gradle` | `mvn test` / `gradle test` | - |
| C/C++ | `CMakeLists.txt`, `Makefile` | `make test` | - |
| Ruby | `Gemfile` | `bundle exec rspec` | `rubocop` |
| PHP | `composer.json` | `vendor/bin/phpunit` | `vendor/bin/phpcs` |

### Configuration Example

```json
{
  "test": {
    "command": "<auto-detected or custom>",
    "lint": "<auto-detected or custom>",
    "type_check": "<optional>",
    "coverage": false,
    "coverage_threshold": 80
  },
  "git": {
    "branch_prefix": "feature/",
    "commit_template": "[{task_id}] {message}",
    "auto_push": true,
    "create_pr": true,
    "default_branch": "main"
  },
  "environments": {
    "development": {
      "auto_deploy": false,
      "command": ""
    },
    "staging": {
      "auto_deploy": false,
      "command": "<your-deploy-command>"
    },
    "production": {
      "auto_deploy": false,
      "command": "<your-deploy-command>",
      "require_approval": true
    }
  }
}
```

## Skill File Structure

```
dev_workflow_skill/
├── SKILL.md                           # Main skill file
├── scripts/
│   ├── init_project.sh                # Initialize project
│   ├── add_reference.sh               # Add reference material
│   ├── show_references.sh             # View reference materials
│   ├── add_task.sh                    # Add task
│   ├── update_task.sh                 # Update task status
│   ├── modify_task.sh                 # Modify task properties
│   ├── show_status.sh                 # Show progress
│   ├── create_change.sh               # Create change request
│   ├── apply_change.sh                # Apply change
│   ├── show_changes.sh                # View change history
│   ├── run_tests.sh                   # Run tests
│   ├── commit_task.sh                 # Commit task
│   ├── deploy_task.sh                 # Deploy task
│   └── show_deployments.sh            # View deployments
└── references/
    ├── requirements.md                # Requirements module guide
    ├── tasks.md                       # Tasks module guide
    ├── testing.md                     # Testing module guide
    ├── git.md                         # Git module guide
    ├── deploy.md                      # Deploy module guide
    ├── requirements-template.md       # Requirements document template
    ├── implements-template.md         # Tasks file template
    ├── change-template.md             # Change request template
    └── cicd-config-template.md        # CI/CD config template
```

## Important Notes

- First use requires granting script execution permissions: `chmod +x scripts/*.sh`
- Use `bash scripts/xxx.sh` format for script calls to avoid permission issues
- Data files are in project root's `.pm/` and `.cicd/` directories
- **Do NOT directly edit** JSON files, must use scripts
- Requirements document `requirements.md` can be directly edited
- Always run tests before committing
- Review deployment history before deploying to production
- Use ask tool to clarify requirements or confirm destructive actions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/biubiujch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
