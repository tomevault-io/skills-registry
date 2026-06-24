---
name: ci-cd-pipelines
description: Experto en CI/CD con GitHub Actions. Automatización de tests, releases y deployments. Use when this capability is needed.
metadata:
  author: ACubero
---

# 🔄 CI/CD Pipelines Expert

Skill para configurar pipelines de integración y despliegue continuo.

## Cuándo Usar

- Configurar GitHub Actions
- Automatizar tests y linting
- Crear releases automáticos
- Configurar deployments

---

## 📝 GitHub Actions - Python CI

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: "pip"

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-dev.txt

      - name: Lint with ruff
        run: ruff check .

      - name: Type check with mypy
        run: mypy src/

      - name: Test with pytest
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          file: coverage.xml

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
```

---

## 🏷️ Auto Release

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - "v*"

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          generate_release_notes: true
```

---

## 🚀 Deploy

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  workflow_run:
    workflows: ["CI"]
    branches: [main]
    types: [completed]

jobs:
  deploy:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to production
        run: echo "Deploy commands here"
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

---

## ✅ Checklist

- [ ] ¿CI ejecuta tests en cada PR?
- [ ] ¿Linting automático?
- [ ] ¿Matrix testing múltiples versiones?
- [ ] ¿Coverage reports?
- [ ] ¿Releases automáticos con tags?

---
> Source: [ACubero/IA_AGENT_esqueleto_proyectos_python_antigravity](https://github.com/ACubero/IA_AGENT_esqueleto_proyectos_python_antigravity) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
