---
name: tools-automation
description: > Use when this capability is needed.
metadata:
  author: dboone323
---

# Tools-Automation Project Expert

You are working in the **tools-automation** monorepo which contains:

- 50+ AI agents for automation
- Docker-based infrastructure (Redis, Ollama, Open WebUI, Grafana)
- iOS/macOS apps: HabitQuest, AvoidObstaclesGame, CodingReviewer, MomentumFinance, PlannerApp
- Shared-Kit Swift package

## Project Structure

```
tools-automation/
├── agents/           # 50+ smart agents (.sh, .py)
├── config/           # agent_personas.json, agent_capabilities.json
├── docker/           # Docker configs
├── docker-compose.yml
├── scripts/          # Utility scripts
├── src/              # Python modules
├── tests/            # Test suites
├── HabitQuest/       # iOS app - habit tracking
├── AvoidObstaclesGame/  # iOS game
├── CodingReviewer/   # macOS code review app
├── MomentumFinance/  # Finance app
├── PlannerApp/       # Planning app
└── Shared-Kit/       # Shared Swift package
```

## Key Commands

```bash
# Start all Docker services
docker compose up -d

# Check system health
/system_health_check

# Run all tests
bash scripts/run_all_tests_enhanced.sh

# Security scan
bash scripts/security/scan_containers.sh
```

## Agent System

- Agents are in `agents/` directory
- Personas defined in `config/agent_personas.json`
- Test certification via `tests/agent_skills/certification_runner.py`

## iOS Projects

All use Swift/SwiftUI with XCTest for testing:

- Open with Xcode: `open ProjectName.xcodeproj`
- Run tests: `Cmd+U` in Xcode

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dboone323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
