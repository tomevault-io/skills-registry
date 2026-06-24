---
name: ruff-linter-formatter
description: Ruff como linter e formatter padrão para Python (substitui Flake8, Black, isort) Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Ruff – Linter e Formatter Python

**Ruff é o linter e formatter padrão para projetos Python a partir de agora.**

Ruff é escrito em Rust, 10–100x mais rápido que Flake8/Black/isort, e substitui em um único binário: Flake8, Black, isort, pydocstyle, pyupgrade, autoflake.

---

## Quando Usar

- **Todo projeto Python** (estudos e trabalho): FastAPI, Django, scripts, notebooks, libs.
- **Antes de commit:** rodar `ruff check` e `ruff format` (ou `ruff check --fix` e `ruff format`).
- **Em CI:** incluir `ruff check` e `ruff format --check` no pipeline.
- **No código:** respeitar as regras que o Ruff reporta; usar `# noqa` só quando justificado.

---

## Comandos

### Lint (checagem e correção automática)

```bash
# Checar apenas (não altera arquivos)
ruff check .

# Checar e aplicar correções automáticas
ruff check . --fix

# Checar caminho específico
ruff check src/ scripts/ tests/
```

### Format (formatação estilo Black)

```bash
# Formatar arquivos
ruff format .

# Apenas checar se está formatado (útil em CI)
ruff format . --check
```

### Uso típico antes de commit

```bash
ruff check . --fix && ruff format .
```

---

## Configuração

Configurar em **`pyproject.toml`** (preferido), **`ruff.toml`** ou **`.ruff.toml`**.

### Exemplo mínimo em `pyproject.toml`

```toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP"]
ignore = ["E501"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

### Opções úteis

| Seção | Opção | Descrição |
|-------|--------|-----------|
| `[tool.ruff]` | `line-length` | Comprimento máximo de linha (default 88, como Black) |
| | `target-version` | Versão mínima Python: py39, py310, py311, py312 |
| | `exclude` | Pastas/arquivos excluídos (ex.: `.venv`, `build`) |
| `[tool.ruff.lint]` | `select` | Regras habilitadas (E, F, I, N, W, UP, B, etc.) |
| | `ignore` | Regras desabilitadas (ex.: E501 para não forçar line-length) |
| | `fixable` | Regras que podem ser corrigidas com `--fix` |
| `[tool.ruff.lint.per-file-ignores]` | `"__init__.py"` | Ex.: `["E402"]` para imports não no topo |

### Regras comuns

- **E**: pycodestyle errors  
- **F**: Pyflakes  
- **I**: isort (imports)  
- **N**: pep8-naming  
- **W**: pycodestyle warnings  
- **UP**: pyupgrade (sintaxe moderna)  
- **B**: flake8-bugbear  

---

## Integração em projetos

### Novo projeto Python

1. Adicionar `ruff>=0.2.0` em `requirements.txt` ou `[project.optional-dependencies].dev` no `pyproject.toml`.
2. Criar `[tool.ruff]` (e `[tool.ruff.lint]`, `[tool.ruff.format]`) no `pyproject.toml` do projeto.
3. No Makefile (se houver): `lint: ruff check .` e `format: ruff format .` (ou `lint: ruff check . --fix && ruff format .`).
4. Em CI: passo que rode `ruff check .` e `ruff format . --check`.

### Projetos existentes (threat-modeling-ai, fastapi-microservice-framework, etc.)

- **fastapi-microservice-framework:** já usa Ruff em `pyproject.toml` e no CI.
- **threat-modeling-ai:** adicionar `pyproject.toml` com `[tool.ruff]` e incluir `ruff` nas dependências de dev; rodar `ruff check --fix` e `ruff format` no código Python e notebooks.

---

## Regras para os agentes

1. **Projetos Python:** usar Ruff como linter e formatter padrão. Não introduzir Black, Flake8 ou isort em projetos novos; em projetos antigos, migrar para Ruff quando for tocar no repo.
2. **Antes de sugerir commit:** garantir que `ruff check .` e `ruff format .` passem (ou que o usuário rode).
3. **Configuração:** preferir `pyproject.toml` com `[tool.ruff]` para manter tudo no mesmo lugar.
4. **Notebooks:** Ruff linta e formata `.ipynb` por padrão (Ruff 0.6+). Para só lintar ou só formatar notebooks, usar `[tool.ruff.format] exclude = ["*.ipynb"]` ou `[tool.ruff.lint] exclude` conforme necessário.

---

## Referências

- **Documentação:** https://docs.astral.sh/ruff/
- **Configuração:** https://docs.astral.sh/ruff/configuration/
- **Regras (linter):** https://docs.astral.sh/ruff/linter/
- **Editor (VS Code):** extensão Ruff oficial; usar Ruff como formatter/linter no lugar de Pylance/Black quando configurado.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
