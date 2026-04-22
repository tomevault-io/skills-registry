---
name: airflow-best-practices
description: Guidelines for Airflow DAGs in the NFL pipeline. Use when this capability is needed.
metadata:
  author: ucalegon206
---

# Airflow Best Practices

## 1. Parameterized Execution
- DAGs should be capable of running for specific parameters (`year`, `week`).
- Use `{{ dag_run.conf.get('key', 'default') }}` to pass these to scripts.

## 2. Testing
- **Integrity**: Always strictly check for import errors and cycles (`test_dag_integrity.py`).
- **Idempotency**: A task should produce the same result if run twice.

## 3. Script Interface
- Airflow should call scripts via `BashOperator` using the Virtual Environment python: `{VENV_PYTHON} script.py --arg val`.
- Do NOT use `PythonOperator` to call massive scripts directly if they have heavy dependencies or memory leaks; isolation via Bash/Subprocess is preferred for this project.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ucalegon206) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
