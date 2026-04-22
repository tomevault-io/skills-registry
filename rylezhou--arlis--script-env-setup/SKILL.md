---
name: script-environment-setup
description: Best practices for loading .env files in standalone TypeScript scripts (e.g., verification scripts, migration tools) to avoid import hoisting issues. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Script Environment Setup (Best Practice)

When writing standalone scripts (e.g., `scripts/verify-foo.ts`) that import app modules (like `lib/db/supabase.ts`), you often encounter issues where `process.env` is undefined inside those modules.

## The Problem: Import Hoisting

TypeScript/JavaScript hoists static `import` statements to the top of the file. This means your app modules are evaluated **BEFORE** `dotenv.config()` runs, even if you put `dotenv` at the top of your code.

```typescript
// ❌ WRONG: Static imports run BEFORE dotenv
import dotenv from "dotenv";
dotenv.config(); // Too late!
import { supabase } from "../lib/db/supabase"; // Evaluated with empty process.env
```

## The Solution: Dynamic Imports

To ensure environment variables are loaded *before* your app code runs, use **Dynamic Imports** (`await import(...)`) for your app modules.

### Template

Use this template for all verification and utility scripts:

```typescript
import dotenv from "dotenv";
import path from "path";
import { fileURLToPath } from "url";

// 1. Setup __dirname (ESM compatibility)
const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// 2. Load Environment Variables
// Point explicitly to .env.local (or your target env file)
const envPath = path.resolve(__dirname, "../.env.local");
const result = dotenv.config({ path: envPath });

if (result.error) {
    console.warn(`[Setup] Warning: Failed to load env from ${envPath}`);
} else {
    console.log(`[Setup] Loaded env. Keys: ${Object.keys(result.parsed || {}).length}`);
}

// 3. Main Function with Dynamic Imports
async function main() {
    try {
        // Import app modules HERE, after env is loaded
        const { someService } = await import("../lib/some-service");
        
        // Your script logic
        console.log("Running script...");
        await someService.doSomething();
        
        process.exit(0);
    } catch (error) {
        console.error("Script failed:", error);
        process.exit(1);
    }
}

main();
```

## Checklist
1.  [ ] Import `dotenv` and `path` statically.
2.  [ ] Configure `dotenv` pointing to `.env.local`.
3.  [ ] Wrap main logic in an `async function`.
4.  [ ] Use `await import("../lib/...")` for **any** local code that depends on env vars.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
