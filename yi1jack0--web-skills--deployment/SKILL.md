---
name: deployment
description: description: Deploy Lunar Storyboard Web to production. Use when deploying frontend to Firebase Hosting, backend to Cloud Run, or setting production environment variables. Triggers on "deploy", "firebase deploy", "gcloud run", "push to prod". Use when this capability is needed.
metadata:
  author: yi1jack0
---
---
name: deployment
description: Deploy Lunar Storyboard Web to production. Use when deploying frontend to Firebase Hosting, backend to Cloud Run, or setting production environment variables. Triggers on "deploy", "firebase deploy", "gcloud run", "push to prod".
---

# Deployment

**Region: `asia-southeast1` (Singapore) - CRITICAL**

## Projects

| Purpose | Project ID | Number |
|---------|------------|--------|
| Backend | `dragon-horse-fly-high` | 918096488363 |
| Frontend | `gen-lang-client-0588247855` | 564440783008 |

## Frontend (Firebase Hosting)

```bash
firebase deploy --only hosting --project gen-lang-client-0588247855
```

Verify: Firebase Console → Hosting → Release history, or:
```bash
firebase hosting:channel:list --project gen-lang-client-0588247855
```

## Backend (Cloud Run)

```bash
# From backend-microservices/
pwsh deploy-media.ps1
pwsh deploy-commerce.ps1
```

### Set Environment Variables

```bash
gcloud run services update SERVICE_NAME --region=asia-southeast1 \
  --update-env-vars="VAR_NAME=value" \
  --project=dragon-horse-fly-high
```

### Production Environment Variables

**svc-commerce:**
```
GOOGLE_CLOUD_PROJECT=dragon-horse-fly-high
GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com
STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
```

**svc-media:**
```
GOOGLE_CLOUD_PROJECT=dragon-horse-fly-high
GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com
GROK_API_KEY=xai-...
GEMINI_API_KEY=AIzaSy...
SVC_COMMERCE_URL=https://svc-commerce-918096488363.asia-southeast1.run.app
SVC_STORY_URL=https://svc-story-918096488363.asia-southeast1.run.app
```

## Pub/Sub Setup

Topic `user-wallet-init` required for async wallet creation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yi1jack0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
