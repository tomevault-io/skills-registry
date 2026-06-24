---
name: start-backend
description: Start the Django backend server on port 8008 Use when this capability is needed.
metadata:
  author: Hairash
---

Start the Django backend development server:

## Quick Start

```bash
cd backend && source venv/bin/activate && DJANGO_DEBUG=True daphne -b 0.0.0.0 -p 8008 server.asgi:application
```

The backend will be available at http://localhost:8008

## Pre-flight Checks

Before starting, verify:

1. **Virtual environment exists**:
   ```bash
   ls backend/venv/bin/activate
   ```
   If not, create it: `cd backend && python -m venv venv`

2. **Dependencies installed**:
   ```bash
   cd backend && source venv/bin/activate && pip freeze | head -5
   ```
   If empty, run: `pip install -r requirements.txt`

3. **Database migrated**:
   ```bash
   cd backend && source venv/bin/activate && python manage.py showmigrations --plan | grep -c '\[ \]'
   ```
   If non-zero, run: `python manage.py migrate`

4. **Port available**:
   ```bash
   lsof -i :8008
   ```
   If in use, kill the process or use a different port

## Troubleshooting

- **"ModuleNotFoundError"**: Activate venv and install requirements
- **"Port already in use"**: Run `lsof -i :8008` and kill the process
- **"Database locked"**: Wait for other processes to finish
- **WebSocket not working**: Ensure using Daphne, not `python manage.py runserver`

---
> Source: [Hairash/little-dinos-game](https://github.com/Hairash/little-dinos-game) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
