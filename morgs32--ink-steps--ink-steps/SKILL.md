---
name: ink-steps
description: Build CLI steps with Ink using ProcedureStep and useProgram. Use when building step-based CLI UIs with Ink, ProcedureStep, ProcedureStepError, ProcedureStepLoading, ProcedureStepSuccess, ProcedureNextStep, or when choosing useProgram keys. Use when this capability is needed.
metadata:
  author: morgs32
---

# Step components (ProcedureStep, useProgram)

When building CLI steps with Ink, use ProcedureStep and useProgram as below.

## ProcedureStep: no conditional rendering

Do not branch on `status` and return different JSX. Render a single `<ProcedureStep status={status}>` and put the step pieces inside it. ProcedureStep (and its children: ProcedureStepError, ProcedureStepLoading, ProcedureStepSuccess, ProcedureNextStep) decide what to show from `status`. One return, one ProcedureStep.

```tsx
// Good: one ProcedureStep, status drives what's shown
return (
  <ProcedureStep status={status}>
    <ProcedureStepError error={error} />
    <ProcedureStepLoading message="..." />
    <ProcedureStepSuccess><Text>...</Text></ProcedureStepSuccess>
    <ProcedureNextStep>{data && children(data)}</ProcedureNextStep>
  </ProcedureStep>
);

// Bad: branching and multiple returns
if (status === 'loading') return <ProcedureStep status="loading">...</ProcedureStep>;
if (status === 'error') return ...;
return <>{children(data)}</>;
```

## useProgram key: one stable key

The `key` for `useProgram` should be a single, stable identifier for that step (e.g. the component or step name). It does not need to encode `repoPath`, `slug`, or other fetcher arguments. Use something like `'GetLocalSkills'`, not `getLocalSkills-${repoPath}-${slug}`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/morgs32) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
