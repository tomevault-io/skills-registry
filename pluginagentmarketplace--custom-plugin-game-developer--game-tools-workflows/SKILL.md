---
name: game-tools-workflows
description: | Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Game Development Tools & Workflows

## Development Tool Stack

```
┌─────────────────────────────────────────────────────────────┐
│                    GAME DEV TOOL STACK                       │
├─────────────────────────────────────────────────────────────┤
│  ENGINE: Unity / Unreal / Godot                             │
│                                                              │
│  IDE: Visual Studio / Rider / VS Code                       │
│                                                              │
│  VERSION CONTROL:                                            │
│  Git + LFS (indie) / Perforce (large teams)                │
│                                                              │
│  ART TOOLS:                                                  │
│  Blender / Maya / Substance Painter / Photoshop            │
│                                                              │
│  AUDIO:                                                      │
│  Wwise / FMOD / Reaper / Audacity                          │
│                                                              │
│  PROJECT MANAGEMENT:                                         │
│  Jira / Notion / Trello / Linear                            │
│                                                              │
│  COMMUNICATION:                                              │
│  Slack / Discord / Teams                                    │
└─────────────────────────────────────────────────────────────┘
```

## Git Workflow for Games

```
GIT BRANCHING STRATEGY:
┌─────────────────────────────────────────────────────────────┐
│                                                              │
│  main ─────●─────────●─────────●─────────● (releases)      │
│             ↑         ↑         ↑         ↑                 │
│  develop ──●──●──●───●──●──●───●──●──●───● (integration)   │
│             ↑  ↑      ↑  ↑                                  │
│  feature/X─●──●      ●──●                                   │
│                                                              │
│  BRANCH TYPES:                                               │
│  main:       Production releases only                       │
│  develop:    Integration branch, daily builds               │
│  feature/*:  New features, short-lived                      │
│  fix/*:      Bug fixes                                      │
│  release/*:  Release preparation                            │
└─────────────────────────────────────────────────────────────┘

GIT LFS CONFIGURATION:
┌─────────────────────────────────────────────────────────────┐
│  .gitattributes:                                             │
│  *.psd filter=lfs diff=lfs merge=lfs -text                 │
│  *.fbx filter=lfs diff=lfs merge=lfs -text                 │
│  *.wav filter=lfs diff=lfs merge=lfs -text                 │
│  *.mp3 filter=lfs diff=lfs merge=lfs -text                 │
│  *.png filter=lfs diff=lfs merge=lfs -text                 │
│  *.tga filter=lfs diff=lfs merge=lfs -text                 │
│  *.zip filter=lfs diff=lfs merge=lfs -text                 │
└─────────────────────────────────────────────────────────────┘
```

## Commit Convention

```
COMMIT MESSAGE FORMAT:
┌─────────────────────────────────────────────────────────────┐
│  PREFIX: Description (max 50 chars)                         │
│                                                              │
│  PREFIXES:                                                   │
│  feat:     New feature                                      │
│  fix:      Bug fix                                          │
│  art:      Art/visual changes                               │
│  audio:    Sound/music changes                              │
│  level:    Level design changes                             │
│  refactor: Code restructuring                               │
│  perf:     Performance improvements                         │
│  test:     Test additions/changes                           │
│  ci:       CI/CD changes                                    │
│  docs:     Documentation                                    │
│                                                              │
│  EXAMPLES:                                                   │
│  feat: Add double jump ability                              │
│  fix: Resolve player falling through floor                  │
│  art: Update hero character textures                        │
│  perf: Optimize enemy spawning system                       │
└─────────────────────────────────────────────────────────────┘
```

## Build Automation

```python
# ✅ Production-Ready: Build Script
import subprocess
import os
from datetime import datetime

class GameBuilder:
    def __init__(self, project_path: str, unity_path: str):
        self.project_path = project_path
        self.unity_path = unity_path
        self.build_number = self._get_build_number()

    def build(self, platform: str, config: str = "Release"):
        build_path = f"Builds/{platform}/{self.build_number}"

        args = [
            self.unity_path,
            "-quit",
            "-batchmode",
            "-projectPath", self.project_path,
            "-executeMethod", "BuildScript.Build",
            f"-buildTarget", platform,
            f"-buildPath", build_path,
            f"-buildConfig", config,
            "-logFile", f"Logs/build_{platform}.log"
        ]

        result = subprocess.run(args, capture_output=True)

        if result.returncode != 0:
            raise Exception(f"Build failed: {result.stderr}")

        return build_path

    def _get_build_number(self) -> str:
        return datetime.now().strftime("%Y%m%d.%H%M")
```

## Team Workflow

```
AGILE SPRINT WORKFLOW:
┌─────────────────────────────────────────────────────────────┐
│  DAY 1: Sprint Planning                                      │
│  • Review backlog                                           │
│  • Commit to sprint goals                                   │
│  • Break into tasks                                         │
├─────────────────────────────────────────────────────────────┤
│  DAILY: Standup (15 min)                                    │
│  • What did you do?                                         │
│  • What will you do?                                        │
│  • Any blockers?                                            │
├─────────────────────────────────────────────────────────────┤
│  CONTINUOUS: Development                                    │
│  • Work on tasks                                            │
│  • Daily builds/tests                                       │
│  • Code reviews                                             │
├─────────────────────────────────────────────────────────────┤
│  PLAYTEST: Mid-sprint                                       │
│  • Team plays current build                                 │
│  • Gather feedback                                          │
│  • Adjust priorities                                        │
├─────────────────────────────────────────────────────────────┤
│  END: Sprint Review + Retro                                 │
│  • Demo completed work                                      │
│  • What went well/poorly?                                   │
│  • Improvements for next sprint                             │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Troubleshooting

```
┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Merge conflicts in scene files                     │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Use prefabs instead of scene objects                      │
│ → Smart merge tools (Unity Smart Merge)                     │
│ → Coordinate who works on which scenes                      │
│ → Use scene additivity                                      │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Repository growing too large                       │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Configure Git LFS properly                                │
│ → Clean up old branches                                     │
│ → Don't commit generated files (Library/)                   │
│ → Use .gitignore templates for game engines                │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Builds breaking frequently                         │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Add CI build on every PR                                  │
│ → Implement smoke tests                                     │
│ → Require passing builds before merge                       │
│ → Add pre-commit hooks for validation                       │
└─────────────────────────────────────────────────────────────┘
```

## Essential .gitignore

```gitignore
# Unity
Library/
Temp/
Obj/
Build/
*.csproj
*.unityproj
*.sln

# Unreal
Intermediate/
Saved/
DerivedDataCache/
*.sln

# Common
*.log
*.tmp
.DS_Store
Thumbs.db
```

---

**Use this skill**: When setting up pipelines, managing assets, or automating builds.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
