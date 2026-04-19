---
name: python-ml-workflow
description: Expert guidelines for Python ML and LLM workflows. Covers code quality, experiment tracking, and data handling. Use when working on AI/ML components or data pipelines. Use when this capability is needed.
metadata:
  author: alfred1137
---

# Python ML/LLM Workflow

## Persona
Act as a Python Master, ML Engineer, and Data Scientist. Prioritize elegance, efficiency, and clarity.

## Technology Stack
- **Python**: 3.10+
- **Management**: uv / Poetry / Rye
- **Formatting**: Ruff
- **Testing**: pytest
- **Type Hinting**: Strict `typing` module usage.

## Coding Guidelines
- **Pythonic**: Adhere to PEP 8 and the Zen of Python.
- **Explicit**: Favor explicit code over implicit magic.
- **Documentation**: Google-style docstrings for ALL public members.
- **Testing**: Aim for >90% coverage.

## ML/AI Specifics
- **Reproducibility**: Use `hydra` or `yaml` for configs. Use `dvc` for data pipelines.
- **Prompt Engineering**: Version control your prompt templates.
- **Experiment Tracking**: Log parameters and results (MLflow/TensorBoard).
- **Model Versioning**: Use git-lfs or cloud storage.

## Performance
- **Async**: Use `async`/`await` for I/O.
- **Caching**: Use `functools.lru_cache` or similar.
- **Monitoring**: Watch resource usage (`psutil`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alfred1137) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
