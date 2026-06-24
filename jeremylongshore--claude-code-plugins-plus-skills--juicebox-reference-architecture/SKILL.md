---
name: juicebox-reference-architecture
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Reference Architecture

## Architecture
```
Recruiter UI → Search Service → Juicebox API
                    ↓
              Candidate Store → ATS (Greenhouse/Lever)
                    ↓
             Outreach Service → Juicebox Outreach
                    ↓
              Webhook Handler ← Juicebox Events
```

## Components
```typescript
class SearchService {
  async findCandidates(criteria) {
    const results = await juicebox.search(criteria);
    return results.profiles.map(p => this.score(p, criteria));
  }
}

class ATSSync {
  async push(candidates, jobId: string) {
    await juicebox.export({ profiles: candidates.map(c => c.id), destination: 'greenhouse', job_id: jobId });
  }
}
```

## Resources
- [Integrations](https://juicebox.ai/integrations)

## Next Steps
See `juicebox-multi-env-setup`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
