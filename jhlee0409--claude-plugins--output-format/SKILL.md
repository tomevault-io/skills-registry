---
name: output-format
description: Beautiful ASCII art output formatting guidelines for all plugin commands Use when this capability is needed.
metadata:
  author: jhlee0409
---

# Output Format Guide

All plugin outputs should be visually clear and beautiful. Choose the format that best fits the content.

---

## EXECUTION INSTRUCTIONS

When generating output for any OAS plugin command, Claude MUST:

1. **Select appropriate format** based on content type (see Format Selection below)
2. **Use consistent styling** within a single output
3. **Keep width under 80 characters** for terminal compatibility
4. **Use emojis sparingly** - only for status indicators
5. **Add blank lines** between sections for readability

This is a REFERENCE skill - use it to format outputs from other commands.

---

## Format Selection

```
Content Type           → Best Format
─────────────────────────────────────
Key-Value pairs        → Table or Aligned
Process/Steps          → Flow Diagram
Hierarchy/Structure    → Tree
Before/After           → Comparison
Status/Progress        → Progress Bar
Summary                → Box
List with status       → Checklist
Timeline               → Timeline
```

---

## 1. Tables (Structured Data)

For structured key-value or multi-column data:

```
┌──────────────┬─────────────────────────────────────┐
│     Item     │              Content                │
├──────────────┼─────────────────────────────────────┤
│ File         │ src/api/user-api.ts                 │
│ Location     │ getUserById() function (line 45-52) │
│ Reason       │ Response type: email field added    │
└──────────────┴─────────────────────────────────────┘
```

Simple aligned format (for fewer columns):

```
File:     src/api/user-api.ts
Location: getUserById() function (line 45-52)
Reason:   Response type: email field added
```

---

## 2. Flow Diagrams (Process/Steps)

Horizontal flow:

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│  Fetch  │───▶│  Parse  │───▶│  Diff   │───▶│ Generate│
│  Spec   │    │  Schema │    │ Compare │    │  Code   │
└─────────┘    └─────────┘    └─────────┘    └─────────┘
```

Vertical flow with details:

```
    ┌──────────────────────────────┐
    │   1. Fetch OpenAPI Spec      │
    │      GET /openapi.json       │
    └──────────────┬───────────────┘
                   │
                   ▼
    ┌──────────────────────────────┐
    │   2. Compare with Cache      │
    │      Hash: abc123 → def456   │
    └──────────────┬───────────────┘
                   │
          ┌───────┴───────┐
          ▼               ▼
    ┌──────────┐    ┌──────────┐
    │ Changed  │    │ No Change│
    │ Continue │    │   Done   │
    └────┬─────┘    └──────────┘
         │
         ▼
    ┌──────────────────────────────┐
    │   3. Generate Code           │
    └──────────────────────────────┘
```

Decision flow:

```
                    ┌─────────────┐
                    │ Cache exists?│
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              ▼            │            ▼
           [ Yes ]         │         [ No ]
              │            │            │
              ▼            │            ▼
    ┌─────────────────┐    │    ┌─────────────────┐
    │  Compare hash   │    │    │ Full fetch      │
    └────────┬────────┘    │    └────────┬────────┘
             │             │             │
             └─────────────┴─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   Output    │
                    └─────────────┘
```

---

## 3. Tree Structure (Hierarchy)

File tree:

```
src/entities/user/
├── api/
│   ├── user-api.ts         ← 8 functions
│   ├── user-api-paths.ts   ← 8 paths
│   └── user-queries.ts     ← 8 hooks
├── model/
│   └── user-types.ts       ← 12 types
└── index.ts
```

Nested structure with status:

```
📦 API Coverage
│
├── ✅ user (100%)
│   ├── getUser
│   ├── updateUser
│   └── deleteUser
│
├── ⚠️ workspace (75%)
│   ├── ✅ getWorkspace
│   ├── ✅ createWorkspace
│   ├── ❌ inviteUser         ← missing
│   └── ✅ deleteWorkspace
│
└── ❌ billing (0%)
    ├── ❌ getPlans
    ├── ❌ subscribe
    └── ❌ cancelSubscription
```

---

## 4. Comparison (Diff)

Side-by-side diff:

```
┌─────────────────────────────┬─────────────────────────────┐
│          Before             │           After             │
├─────────────────────────────┼─────────────────────────────┤
│ interface User {            │ interface User {            │
│   id: string;               │   id: string;               │
│   name: string;             │   name: string;             │
│                             │ + email: string;            │
│                             │ + avatar?: string;          │
│ }                           │ }                           │
└─────────────────────────────┴─────────────────────────────┘
```

Inline diff:

```
GET /users/{id}
Response: UserResponse
  + email: string        (added)
  + avatar?: string      (added, optional)
  ~ name: string → string | null  (changed to nullable)
  - legacy_id: number    (removed)
```

---

## 5. Progress & Status

Progress bar:

```
Syncing API endpoints...

user      ████████████████████ 100%  ✅
workspace ████████████░░░░░░░░  60%  ⏳
billing   ░░░░░░░░░░░░░░░░░░░░   0%  ⏸️

Overall: ██████████████░░░░░░ 70% (21/30 endpoints)
```

Status summary:

```
╔═══════════════════════════════════════════════╗
║              Sync Complete                    ║
╠═══════════════════════════════════════════════╣
║  ✅ Generated:  24 files                      ║
║  ⚠️ Warnings:   3 (nullable handling)         ║
║  ❌ Errors:     0                             ║
║  ⏱️ Duration:   2.3s                          ║
╚═══════════════════════════════════════════════╝
```

---

## 6. Summary Box

Simple header:

```
═══════════════════════════════════════════════════
  📊 API Diff Report
═══════════════════════════════════════════════════
```

Info box:

```
┌─────────────────────────────────────────────────┐
│ 💡 TIP                                          │
│                                                 │
│ Use --force to regenerate all endpoints,       │
│ ignoring cache.                                 │
└─────────────────────────────────────────────────┘
```

Warning box:

```
⚠️ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   Breaking Changes Detected!

   3 endpoints removed:
   - DELETE /users/{id}/sessions
   - GET /legacy/auth
   - POST /v1/login
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 7. Checklist

Task list:

```
📋 Sync Checklist

[✓] Fetch OpenAPI spec
[✓] Parse endpoints (42 found)
[✓] Compare with existing code
[ ] Generate user-api.ts
[ ] Generate workspace-api.ts
[ ] Update type definitions
```

Validation result:

```
🔍 Validation Results

✅ PASS  Type safety          All types match spec
✅ PASS  Path coverage        42/42 endpoints covered
⚠️ WARN  Nullable handling    3 fields need attention
❌ FAIL  Response format      getUserById() returns wrong type
❌ FAIL  Missing endpoint     POST /users not implemented
```

---

## 8. Timeline

```
📅 Sync History

  Jan 13, 10:30  ●───── Full sync (42 endpoints)
                 │
  Jan 12, 15:22  ●───── Partial sync: +2 workspace endpoints
                 │
  Jan 10, 09:15  ●───── Initial setup
                 │
  Jan 08, 14:00  ○───── Failed: spec unreachable
```

---

## 9. Impact Analysis

```
═══════════════════════════════════════════════════════════════
  🔍 Impact Analysis Report
═══════════════════════════════════════════════════════════════

Spec Change:  GET /users/{id} response updated
              + email: string
              + preferences: UserPreferences

┌─────────────────────────────────────────────────────────────┐
│  Affected Files                                             │
├───────────────────────────────┬─────────────────────────────┤
│  File                         │  Required Changes           │
├───────────────────────────────┼─────────────────────────────┤
│  src/api/user-api.ts:45       │  Update return type         │
│  src/types/user.ts:12         │  Add email, preferences     │
│  src/hooks/useUser.ts:23      │  No change (auto-inferred)  │
└───────────────────────────────┴─────────────────────────────┘

Code Changes Required:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  📁 src/types/user.ts

  interface User {
    id: string;
    name: string;
  + email: string;           // ← Add
  + preferences: UserPreferences;  // ← Add
  }

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Steps:
  1. Run /oas:sync --tag=user to auto-generate
  2. Or manually update the files above
```

---

## Character Reference

```
Box Drawing:
  ┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼ │ ─
  ╔ ╗ ╚ ╝ ╠ ╣ ╦ ╩ ╬ ║ ═

Arrows:
  → ← ↑ ↓ ↔ ↕
  ▶ ◀ ▲ ▼
  ───▶  ◀───  │
               ▼

Progress:
  █ ░ ▓ ▒

Status Icons:
  ✅ ❌ ⚠️ ⏳ ⏸️ 💡 📦 📋 🔍 📊 📁 🔌 📝 🏷️

Bullets:
  • ○ ● ◦ ▪ ▫

Lines:
  ─ ━ ═ ┄ ┅ ┈ ┉
```

---

## Best Practices

1. **Consistency**: Use same style within one output
2. **Breathing room**: Add blank lines between sections
3. **Alignment**: Align columns for readability
4. **Icons sparingly**: Don't overuse emojis
5. **Width**: Keep under 80 chars for terminal compatibility
6. **Context-appropriate**: Tables for data, flows for processes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jhlee0409) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
