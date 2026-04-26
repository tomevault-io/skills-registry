---
name: code-generation
description: Scaffolding, boilerplate generation, and project templates. Use for generating components, modules, APIs, and project structures. Use when this capability is needed.
metadata:
  author: lovedragonball
---

# ⚡ Code Generation Skill

## Component Generators

### React Component
```javascript
// Template: React Functional Component
import { FC } from 'react';
import styles from './${name}.module.css';

interface ${Name}Props {
  // Props here
}

export const ${Name}: FC<${Name}Props> = ({ }) => {
  return (
    <div className={styles.container}>
      {/* Content */}
    </div>
  );
};
```

### API Route (Next.js)
```typescript
// Template: Next.js API Route
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  try {
    const data = await fetchData();
    return NextResponse.json({ data });
  } catch (error) {
    return NextResponse.json({ error: 'Failed' }, { status: 500 });
  }
}

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    const result = await createItem(body);
    return NextResponse.json({ data: result }, { status: 201 });
  } catch (error) {
    return NextResponse.json({ error: 'Failed' }, { status: 500 });
  }
}
```

---

## Project Scaffolding

### Vite React Project
```bash
npx create-vite@latest my-app --template react-ts
cd my-app
npm install
npm run dev
```

### Next.js Project
```bash
npx create-next-app@latest my-app --typescript --tailwind --eslint --app --src-dir
```

### Express API
```bash
mkdir my-api && cd my-api
npm init -y
npm install express cors helmet dotenv
npm install -D typescript @types/express @types/node ts-node nodemon
```

---

## File Structure Templates

### Feature-Based (Recommended)
```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api.ts
│   │   └── types.ts
│   └── users/
│       ├── components/
│       ├── hooks/
│       ├── api.ts
│       └── types.ts
├── shared/
│   ├── components/
│   ├── hooks/
│   └── utils/
└── app/
```

### Layer-Based
```
src/
├── components/
├── pages/
├── hooks/
├── services/
├── utils/
└── types/
```

---

## CRUD Generator Pattern

```typescript
// Generate CRUD for any entity
interface CRUDTemplate {
  entity: string;
  fields: Field[];
  endpoints: {
    list: boolean;
    get: boolean;
    create: boolean;
    update: boolean;
    delete: boolean;
  };
}

// Generates:
// - API routes (GET, POST, PUT, DELETE)
// - Types/Interfaces
// - React hooks (useGet, useCreate, useUpdate, useDelete)
// - Form component
// - List component
```

---

## Generation Checklist

- [ ] กำหนด folder structure
- [ ] สร้าง type definitions ก่อน
- [ ] Generate components จาก template
- [ ] เพิ่ม exports ใน index files
- [ ] Update routing ถ้าจำเป็น

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lovedragonball) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
