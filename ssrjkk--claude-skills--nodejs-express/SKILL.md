---
name: nodejs-express
description: Generates boilerplate for Express.js applications with TypeScript and middleware. Use for building Node.js APIs or web services.
metadata:
  author: ssrjkk
---
# Node.js Express

> Minimalist web framework for Node.js with TypeScript support.

## 🚀 Quick Start
```typescript
import express, { Request, Response } from 'express';

const app = express();
app.use(express.json());

app.get('/api/users', (req: Request, res: Response) => {
    res.json([{ id: 1, name: 'John' }]);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

## 📋 When to Use
- ✅ Building REST APIs on Node.js
- ✅ Need simple and flexible middleware setup
- ❌ Not for complex GraphQL servers (use Apollo)

## 🔧 Step-by-Step Instructions
1. Initialize: `npm init -y`
2. Install: `npm install express typescript @types/node`
3. Create `tsconfig.json` and server file
4. Run: `npx tsc && node dist/server.js`

## 📦 Dependencies
```bash
npm install express typescript @types/express @types/node ts-node
```

## 🧪 Examples
Input: `GET /api/users`
Output: `[{"id": 1, "name": "John"}]`

## 🔗 Resources
- [Express.js Documentation](https://expressjs.com)
- [Examples](./examples/)

## ✅ Validation
1. Server starts without compilation errors
2. Endpoints respond correctly
3. JSON parsing works properly

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
