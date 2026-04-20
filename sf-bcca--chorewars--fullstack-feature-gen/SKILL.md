---
name: fullstack-feature-gen
description: Generates a fullstack feature including backend service, route, test, and frontend page component with automatic route registration.
metadata:
  author: sf-bcca
---

# Fullstack Feature Generator

This skill automates the creation of a new "vertical slice" feature in the ChoreWars application. It scaffolds both backend (Fastify/Prisma) and frontend (React/Vite) components.

## Capabilities

- **Backend Service**: Creates `server/src/services/<name>Service.ts` with CRUD placeholders.
- **Backend Route**: Creates `server/src/routes/<name>.ts` and registers it in `server/src/app.ts`.
- **Backend Test**: Creates `server/test/<name>.test.ts`.
- **Frontend Page**: Creates `pages/<FeatureName>.tsx`.
- **Frontend Route**: Adds the new route to `App.tsx` (wrapped in `UserLayout` by default).

## Usage

Run the scaffold script with the PascalCase name of the feature.

```bash
node .gemini/skills/fullstack-feature-gen/scripts/scaffold_feature.js <FeatureName>
```

**Example:**
```bash
node .gemini/skills/fullstack-feature-gen/scripts/scaffold_feature.js GoalTracker
```

## Generated Files

| Component | Path | Description |
| :--- | :--- | :--- |
| **Service** | `server/src/services/<featureName>Service.ts` | Business logic and Prisma calls. |
| **Route** | `server/src/routes/<featureName>.ts` | API endpoints definition. |
| **Test** | `server/test/<featureName>.test.ts` | Basic unit/integration tests. |
| **Page** | `pages/<FeatureName>.tsx` | React page component. |

## Post-Execution Steps

After running the script, you may need to:

1.  **Define Prisma Model**: If this feature requires a new database table, add the model to `server/prisma/schema.prisma` and run `npx prisma migrate dev`.
2.  **Add Navigation**: Add a link to the new page in `components/Sidebar.tsx` or `components/BottomNav.tsx`.
3.  **Implement Logic**: Replace the placeholder CRUD logic in the generated service and page with actual requirements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sf-bcca) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
