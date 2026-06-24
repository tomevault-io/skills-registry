---
name: pytest-fixtures
description: Pytest com fixtures e parametrize, sem unittest.TestCase Use when this capability is needed.
metadata:
  author: alanpcf
---
- Setup via `@pytest.fixture` com `scope=` explícito (`function`, `module`, `session`).
- Casos variados via `@pytest.mark.parametrize` com `ids=` legíveis.
- Sem `unittest.TestCase` em código novo.
- Asserts simples — pytest reescreve mensagem automaticamente.
- `conftest.py` por pasta pra fixtures compartilhadas.
- Marcadores customizados em `pyproject.toml [tool.pytest.ini_options]`.

---
> Source: [alanpcf/cocada](https://github.com/alanpcf/cocada) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
