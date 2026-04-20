---
name: functional-test
description: Systematic functional testing of all dashboard routes in the browser, logging bugs to a report file. Use when this capability is needed.
metadata:
  author: michal-j-kolodziej
---

# Dashboard Functional Testing

A systematic process for testing all dashboard routes and interactive elements in the browser, documenting bugs and unexpected behaviors for later fixing.

## When to Use This Skill

- After implementing new features or major changes
- Before a release or milestone
- When the user reports "something is broken" without specifics
- For regression testing after refactoring

## Prerequisites

1. **App Running**: Development server running (`npm run dev`)
2. **Authenticated**: Have login credentials ready or ability to register
3. **Browser Tool**: You will use `browser_subagent` to test the app

---

## Phase 1: Setup

**Goal:** Prepare environment and create bug report file.

1. **Check server**: Navigate to `http://localhost:5173`. If error, run:

   ```bash
   cd /Users/michal/Documents/MyApps/WalkWithMe && npm run dev
   ```

2. **Login or Register**:
   - Use existing account OR register new (e.g., `tester@walkwithme.test`)
   - ⚠️ If login/register fails → log as Bug #1, STOP

3. **Complete profile** if required to access dashboard

4. **Create bug report** at project root: `DASHBOARD_BUGS.md`

   ```markdown
   # Dashboard Bug Report

   > Generated: [current date]
   > Tester: AI Agent

   ---
   ```

---

## Phase 2: Route Testing

**Goal:** Test each route systematically, logging bugs immediately.

### Test Workflow (per route)

1. Navigate to route
2. Check browser console for errors
3. Run tests from table below
4. Log bugs immediately when found
5. Continue to next route

### Stop Conditions

- ✅ All 10 routes tested → write summary, finish
- 🛑 Critical blocker → write summary, finish
- ⚠️ 10+ bugs found → write summary, pause for fixes

---

### Route 1: Dashboard Home (`/dashboard`)

| Test            | Pass Criteria                              |
| --------------- | ------------------------------------------ |
| Page loads      | No crash, no blank screen                  |
| Welcome message | Shows user's name                          |
| Stat cards      | 4 cards render (Dogs, Walks, Time, Events) |
| Dogs section    | Shows dogs OR "add your first dog"         |
| Sidebar nav     | Each link navigates correctly              |
| Weather         | Data loads OR graceful error message       |

---

### Route 2: Dogs (`/dashboard/dogs`)

| Test           | Pass Criteria                                      |
| -------------- | -------------------------------------------------- |
| Page loads     | Dog list OR empty state                            |
| **Add dog**    | Button → form → fill name/breed → submit → appears |
| **Edit dog**   | Edit button → prefills → change → save → updated   |
| **Delete dog** | Delete → confirm → removed                         |
| Validation     | Empty submit → error messages                      |

> **Note**: Create a dog first if none exist to test edit/delete.

---

### Route 3: Profile (`/dashboard/profile`)

| Test         | Pass Criteria                  |
| ------------ | ------------------------------ |
| Page loads   | No crash                       |
| User info    | Name, email, role visible      |
| Dogs section | Count/previews OR "no dogs"    |
| Manage link  | Navigates to `/dashboard/dogs` |

---

### Route 4: Settings (`/dashboard/settings`)

| Test            | Pass Criteria                                  |
| --------------- | ---------------------------------------------- |
| Form loads      | Fields have current values                     |
| **Edit name**   | Change → save → feedback → refresh → persisted |
| Language toggle | PL↔EN → UI text changes                        |
| Email field     | Read-only (not editable)                       |

---

### Route 5: Friends (`/dashboard/friends`)

| Test          | Pass Criteria                 |
| ------------- | ----------------------------- |
| Tabs visible  | "Friends", "Received", "Sent" |
| Tab switching | Click each → content changes  |
| Empty state   | Shows message, not blank      |

> **Note**: Mark accept/reject/unfriend as SKIPPED if no data.

---

### Route 6: Discover (`/dashboard/discover`)

| Test       | Pass Criteria                     |
| ---------- | --------------------------------- |
| Page loads | User cards OR "no users"          |
| Search     | Type → list filters               |
| Add friend | Button → modal with message field |

---

### Route 7: Chat (`/dashboard/chat`)

| Test         | Pass Criteria                     |
| ------------ | --------------------------------- |
| Page loads   | Conversation list OR empty state  |
| Open chat    | Click conversation → thread opens |
| Send message | Type → send → appears in thread   |

> **Note**: Mark as SKIPPED if no conversations exist.

---

### Route 8: Meetings (`/dashboard/meetings`)

| Test               | Pass Criteria                           |
| ------------------ | --------------------------------------- |
| Tabs visible       | "Upcoming", "Past", "Invitations"       |
| **Create meeting** | Button → modal → fill → submit → listed |
| View details       | Click meeting → details page            |
| Location map       | Map renders (not blank)                 |

---

### Route 9: Map (`/dashboard/map`)

| Test      | Pass Criteria                          |
| --------- | -------------------------------------- |
| Map loads | Tiles render, not blank                |
| Pan/zoom  | Can interact                           |
| Location  | Permission prompt OR location OR error |

---

### Route 10: Walks (`/dashboard/walks`)

| Test       | Pass Criteria          |
| ---------- | ---------------------- |
| Page loads | History OR empty state |
| Start walk | Button → tracking UI   |
| End walk   | End → summary          |

---

## Phase 3: Bug Reporting

**Goal:** Document bugs in a consistent format.

### Bug Entry Format

Append each bug to `DASHBOARD_BUGS.md`:

```markdown
## Bug #[N]: [Short title]

**Route**: `/dashboard/[page]`  
**Severity**: Critical | High | Medium | Low  
**Issue**: [1-2 sentences]  
**Expected**: [What should happen]  
**Console**: [Error or "None"]  
**Fix hint**: [Suggestion for fixing]
```

### Severity Scale

| Level        | Description                     |
| ------------ | ------------------------------- |
| **Critical** | Crash, data loss, can't proceed |
| **High**     | Feature broken, can work around |
| **Medium**   | Works but poor UX               |
| **Low**      | Cosmetic, typo                  |

---

## Phase 4: Summary Report

**Goal:** Summarize findings at the top of the report.

After testing, **prepend** to `DASHBOARD_BUGS.md`:

```markdown
## Test Summary

| Metric        | Value                         |
| ------------- | ----------------------------- |
| Routes Tested | X/10                          |
| Critical      | X                             |
| High          | X                             |
| Medium        | X                             |
| Low           | X                             |
| Skipped       | X                             |
| Status        | Stable / Needs Fixes / Broken |

### Priority Fixes

1. Bug #X - [reason]
2. Bug #X - [reason]
3. Bug #X - [reason]

---
```

---

## Scope Variants

### `full` (default)

Test all 10 routes with all tests.

### `quick`

Test only routes 1-4 (Dashboard, Dogs, Profile, Settings) - core functionality.

### `[route-name]`

Test a specific route only (e.g., `meetings`, `chat`).

---

## Out of Scope

Do NOT test:

- Real-time sync between two users
- Email notifications sending
- External API failures (weather down)
- Pixel-perfect visuals (use `visual-audit` skill)
- Mobile responsiveness (desktop only)

---

## Quick Route Reference

| #   | Route                 | Key Tests      |
| --- | --------------------- | -------------- |
| 1   | `/dashboard`          | Stats, sidebar |
| 2   | `/dashboard/dogs`     | CRUD           |
| 3   | `/dashboard/profile`  | Info display   |
| 4   | `/dashboard/settings` | Edit profile   |
| 5   | `/dashboard/friends`  | Tabs           |
| 6   | `/dashboard/discover` | Search, add    |
| 7   | `/dashboard/chat`     | Messages       |
| 8   | `/dashboard/meetings` | Create flow    |
| 9   | `/dashboard/map`      | Map loads      |
| 10  | `/dashboard/walks`    | Start/end      |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michal-j-kolodziej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
