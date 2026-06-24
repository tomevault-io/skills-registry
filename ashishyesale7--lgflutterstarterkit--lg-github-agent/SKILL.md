---
name: lg-github-agent
description: Manages GitHub repository operations for LG Flutter projects — branching, PRs, issues, releases, and OSS best practices.
metadata:
  author: ashishyesale7
---

# Liquid Galaxy GitHub Agent

## Overview

This skill manages all GitHub-related operations for Liquid Galaxy Flutter projects. It handles creating new repositories for generated apps, ensures proper Git workflow, branch management, pull request practices, and OSS release standards expected by the Liquid Galaxy community.

**Announce at start:** "I'm using the lg-github-agent skill to handle [Git/GitHub Task]."

## 🆕 New App Repository Creation

### When This Triggers
During `lg-init` Phase 0, after the new app directory is created as a **sibling** to `LGFlutterStarterKit`.

### ⚠️ MANDATORY: App Naming Convention

The repository name **MUST** follow the `LG-<TaskName>` pattern:
- ✅ `LG-Earthquake-Viz`, `LG-Space-Explorer`, `LG-Weather-Atlas`
- ❌ `earthquake_app`, `my_flutter_project`, `cool_viz`

**Validate before creating**: If the name does not start with `LG-`, **STOP** and ask the student to rename.

### Step 1: Create GitHub Repository
```bash
# Method 1: GitHub CLI (RECOMMENDED — if gh is installed)
gh auth status  # Verify authenticated
gh repo create "<username>/LG-<TaskName>" --public --description "Liquid Galaxy Flutter controller: <description>" --source . --remote origin --push

# Method 2: Manual (if gh not installed)
# 1. Go to https://github.com/new
# 2. Create repo with name: LG-<TaskName>
# 3. Set to Public
# 4. Do NOT initialize with README (we already have one)
# 5. Copy the repo URL
git remote add origin https://github.com/<username>/LG-<TaskName>.git
git push -u origin main
```

### Step 2: Verify Remote
```bash
git remote -v
# Should show origin pointing to the new repo
```

### Step 3: Configure Repository
```bash
# Set up branch protection (via gh CLI)
gh repo edit --enable-auto-merge --delete-branch-on-merge

# Push GitHub Actions workflows (already copied from starter kit)
git add .github/ && git commit -m "chore(ci): add Flutter CI/CD workflows" && git push
```

### Step 4: Set Up Templates
Copy PR and Issue templates from starter kit reference (see below).

### Important: Three Repos, Three Purposes (Contest Workflow)
| Repository | Purpose | Push To |
|------------|---------|--------|
| `LGFlutterStarterKit` | Template/reference (Antigravity agent system) | Original repo (read-only for students) |
| `LG-<TaskName>` | Student's actual LG app (Task 2 deliverable) | Student's own GitHub account |
| Agent System Repo | Gemini agent config + prompts (Task 1 deliverable) | Student's own GitHub account |

> **Contest**: Gemini Summer of Code 2026 — see `.agent/rules/lg-architecture.md` for full requirements.

## 🌿 Branching Strategy

### Branch Naming
| Branch | Purpose |
|---|---|
| `main` | Production-ready, tagged releases |
| `develop` | Integration branch for features |
| `feature/<name>` | New features (e.g., `feature/earthquake-viz`) |
| `fix/<name>` | Bug fixes (e.g., `fix/ssh-timeout`) |
| `chore/<name>` | Non-functional (docs, CI, refactor) |

### Workflow
```
1. Create feature branch from develop
2. Implement + commit with conventional commits
3. Push and create PR to develop
4. Code review (lg-code-reviewer)
5. Merge to develop
6. When ready: merge develop → main, create release tag
```

## 📝 Conventional Commits

All commits MUST follow conventional commit format:

```
<type>(<scope>): <description>

Types:
  feat:     New feature (earthquake KML, fly-to, orbit)
  fix:      Bug fix (SSH timeout, coordinate swap)
  docs:     Documentation only (README, design docs)
  style:    Formatting (dart format, no logic change)
  refactor: Code restructure (no behavior change)
  test:     Adding/fixing tests
  chore:    Build/tooling/CI changes
  init:     Project scaffolding

Examples:
  feat(kml): add earthquake placemark generation
  fix(ssh): handle connection timeout gracefully
  docs: update architecture-map with KML pipeline
  test(services): add unit tests for KMLService
  chore(ci): add GitHub Actions Flutter workflow
  init: project scaffolding from starter kit
```

## 🔀 Pull Request Template

Create `.github/PULL_REQUEST_TEMPLATE.md`:

```markdown
## Description
<!-- What does this PR do? Which feature/fix? -->

## LG Functionality
<!-- How does this affect the LG rig? -->
- [ ] New KML visualization
- [ ] SSH command changes
- [ ] Navigation/camera control
- [ ] Rig management (reboot, relaunch)
- [ ] UI-only (no rig interaction)

## Testing
- [ ] `flutter analyze` passes
- [ ] `flutter test` passes
- [ ] Tested on Android device/emulator
- [ ] Tested with LG rig (or mock SSH)
- [ ] Google Earth Pro KML preview verified

## Screenshots/Demo
<!-- Add phone UI screenshots or GE screenshots if applicable -->
```

## 🐛 Issue Templates

Create `.github/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug Report
about: Report a bug in the LG controller app
labels: bug
---

## Bug Description
<!-- Clear description of the bug -->

## Steps to Reproduce
1.
2.
3.

## Expected Behavior
<!-- What should happen -->

## Actual Behavior
<!-- What actually happens -->

## Environment
- Flutter version:
- Device:
- LG Rig screens:
- LG Master IP:

## Logs
<!-- Paste relevant logs/errors -->
```

## 🏷 Release Process

### Semantic Versioning
- `v1.0.0` — Initial release
- `v1.1.0` — New feature (e.g., orbit tours)
- `v1.0.1` — Bug fix (e.g., SSH timeout fix)

### Release Checklist
```bash
# 1. Ensure all tests pass
cd flutter_client && flutter test

# 2. Build release APK
flutter build apk --release

# 3. Tag the release
git tag -a v1.0.0 -m "Release v1.0.0: Initial earthquake visualizer"
git push origin v1.0.0

# 4. Create GitHub Release with APK artifact
# Upload build/app/outputs/flutter-apk/app-release.apk
```

## 📄 Repository Structure for OSS

```
.github/
  PULL_REQUEST_TEMPLATE.md
  ISSUE_TEMPLATE/
    bug_report.md
    feature_request.md
  workflows/
    flutter-ci.yml
    build-apk.yml
.gitignore
LICENSE               # MIT recommended
README.md
DEVELOPMENT_LOG.md
CONTRIBUTING.md       # How to contribute
```

## Reference Links

- **Conventional Commits**: https://www.conventionalcommits.org/
- **GitHub CLI docs**: https://cli.github.com/manual/
- **Open Source Guides**: https://opensource.guide/
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## ⛔ Student Interaction Checkpoints

### After Repo Creation — Branching Strategy

⛔ **STOP and WAIT** — After creating the repository, ask:
> *"We just created the repo. Now, what branching strategy should we use? Can you explain the difference between `main`, `develop`, and `feature/` branches? When would you create a PR?"*

Wait for the student's answer. Evaluate:
- ✅ **Correct**: They understand Git flow (feature → develop → main).
- ⚠️ **Partially correct**: Walk through the branch table above.
- ❌ **Wrong**: Link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md) for Git/GitHub fundamentals.

### Conventional Commits — Practice

⛔ **STOP and WAIT** — Present a scenario:
> *"You just added a new KML orbit feature. Write the commit message using conventional commit format. What type prefix would you use?"*

Multiple choice:
> A) `fix(kml): add orbit tour generation`
> B) `feat(kml): add orbit tour generation`
> C) `chore(kml): add orbit tour generation`

### PR Workflow — Trace the Process

⛔ **STOP and WAIT** — Ask:
> *"Trace the lifecycle of a feature from branch creation to merge. What checks must pass before merging?"*

## Handoff
After GitHub setup → return to the original workflow.

## 🔗 Skill Chain

After the repository is configured and the student understands the Git workflow, **automatically offer to return to the calling skill**:

> *"GitHub repo is set up with CI, templates, and branch protection! Let's continue with the project initialization. Ready to go back? 🚀"*

If student says "ready" → activate `.agent/skills/lg-init/SKILL.md` (return to calling workflow).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
