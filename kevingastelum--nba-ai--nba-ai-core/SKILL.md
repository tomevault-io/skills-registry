---
name: nba-ai-core
description: Core knowledge for the NBA AI project, including data pipeline, prediction engines, and system architecture. Use when this capability is needed.
metadata:
  author: kevingastelum
---

# NBA AI Core Skill

This skill provides comprehensive context for maintaining and upgrading the NBA AI system.

## 🏗️ System Architecture

### 9-Stage Data Pipeline
The data pipeline is orchestrated by `src/database_updater/database_update_manager.py` and consists of the following sequential stages:

1.  **Schedule Update**: Fetch game schedule from NBA Stats API.
2.  **Players Update**: Update player reference data.
3.  **Injuries Update**: Fetch official NBA injury report PDFs.
4.  **Betting Update**: Unified betting lines from ESPN API + Covers.com.
5.  **PbP Collection**: Fetch play-by-play data (CDN primary, Stats API fallback).
6.  **GameStates Parsing**: Transform raw PBP into structured snapshots.
7.  **Boxscores Collection**: Fetch traditional PlayerBox and TeamBox stats.
8.  **Pre-Game Data**: Generate prior states and feature sets (34 rolling average features).
9.  **Predictions**: Generate pre-game and live predictions.

### Database Strategy
- **SQLite**: Primary storage. 
- **Three-DB Setup**: 
    - `current`: (~500MB) Latest season data.
    - `dev`: (~3GB) Last 3 seasons (Default for work).
    - `full`: (~25GB) Historical archive (1999-present).
- **Timezones**: Always store in UTC. Query logic uses Eastern Time (NBA ops). Display uses user local.

## 🧠 Prediction Engines

All predictors must inherit from or follow the pattern in `src/predictions/prediction_manager.py`:
- `make_pre_game_predictions(game_ids)`
- `make_current_predictions(game_ids)`

### Current Engines:
- **Baseline**: PPG-based.
- **Linear**: Ridge Regression.
- **Tree**: XGBoost (Default).
- **MLP/Ensemble**: Torch-based (Optional).

**Future Goal**: Transit to GenAI-based engines using sequential PBP data.

## 🛠️ Development Workflows

### Environment Setup (CRITICAL)
Always use the virtual environment:
```bash
# Windows
.\venv\Scripts\activate
# Unix
source venv/bin/activate
```

### Common Commands
- **Start Web App**: `python start_app.py --predictor=Tree --log_level=INFO`
- **Batch Update**: `python -m src.database_updater.database_update_manager --season=2025-2026 --predictor=Tree`
- **Health Check**: `python -m src.health_check --season=2025-2026`
- **Run Tests**: `pytest -v`

## 📋 Coding Rules & Pitfalls
- **No verbose comments**: Use self-documenting code.
- **No hardcoded paths**: Use `src/config.py` loaded config.
- **Always ask before committing**: Solo dev setup doesn't mean auto-commits.
- **Transitive dependencies**: Never add them to `requirements.txt`.
- **CRITICAL: Row Factory**: When querying SQLite, always use `conn.row_factory = sqlite3.Row`. Failure to do so causes `TypeError` in API layers that expect dictionary-like access.

## 🔗 Key Files
- `config.yaml`: Central configuration.
- `DATA_MODEL.md`: Schema source of truth.
- `project-instructions.md`: Deep AI context.
- `TODO.md`: Sprint and task tracking.
- `AGENT_HANDOVER_REPORT.md`: Detailed history of fixes and project state (Feb 2026).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevingastelum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
