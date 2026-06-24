---
name: deploy
description: Deploy to preview or production and verify bot connectivity Use when this capability is needed.
metadata:
  author: api-haus
---

Deploy the bot and verify it's working correctly.

## Usage

```
/deploy [preview|prod]
```

If environment not specified, ask the user which environment to deploy to.

## Process

### 1. Deploy

Run the appropriate deploy command:
- **preview**: `pnpm preview:deploy`
- **prod**: `pnpm run deploy`

### 2. Verify Webhook

Check webhook status using the `/webhook-info` endpoint:
```bash
# Preview
curl -s https://nanoclawnana-preview.yura415.workers.dev/webhook-info | jq .

# Production
curl -s https://nanoclawnana.yura415.workers.dev/webhook-info | jq .
```

Verify:
- `_url_matches: true`
- No `last_error_message`
- `pending_update_count` is reasonable

If issues, re-register with `/setup` endpoint.

### 3. Run Sanity Tests

Run minimal sanity tests against the deployed environment:
```bash
# Preview
pnpm test:e2e:sanity:preview

# Production
pnpm test:e2e:sanity:prod
```

These are fast (~3 tests) and verify basic bot responsiveness.

### 4. User Confirmation

After E2E tests pass, ask the user to manually verify the bot works:

> **Please verify the bot is responding:**
> 1. Send a message to the bot
> 2. Confirm you received a response
>
> Does the bot work correctly?

Wait for user confirmation before considering deployment complete.

## URLs

| Environment | Worker URL |
|-------------|------------|
| preview | `https://nanoclawnana-preview.yura415.workers.dev` |
| prod | `https://nanoclawnana.yura415.workers.dev` |

## Troubleshooting

If the bot is unresponsive after deployment:

1. **Check webhook status**: `curl <worker-url>/webhook-info | jq .`
2. **Check logs**: `pnpm preview:tail` or `pnpm tail`
3. **Re-register webhook**: `curl <worker-url>/setup | jq .`
4. **Check for runtime errors** in the tail output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/api-haus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
