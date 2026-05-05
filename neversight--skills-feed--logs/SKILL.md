---
name: logs
description: View Vercel deployment logs. Use when the user says "show logs", "check logs", "vercel logs", or "what went wrong with the deployment". Use when this capability is needed.
metadata:
  author: neversight
---

# Vercel Logs

## List Deployments

```bash
vercel ls
```

## View Logs

```bash
vercel logs <deployment-url>
```

**Follow logs in real-time:**
```bash
vercel logs <deployment-url> --follow
```

## Analyze

- Look for errors or warnings
- Check for failed function invocations
- Identify build failures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
