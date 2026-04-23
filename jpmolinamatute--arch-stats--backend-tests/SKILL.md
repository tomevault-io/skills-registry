---
name: backend-tests
description: How to run pytest to ensure code quality and functionality Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Python Tests

We use Pytest for unit testing. We also use ./backend/pyproject.toml to configure Pytest.
There are two ways to run tests check:

1. Manually:

    ```bash
    docker compose -f ./docker/docker-compose.yaml up -d  # this will start the PostgreSQL Database
    cd ./backend
    uv run pytest -vv
    cd -
    docker compose -f ./docker/docker-compose.yaml down  # this will stop the PostgreSQL Database
    ```

2. Via script (run from project root), this will also run formatting, lint and type annotation check:

    ```bash
    ./scripts/linting.bash --backend
    ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
