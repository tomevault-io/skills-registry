---
name: pytest-style
description: Convenciones y reglas de performance para tests con pytest. Invocar cuando se escriban tests unitarios o de integración en Python, se configure pytest en un proyecto nuevo, o se audite una suite que tarda más de lo esperado. Cubre naming, AAA, SAVEPOINT-per-test, paralelismo con xdist, mocks obligatorios de renderers/SDKs y checklist de validación antes de declarar "tests listos". Use when this capability is needed.
metadata:
  author: xguilxr
---

# pytest — Convenciones + Performance

Esta skill tiene dos capas: **estilo** (naming, AAA, parametrize, factories) y **performance** (schema one-shot + SAVEPOINT, xdist, mocks obligatorios, markers, checklist). La capa de performance es innegociable: una suite que tarda más de 60s para <500 tests está mal configurada, no "es lo que es".

> **Detectar antes de imponer**: antes de scaffoldear `conftest.py`, leé `pyproject.toml` / `requirements*.txt` para ver qué ya está instalado (pytest-asyncio, pytest-xdist, factory-boy, freezegun). Si el proyecto usa `unittest`, no fuerces pytest — adaptá las reglas aplicables.

## Estructura

```
tests/
├── conftest.py          # fixtures compartidos (engine session-scoped, SAVEPOINT)
├── factories.py         # factory-boy factories
├── unit/
│   └── test_services.py
└── integration/
    └── test_endpoints.py
```

## Regla 1 — Schema de DB UNA sola vez por worker

**Anti-pattern letal**: `Base.metadata.create_all` + `drop_all` en un fixture function-scoped corre el DDL completo por cada test. Con 300 tests y un schema no trivial esto suma minutos.

**Patrón correcto**:
- `engine` scope `session` — se crea una vez por worker de xdist.
- `create_all` UNA vez al crear el engine; nunca `drop_all` per-test.
- `db_session` function-scoped abre una **nested transaction (SAVEPOINT)** y hace `rollback` al terminar. Cada test ve una DB "limpia" sin regenerar el schema.

Template de referencia completo en `templates/pytest/conftest.py`. Snippet clave:

```python
@pytest.fixture(scope="session")
def engine():
    eng = create_engine("sqlite:///:memory:", future=True)
    Base.metadata.create_all(eng)
    yield eng
    eng.dispose()

@pytest.fixture()
def db_session(engine):
    connection = engine.connect()
    trans = connection.begin()
    session = Session(bind=connection, join_transaction_mode="create_savepoint")
    try:
        yield session
    finally:
        session.close()
        trans.rollback()
        connection.close()
```

Para async: misma idea con `AsyncEngine` + `AsyncSession` y `begin_nested()`.

## Regla 2 — Paralelismo por default (pytest-xdist)

Agregar `pytest-xdist` a `requirements-dev.txt` / `pyproject.toml`. El comando canónico es:

```bash
pytest -n auto --dist loadfile
```

- `-n auto` — un worker por CPU.
- `--dist loadfile` — todos los tests de un mismo archivo van al mismo worker. Evita que fixtures module-scoped se ejecuten N veces.

Los fixtures deben ser **seguros entre workers**. SQLite in-memory es naturalmente aislado porque cada worker corre en proceso separado. Si usás PostgreSQL de test, cada worker necesita su propia DB (usar `pytest-postgresql` o prefijar DB name con `PYTEST_XDIST_WORKER`).

## Regla 3 — Mocks obligatorios para operaciones pesadas

Renderizar un PDF real, llamar a OpenAI, subir a S3 real — cada uno agrega segundos. **Por default se mockean**. Tests que quieran ejercer el render real se marcan `@pytest.mark.heavy` y corren en un job separado del gate de PR.

Lista de clientes / operaciones que **siempre** se mockean por default:

| Área | Librerías típicas | Cómo mockear |
|---|---|---|
| PDF | `weasyprint`, `reportlab`, `wkhtmltopdf`, `pdfkit` | `monkeypatch` devuelve `b"%PDF-1.4 stub"` |
| Docs | `python-docx`, `openpyxl` (si el libro es pesado) | stub que escribe archivo vacío |
| LLM APIs | `openai`, `anthropic`, `cohere` | `AsyncMock` con response canónica |
| Cloud | `boto3`, `google-cloud-*` | `moto` (S3) o `MagicMock` |
| Email | `resend`, `sendgrid`, `smtplib` | capturar llamadas, no enviar |
| HTTP | `httpx`, `requests` | `respx` / `responses` |
| Celery/Redis | `celery`, `redis` | eager mode + `fakeredis` |

Patrón con `autouse=True` para cubrir por default:

```python
@pytest.fixture(autouse=True)
def _mock_heavy_renderers(monkeypatch, request):
    if "heavy" in request.keywords:
        return
    monkeypatch.setattr("app.pdf.render", lambda *a, **kw: b"%PDF-1.4 stub")
    monkeypatch.setattr("app.llm.openai_client", AsyncMock(...))
    ...
```

## Regla 4 — Fixtures de setup compartidas, no `_setup()` helpers

**Anti-pattern**: cada test llama `_setup()` que hace 4 POSTs para crear tenant + user + login + resource. 300 tests × 4 round-trips = suite lenta.

**Patrón correcto**: fixtures con scope apropiado (`module` o `session` con SAVEPOINT) que crean el setup una vez y se comparten.

```python
@pytest.fixture(scope="session")
def sample_tenant(db_session_factory):
    with db_session_factory() as s:
        tenant = TenantFactory(name="Acme")
        s.commit()
        return tenant.id  # devolver ID, no objeto ORM (detached entre sessions)

@pytest.fixture()
def authed_client(client, db_session, sample_tenant):
    user = UserFactory(tenant_id=sample_tenant, role="admin")
    db_session.flush()
    token = issue_test_token(user)
    client.headers.update({"Authorization": f"Bearer {token}"})
    return client
```

## Regla 5 — Config de seguridad "rápida" para tests

Tests no deben pagar el costo de hashes de producción. En `conftest.py` o `pytest.ini`:

- **bcrypt**: `BCRYPT_ROUNDS=4` (default prod 12+). Cambia un test de ~300ms a ~3ms.
- **Argon2**: `time_cost=1, memory_cost=8, parallelism=1`.
- **JWT secrets**: hardcodeados de test, no leer de entorno real.
- **Celery**: `task_always_eager=True, task_eager_propagates=True`.
- **Redis**: `fakeredis` o `AsyncMock`.

Variables típicas:

```python
# conftest.py (top-level)
import os
os.environ.setdefault("BCRYPT_ROUNDS", "4")
os.environ.setdefault("JWT_SECRET", "test-secret-not-for-prod")
os.environ.setdefault("CELERY_TASK_ALWAYS_EAGER", "true")
```

## Regla 6 — Markers registrados

En `pyproject.toml`:

```toml
[tool.pytest.ini_options]
markers = [
    "heavy: tests que ejercen renderizado real o servicios externos. Corren en job aparte, NO en gate de PR.",
    "slow: tests >2s que no son heavy. Se pueden excluir con -m 'not slow' para feedback rápido.",
    "integration: tests que requieren infra real (docker-compose con postgres/redis/etc).",
]
addopts = "-m 'not heavy'"  # default del gate: excluir heavy
```

Uso:

```python
@pytest.mark.heavy
def test_real_pdf_render_matches_snapshot(...):
    ...

@pytest.mark.slow
def test_large_aggregation_query(...):
    ...
```

## Regla 7 — Nunca `sleep` real

`time.sleep(1)` en 10 tests = 10s muertos. Mockeá el reloj.

```python
from freezegun import freeze_time

@freeze_time("2026-04-24 12:00:00")
def test_token_expires_after_one_hour():
    token = issue_token()
    with freeze_time("2026-04-24 13:00:01"):
        assert is_expired(token)
```

Para async, mockear `asyncio.sleep`:

```python
@pytest.fixture(autouse=True)
def _fast_sleep(monkeypatch):
    async def _noop(_): pass
    monkeypatch.setattr("asyncio.sleep", _noop)
```

## Naming

```python
def test_create_user_returns_201_with_valid_payload(): ...
def test_create_user_returns_409_when_email_exists(): ...
def test_create_user_returns_422_when_email_is_invalid(): ...
```

Patrón: `test_<acción>_<resultado_esperado>_<cuándo>`.

## AAA pattern

```python
async def test_create_user_returns_201(authed_client):
    # Arrange
    payload = {"email": "a@b.com", "name": "Ana", "password": "secret123"}

    # Act
    response = await authed_client.post("/users/", json=payload)

    # Assert
    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "a@b.com"
    assert "id" in data
```

## Parametrize

```python
@pytest.mark.parametrize("email,expected_status", [
    ("valid@email.com", 201),
    ("invalid-email",    422),
    ("",                 422),
    (None,               422),
])
async def test_create_user_email_validation(client, email, expected_status):
    response = await client.post("/users/", json={"email": email, "name": "x", "password": "x"})
    assert response.status_code == expected_status
```

## Factories con factory-boy

```python
# tests/factories.py
import factory
from app.models.user import User

class UserFactory(factory.Factory):
    class Meta:
        model = User
    email = factory.Sequence(lambda n: f"user{n}@test.com")
    name = factory.Faker("name")
```

## Checklist de validación (obligatorio antes de cerrar)

Antes de declarar "tests generados" / "suite lista", correr y reportar:

1. **`pytest --collect-only -q`** — reportar número total de tests.
2. **`time pytest -n auto -q --durations=10`** — reportar duración total + top-10 más lentos.
3. **Bloqueos duros** (no cerrar la tarea si alguno aplica):
   - Algún test > 2s sin marker `heavy` o `slow` → proponer mock específico, fixture reusable o mover a marker.
   - Duración total > 60s para <500 tests → revisar fixtures module/session-scoped, SAVEPOINT, xdist.
   - `pytest -n auto` falla pero single-worker pasa → fixture no es thread-safe (estado global, mock mal aislado).
4. **Reporte final** con: total tests, duración, top-10 lentos, decisiones tomadas (qué se mockeó, qué se marcó heavy).

## Anti-patterns

- **`create_all` / `drop_all` en fixture function-scoped** — el asesino #1 de performance.
- **`_setup()` helper llamado en cada test** — 4 round-trips × N tests = suite lenta. Usar fixtures.
- **Render real de PDF / call real a OpenAI en unit tests** — mover a `@pytest.mark.heavy`.
- **`time.sleep` / `asyncio.sleep` real** — usar `freezegun` o monkeypatch.
- **bcrypt con rounds de producción en tests** — bajar a 4.
- **Un test con N asserts sobre N cosas distintas** — dividir.
- **Tests que dependen del orden** — cada test debe poder correr aislado y en cualquier worker.
- **Mockear lo que estás testeando** — si mockeaste la función bajo test, no estás testeando nada.
- **Sin assert** — un `try/except` sin assert no valida nada.

## Ver también

- `templates/pytest/conftest.py` — template de referencia completo (engine session-scoped, SAVEPOINT, mocks autouse).
- `github-actions-ci` — cómo orquestar el split `lint / typecheck / test-smoke / test-heavy` en CI.
- `fastapi-structure` — estructura de proyecto compatible con estos fixtures.

---
> Source: [xguilxr/claudio-enterprises](https://github.com/xguilxr/claudio-enterprises) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
