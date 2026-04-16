---
name: juicebox-webhooks-events
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Webhooks & Events

## Webhook Setup
Configure at app.juicebox.ai > Settings > Webhooks.

## Event Handling
```typescript
app.post('/webhooks/juicebox', (req, res) => {
  const sig = req.headers['x-juicebox-signature'];
  if (!verifySignature(req.body, sig, WEBHOOK_SECRET)) return res.status(401).end();
  switch (req.body.type) {
    case 'outreach.replied': handleReply(req.body.data); break;
    case 'enrichment.complete': handleEnrichment(req.body.data); break;
  }
  res.status(200).send('OK');
});
```

## Events
| Event | Use |
|-------|-----|
| `outreach.replied` | Alert recruiter |
| `enrichment.complete` | Update record |
| `search.alert` | New candidate matches |

## Resources
- [Webhooks](https://docs.juicebox.work/webhooks)

## Next Steps
See `juicebox-performance-tuning`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
