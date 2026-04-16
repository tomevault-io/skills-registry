---
name: juicebox-security-basics
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Security Basics

## API Key Security
```bash
# .env (never commit)
JUICEBOX_API_KEY=jb_live_...
# .gitignore: .env
```

## Data Privacy
- Juicebox sources from public professional profiles
- Contact data requires explicit enrichment request
- Comply with GDPR/CCPA for candidate data storage
- Implement data retention policies

## Security Checklist
- [ ] API keys in environment variables
- [ ] Separate keys per environment
- [ ] Candidate data encrypted at rest
- [ ] GDPR consent for EU candidates
- [ ] Data retention policy documented

## Resources
- [Juicebox Privacy](https://juicebox.ai/privacy)
- [Data Sources](https://docs.juicebox.work/data-sources)

## Next Steps
See `juicebox-prod-checklist`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
