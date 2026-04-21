---
name: truthseeker-health
description: Analiza zdrowia projektu TruthSeeker - kod, testy, bezpieczeństwo, dokumentacja Use when this capability is needed.
metadata:
  author: xmadmaro
---

Przeprowadź analizę zdrowia projektu TruthSeeker.

**Steps**

1. **Sprawdź strukturę projektu**
   
   Oczekiwana struktura:
   ```
   truthseeker/
   ├── src/
   │   ├── agents/          # 6 agentów
   │   ├── api/             # FastAPI endpoints
   │   ├── core/            # config, models
   │   └── infrastructure/  # LLM, Qdrant
   ├── tests/               # Testy pytest
   ├── pyproject.toml       # Zależności
   ├── docker-compose.yml   # Infrastruktura
   ├── .env.example         # Konfiguracja
   └── README.md            # Dokumentacja
   ```
   
   Zweryfikuj:
   - [ ] Wszystkie katalogi istnieją
   - [ ] Pliki `__init__.py` są obecne
   - [ ] README.md jest aktualny

2. **Sprawdź jakość kodu**
   
   Uruchom lintery:
   ```bash
   cd c:\Users\Trzyb\Desktop\truthseeker
   
   # Ruff (Python linter)
   uv run ruff check src/
   
   # Mypy (type checking)
   uv run mypy src/ --ignore-missing-imports
   
   # Format z Black
   uv run black src/ --check
   ```
   
   Zidentyfikuj:
   - [ ] Nieużywane importy
   - [ ] Brakujące type hints
   - [ ] Niespójne formatowanie
   - [ ] Złożone funkcje (>50 linii)

3. **Sprawdź testy**
   
   Uruchom testy:
   ```bash
   uv run pytest tests/ -v --tb=short
   ```
   
   Sprawdź pokrycie:
   ```bash
   uv run pytest tests/ --cov=src --cov-report=html
   ```
   
   Zidentyfikuj:
   - [ ] Brakujące testy dla agentów
   - [ ] Niestabilne testy (flaky)
   - [ ] Pokrycie < 70%

4. **Sprawdź bezpieczeństwo**
   
   Secrets w kodzie:
   ```bash
   findstr /S /I "password\|secret\|api_key\|token" src\*.py
   ```
   
   Zależności:
   ```bash
   uv pip audit
   ```
   
   Zidentyfikuj:
   - [ ] Hardcoded secrets
   - [ ] Podatne zależności
   - [ ] Brak walidacji inputów
   - [ ] SQL injection (raw queries)

5. **Sprawdź dokumentację**
   
   Zweryfikuj:
   - [ ] README.md - instrukcja uruchomienia
   - [ ] architecture.md - aktualny diagram
   - [ ] agents.md - opis wszystkich agentów
   - [ ] truthseeker_prompts.md - wszystkie prompty
   - [ ] API docs (FastAPI /docs)

6. **Sprawdź spójność agentów**
   
   Dla każdego agenta w `src/agents/`:
   - [ ] Ma odpowiedni System Prompt w `truthseeker_prompts.md`
   - [ ] Ma dataclass Input i Output
   - [ ] Loguje swoje działania
   - [ ] Obsługuje błędy (try/except)
   - [ ] Jest zarejestrowany w Orchestratorze

7. **Wygeneruj raport**
   
   ```markdown
   ## 🏥 TruthSeeker Health Report
   
   **Data:** <timestamp>
   
   ### Podsumowanie
   | Kategoria | Status | Problemy |
   |-----------|--------|----------|
   | Struktura | ✅/⚠️/❌ | X |
   | Jakość kodu | ✅/⚠️/❌ | X |
   | Testy | ✅/⚠️/❌ | X |
   | Bezpieczeństwo | ✅/⚠️/❌ | X |
   | Dokumentacja | ✅/⚠️/❌ | X |
   | Agenci | ✅/⚠️/❌ | X |
   
   **Health Score:** X/100
   
   ### Problemy do naprawienia
   1. ...
   2. ...
   
   ### Rekomendacje
   1. ...
   2. ...
   ```

**Output**
Zapisz raport do `truthseeker/reports/health-<timestamp>.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmadmaro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
