---
name: troubleshooting
description: name: troubleshooting Use when this capability is needed.
metadata:
  author: yi1jack0
---
---
name: troubleshooting
description: Diagnose and fix common Lunar Storyboard Web issues. Use for "Invalid token" errors, deployment failures, wallet initialization problems, health checks, or environment variable issues. Triggers on "error", "not working", "fix", "debug", "health check".
---

# Troubleshooting

## Health Checks

```bash
curl -s https://svc-commerce-918096488363.asia-southeast1.run.app/health
curl -s https://svc-media-918096488363.asia-southeast1.run.app/health
curl -s -o /dev/null -w "%{http_code}" https://gen-lang-client-0588247855.web.app
```

## "Invalid token: No Google Client IDs configured"

Backend needs `GOOGLE_CLIENT_ID` for token verification.

```bash
gcloud run services update svc-commerce --region=asia-southeast1 \
  --update-env-vars="GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com" \
  --project=dragon-horse-fly-high

gcloud run services update svc-media --region=asia-southeast1 \
  --update-env-vars="GOOGLE_CLIENT_ID=564440783008-38upbdbim2lv4ipq6la18mq0bfnotlg2.apps.googleusercontent.com" \
  --project=dragon-horse-fly-high
```

## Two Projects - Don't Confuse

| Purpose | Project ID | --project flag |
|---------|------------|----------------|
| Backend | `dragon-horse-fly-high` | `--project=dragon-horse-fly-high` |
| Frontend | `gen-lang-client-0588247855` | `--project gen-lang-client-0588247855` |

## Deployment Verification Failed

Check Firebase Console → Hosting → Release history. Look for `Deploy complete!` in output.

## Wrong Region

All Cloud Run services MUST use `--region=asia-southeast1`.

## Wallet Initialization Stuck

New users see perpetual loading.

1. Verify Pub/Sub topic `user-wallet-init` exists
2. Deploy Cloud Functions: `cd backend-microservices && pwsh deploy-functions.ps1`
3. Frontend should poll `/credits/status` and handle 202 responses

## Update Environment Variables

```bash
gcloud run services update SERVICE_NAME --region=asia-southeast1 \
  --update-env-vars="VAR1=value1,VAR2=value2" \
  --project=dragon-horse-fly-high
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yi1jack0) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
