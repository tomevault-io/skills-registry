---
name: openevidence-hello-world
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Hello World

## Overview
Minimal working examples demonstrating core OpenEvidence API functionality.

## Instructions

### Step 1: Clinical Query
```typescript
const result = await client.query({
  question: 'What is the recommended treatment for acute migraine in adults?',
  context: 'emergency_department',
  evidence_level: 'high',  // Filter by evidence quality
  max_citations: 10
});

console.log('Answer:', result.answer);
console.log('Confidence:', result.confidence);
result.citations.forEach(c =>
  console.log(`  [${c.journal}] ${c.title} (${c.year}) — ${c.evidence_level}`)
);
```

### Step 2: Drug Interaction Check
```typescript
const interactions = await client.interactions.check({
  medications: ['metformin', 'lisinopril', 'atorvastatin'],
  patient_context: { age: 65, conditions: ['diabetes', 'hypertension'] }
});

interactions.forEach(i =>
  console.log(`${i.drug1} + ${i.drug2}: ${i.severity} — ${i.description}`)
);
```

### Step 3: Guideline Lookup
```typescript
const guidelines = await client.guidelines.search({
  condition: 'hypertension',
  source: ['ACC/AHA', 'ESC'],
  year_min: 2023
});
guidelines.forEach(g =>
  console.log(`${g.source}: ${g.title} (${g.year})`)
);
```

## Error Handling
| Error | Cause | Solution |
|-------|-------|----------|
| Auth error | Invalid credentials | Check OPENEVIDENCE_API_KEY |
| Not found | Invalid endpoint | Verify API URL |
| Rate limit | Too many requests | Implement backoff |

## Resources
- [OpenEvidence API Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-local-dev-loop`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
