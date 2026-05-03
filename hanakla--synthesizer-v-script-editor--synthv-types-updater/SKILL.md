---
name: synthv-types-updater
description: Update TypeScript type definitions for Synthesizer V Studio Scripting API from official documentation. Use when the user requests updating type definitions from the official Synthesizer V documentation, adding new class definitions, or synchronizing types with the latest API documentation. Use when this capability is needed.
metadata:
  author: hanakla
---

# Synthesizer V Types Updater

Systematically update TypeScript type definitions for Synthesizer V Studio by extracting information from the official Japanese documentation and GitHub examples.

**Prerequisites**: Refer to CLAUDE.md for package structure, documentation source, and type definition guidelines.

## Workflow

Follow these steps in order. **Report progress to the user after completing each step.**

### Step 1: List All Classes from Official Documentation

Navigate to the official Synthesizer V Scripting API documentation:
https://resource.dreamtonics.com/scripting/ja/index.html

Use Chrome DevTools MCP to take a snapshot and extract the complete list of class names from the documentation index.

Create a TODO task for each class using TaskCreate.

Report the total number of classes found and created as TODOs.

### Step 2: Process Each Class Definition

For each class TODO:

1. Mark the task as in_progress using TaskUpdate
2. Navigate to the class's documentation page (e.g., `https://resource.dreamtonics.com/scripting/ja/<ClassName>.html`)
3. Use Chrome DevTools MCP `take_snapshot` to extract the full class documentation
4. Check if the class file exists in `pkgs/synthv-types/src/<ClassName>.ts`
5. If the file exists, read it and update it. If not, create a new file following the guidelines below
6. Mark the task as completed using TaskUpdate
7. Report which class was processed and whether it was created or updated

Follow the type definition guidelines in CLAUDE.md.

**Resolving `object` types:**
When the documentation shows a parameter or return type as `object`, do NOT use the generic `object` type. Instead:
1. Search the current class documentation for type details
2. Check related class definitions in `pkgs/synthv-types/src/`
3. Navigate to other documentation pages if referenced
4. Define the specific interface structure based on the documentation

**TSDoc format:**
- Use single-line format `/** コメント */` when the comment fits on one line
- Use multi-line format only when necessary (e.g., with `@param`, `@returns`, `@version` tags, or long descriptions)

Example class structure:

```typescript
import type { OtherClass } from './OtherClass'

/**
 * クラスの説明
 *
 * @version 2.1.0
 */
export class ClassName {
  /**
   * メソッドの説明
   *
   * @param paramName パラメータの説明
   * @returns 戻り値の説明
   * @version 2.1.2
   */
  methodName(paramName: string): ReturnType
}

/**
 * 複雑な戻り値の型定義
 */
export interface ReturnType {
  property: string
}
```

### Step 3: Process Tutorial Pages for Additional Types

1. Navigate to the tutorials menu on the official documentation
2. Locate and navigate to the following tutorial pages:
   - "カスタムサイドパネルセクション" (Custom Side Panel Section)
   - "カスタムダイアログ" (Custom Dialog)
3. Use Chrome DevTools MCP `take_snapshot` to extract content

#### Define SV.ClientInfo

From the "カスタムサイドパネルセクション" page, extract the return type of `getClientInfo()` and define it as `SV.ClientInfo` interface in `pkgs/synthv-types/src/extra/ClientInfo.ts`.

Report when SV.ClientInfo has been defined.

#### Define SV.Form

1. Navigate to: https://raw.githubusercontent.com/Dreamtonics/svstudio-scripts/refs/heads/master/Tests/TestCustomDialog.js
2. Use WebFetch or chrome devtools to retrieve the content
3. Analyze the custom dialog implementation example
4. Define `SV.Form` interface based on the form structure used in the example
5. Create the type definition in `pkgs/synthv-types/src/extra/Form.ts`

Report when SV.Form has been defined.

### Step 4: Verify and Export

1. Ensure all new classes are exported from `pkgs/synthv-types/src/index.ts` in alphabetical order
2. Run type check: `cd pkgs/synthv-types && yarn typecheck`
3. Review TaskList to confirm all tasks are completed

Report the final summary: total classes processed, type check result, and completion status.

## Important Notes

- Always preserve exact Japanese documentation text from official sources
- **Do NOT omit or summarize any part of the documentation**: Copy the complete description from official documentation. If you find existing TSDoc that is abbreviated or summarized, fix it to include the full documentation text.
- **@version tag**: Only add `@version` TSDoc tag when the official documentation explicitly mentions a version (e.g., "(2.1.2以降でサポート)"). Do NOT add version tags if no version is specified in the documentation.
- Follow the "one class per file" convention strictly
- Use Chrome DevTools MCP for all documentation extraction
- Process classes sequentially, updating TODO status as you go

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hanakla) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
