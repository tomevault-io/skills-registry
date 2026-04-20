---
name: development
description: description: Local development commands for Lunar Storyboard Web. Use when running frontend dev server, backend services locally, or executing tests. Triggers on "npm run dev", "run locally", "start server", "run tests", "pytest". Use when this capability is needed.
metadata:
  author: yi1jack0
---
---
name: development
description: Local development commands for Lunar Storyboard Web. Use when running frontend dev server, backend services locally, or executing tests. Triggers on "npm run dev", "run locally", "start server", "run tests", "pytest".
---

# Development

## Frontend (Next.js 16)

```bash
# From root (npm workspaces)
npm run dev       # localhost:3000
npm run build
npm run lint

# From frontend/
cd frontend && npm run dev
```

## Backend Services (FastAPI)

```bash
# svc-media (port 8080)
cd backend-microservices/svc-media
python -m uvicorn app:app --reload --port 8080

# svc-commerce (port 8081)
cd backend-microservices/svc-commerce
python -m uvicorn app:app --reload --port 8081

# svc-story (port 8082)
cd backend-microservices/svc-story
python -m uvicorn app:app --reload --port 8082
```

## Tests

```bash
# Frontend
cd frontend && npx tsx __tests__/apphosting.test.ts

# Backend
cd backend-microservices/svc-commerce && python -m pytest tests/
cd backend-microservices/svc-media && python -m pytest tests/
cd backend-microservices/shared && python -m pytest tests/
```

## Local Environment Variables

### svc-media
- `GROK_API_KEY` (xai-...)
- `GEMINI_API_KEY` (AIzaSy...)
- `SVC_COMMERCE_URL`, `SVC_STORY_URL`
- `GOOGLE_CLOUD_PROJECT=dragon-horse-fly-high`
- `GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com`

### svc-commerce
- `STRIPE_SECRET_KEY` (sk_test_... or sk_live_...)
- `STRIPE_WEBHOOK_SECRET` (whsec_...)
- `GOOGLE_CLOUD_PROJECT=dragon-horse-fly-high`
- `GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yi1jack0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
