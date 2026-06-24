---
name: test
description: Run tests for Terrae components Use when this capability is needed.
metadata:
  author: alamenai
---

# Test Skill

Run tests for Terrae components.

## Instructions

1. **Run All Tests**

   ```bash
   npm run test:run
   ```

   This runs vitest in single-run mode.

2. **Run Tests in Watch Mode**
   If the user wants to develop with live feedback:

   ```bash
   npm run test
   ```

   Note: This runs in watch mode and won't exit automatically.

3. **Run Specific Tests**
   To run tests for a specific component:

   ```bash
   npx vitest run {component-name}
   ```

   Example: `npx vitest run marker`

4. **Report Results**
   - Show passing/failing test counts
   - For failures, show:
     - Test name
     - Expected vs received values
     - File and line number

5. **Handle Failures**
   If tests fail:
   - Analyze the failure reason
   - Ask if the user wants help fixing the test or the code
   - Show the relevant test file and component code

6. **Test File Location**
   Tests are typically located at:
   - `src/registry/map/__tests__/{component}.test.tsx`
   - Or alongside components: `{component}.test.tsx`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alamenai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
