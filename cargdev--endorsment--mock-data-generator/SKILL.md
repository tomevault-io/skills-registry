---
name: mock-data-generator
description: Generates robust mock datasets for papers, users, and endorsements so the frontend can function without a backend.
metadata:
  author: cargdev
---

# Mock Data Generator

When to use this skill

- Use early in development to simulate arXiv papers, users, and endorsement relations for UI and store integration.
- Triggered by requests to create JSON or TypeScript fixtures under `src/data/mockData.ts`.

Instructions

1. First Step: Produce realistic paper fixtures (title, authors, categories, abstract, submittedAt) and user fixtures (id, name, affiliation, expertiseAreas).

2. Second Step: Optionally seed persisted store on first load if no data exists, using the mock dataset to populate `usePaperStore` or similar.

3. Third Step: Provide utility functions to filter and paginate mock data to emulate real API behavior.

Examples

- `src/data/mockData.ts` exports `{ papers, users, endorsements }` and helper `seedMockData()`.

Notes

- Keep the dataset representative (vary categories, dates) so components (sorting, filtering) can be tested effectively.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
