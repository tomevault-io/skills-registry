---
name: pnpm-react-types-monorepo-mismatch
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# pnpm React Types Monorepo Mismatch

## Problem

TypeScript build fails with misleading errors like "'Card' cannot be used as a JSX component"
when different packages in a pnpm monorepo have different versions of `@types/react`.

## Context / Trigger Conditions

- **Error message**: `'X' cannot be used as a JSX component. Its type 'ForwardRefExoticComponent<...>' is not a valid JSX element type.`
- **Variant**: `Type 'ReactNode' is not assignable to type 'ReactNode'. Type 'ReactElement<unknown, string | JSXElementConstructor<any>>' is not assignable to type 'ReactNode'.`
- **When it happens**: After adding a new package to a monorepo, or when packages have divergent dependencies
- **Misleading aspect**: The error suggests the component itself is wrong, but the actual issue is type version mismatch

## Solution

### Step 1: Diagnose the version mismatch

```bash
pnpm ls @types/react --depth=0 -r
```

Look for different versions across packages, e.g.:
```
package-a: @types/react 18.3.27
package-b: @types/react 19.2.7
```

### Step 2: Add pnpm overrides to root package.json

```json
{
  "pnpm": {
    "overrides": {
      "@types/react": "^19.0.0",
      "@types/react-dom": "^19.0.0"
    }
  }
}
```

Use the version that matches your actual React version (React 18 → `@types/react@^18.0.0`, React 19 → `@types/react@^19.0.0`).

### Step 3: Reinstall dependencies

```bash
pnpm install
```

### Step 4: Verify the fix

```bash
pnpm ls @types/react --depth=0 -r
# All packages should now show the same version
pnpm build
```

## Verification

- `pnpm ls @types/react --depth=0 -r` shows identical versions across all packages
- Build completes without JSX component type errors

## Example

**Before** (in root `package.json`):
```json
{
  "pnpm": {
    "overrides": {
      "next": "^15.5.9"
    }
  }
}
```

**After**:
```json
{
  "pnpm": {
    "overrides": {
      "next": "^15.5.9",
      "@types/react": "^19.0.0",
      "@types/react-dom": "^19.0.0"
    }
  }
}
```

## Notes

- This commonly occurs when using Remotion (which pins older React types) alongside a Next.js app using React 19
- The same pattern applies to npm workspaces using `overrides` and yarn using `resolutions`
- Always match the types version to your actual React runtime version
- Consider adding a note in the monorepo README about this override for future maintainers

## References

- [pnpm overrides documentation](https://pnpm.io/package_json#pnpmoverrides)
- [TypeScript JSX element type errors](https://github.com/DefinitelyTyped/DefinitelyTyped/issues/65135)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
