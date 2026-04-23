---
name: fastapi-backend-template
description: FastAPI with PostgreSQL, async SQLAlchemy 2.0, Alembic, and Docker. Use when this capability is needed.
metadata:
  author: eng0ai
---

# FastAPI Backend Template

FastAPI with PostgreSQL, async SQLAlchemy 2.0, Alembic migrations, and Docker.

## Tech Stack

- **Framework**: FastAPI
- **Language**: Python
- **ORM**: SQLAlchemy 2.0 (async)
- **Migrations**: Alembic
- **Database**: PostgreSQL

## Prerequisites

- Python 3.11+
- Docker (recommended)
- PostgreSQL

## Setup

### 1. Clone the Template

```bash
git clone --depth 1 https://github.com/Aeternalis-Ingenium/FastAPI-Backend-Template.git .
```

If the directory is not empty:

```bash
git clone --depth 1 https://github.com/Aeternalis-Ingenium/FastAPI-Backend-Template.git _temp_template
mv _temp_template/* _temp_template/.* . 2>/dev/null || true
rm -rf _temp_template
```

### 2. Remove Git History (Optional)

```bash
rm -rf .git
git init
```

### 3. Setup Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
```

### 4. Setup Database

Configure PostgreSQL and run Alembic migrations.

## Development

```bash
uvicorn main:app --reload
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eng0ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
