---
name: openevidence-webhooks-events
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Webhooks & Events

## Webhook Handler
```typescript
app.post('/webhooks/openevidence', (req, res) => {
  // Verify signature
  const event = req.body;
  console.log(`Event: ${event.type}`);
  res.status(200).send('OK');
});
```

## Resources
- [OpenEvidence Webhooks](https://www.openevidence.com)

## Next Steps
See `openevidence-performance-tuning`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
