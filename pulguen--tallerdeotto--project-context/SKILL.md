---
name: project-context
description: Combined context for Taller de Otto project (Backend + Frontend) and Design Guidelines. Use when this capability is needed.
metadata:
  author: pulguen
---

# Taller de Otto - Project Context

## Project Overview
**Taller de Otto** is a portfolio and services website for a creative workshop.
- **Root Path:** `c:\Datos\Ric\TDO\2025\el taller\tallerdeotto.com`
- **Aesthetic:** "Dark Premium". High contrast, black backgrounds, neon accents, glassmorphism.
- **Key Technologies:** Django 5.2 (Backend), React + Vite (Frontend).

## Backend: `tdobackend`
- **Path:** `./tdobackend`
- **Tech Setup:**
  - Django 5.2 + Django REST Framework + SimpleJWT.
  - Database: SQLite (dev: `db.sqlite3`), Postgres (prod).
  - Virtual Environment: `.venv` (Python 3.12 recommended).
- **Core Commands:**
  - Start Server: `python manage.py runserver` (runs on port 8000).
  - SSL Server (if needed): `python manage.py runsslserver`.
  - Migrations: `python manage.py makemigrations` / `python manage.py migrate`.
  - Create User: `python manage.py createsuperuser`.
- **Apps:** `apps/common`, `apps/users`, `apps/trabajos` (Portfolio works), `apps/ingresos`, `apps/gastos`.

## Frontend: `tdofrontend`
- **Path:** `./tdofrontend`
- **Tech Setup:**
  - React (Vite).
  - CSS: Plain CSS with variables (no Tailwind unless requested).
  - HTTP Client: `axios` configured in `src/context/customAxios.js`.
- **Core Commands:**
  - Start Dev Server: `npm run dev` (runs on port 5173).
  - Build: `npm run build`.
- **Key Features:**
  - `Home`: Landing page with services, recent works (`TrabajosRecientes`), products.
  - `Portafolio`: Full portfolio page with filtering.
  - `Users`: Login/Register (`AuthLayout`).

## Important Files
- **Backend Settings:** `tdobackend/core/settings.py`
- **Frontend Config:** `tdofrontend/vite.config.js` (Proxies requests to backend).
- **Documentation:** `Documentacion 1.0.2.txt` (Root).

## Design Guidelines (Dark Premium)
- **Colors:**
  - Background: Black (`#000000` or very dark gray `#121212`).
  - Text: White (`#ffffff`) or light gray (`#e0e0e0`).
  - Accents: Neon Blue, Purple, or Gold for buttons/highlights.
- **Typography:** Modern sans-serif (Inter, Roboto).
- **UI Elements:**
  - Cards: Dark semi-transparent backgrounds with subtle borders.
  - Interactions: Smooth hover effects (`transform: scale`, `box-shadow`).
  - Animations: Fade-ins, slide-ups.

## Common Workflows
1. **Start Development:**
   - Terminal 1 (Backend): `cd tdobackend`, activate venv, `python manage.py runserver`.
   - Terminal 2 (Frontend): `cd tdofrontend`, `npm run dev`.
2. **Database Changes:**
   - Modify models -> `makemigrations` -> `migrate`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pulguen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
