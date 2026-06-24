---
name: preview-phase
description: Standard Operating Procedure for /preview phase. Covers manual UI/UX testing, user flow validation, and pre-deployment verification on local dev server. Use when this capability is needed.
metadata:
  author: oxie
---

# Preview Phase: Standard Operating Procedure

> **Training Guide**: Step-by-step procedures for executing the `/preview` command with thorough manual testing before deployment.

**Supporting references**:
- [reference.md](reference.md) - Manual testing checklist, user flow validation, visual regression detection
- [examples.md](examples.md) - Thorough preview (catches issues) vs rushed preview (misses issues)

---

## Phase Overview

**Purpose**: Manual UI/UX testing on local dev server to catch issues before deployment. Human validation gate before shipping.

**Inputs**:
- Implemented and optimized feature
- `specs/NNN-slug/spec.md` - Success criteria and user flows
- `specs/NNN-slug/optimization-report.md` - Automated test results

**Outputs**:
- `specs/NNN-slug/preview-checklist.md` - Manual test results
- `specs/NNN-slug/release-notes.md` - User-facing release notes
- Updated `workflow-state.yaml`

**Expected duration**: 30 minutes - 1 hour

---

## Prerequisites

**Environment checks**:
- [ ] Optimization phase completed
- [ ] Local dev server running (npm run dev or equivalent)
- [ ] Feature works in automated tests
- [ ] Browser available for testing (Chrome recommended)

**Knowledge requirements**:
- User flows from spec.md
- Success criteria to validate
- Manual testing techniques
- Accessibility testing basics

---

## Execution Steps

### Step 1: Start Local Dev Server

**Actions**:
```bash
# Kill any processes on ports (per CLAUDE.md)
npx kill-port 3000 3001 3002

# Clean npm cache
npm cache clean --force

# Start dev server
npm run dev

# Verify server running
curl http://localhost:3000
# Should return 200 OK
```

**Quality check**: Dev server running, feature accessible.

---

### Step 2: Test Happy Path

**Actions**:
1. Read user flows from spec.md
2. For each happy path, manually test:

**Example** (Student Progress Dashboard):
```markdown
## Happy Path: Teacher Views Student Progress

1. Navigate to http://localhost:3000/login
   ✓ Login page loads
   ✓ No console errors

2. Log in as teacher (test credentials)
   ✓ Login successful
   ✓ Redirects to dashboard

3. Navigate to student list
   ✓ Students displayed
   ✓ "View Progress" button visible

4. Click "View Progress" for a student
   ✓ Progress dashboard loads
   ✓ Displays: completion rate, time spent, last activity
   ✓ Chart renders correctly
   ✓ Data accurate (cross-check with database)

5. Filter by 7-day period
   ✓ Chart updates
   ✓ Metrics recalculate
   ✓ Loading state shown briefly
   ✓ No errors in console
```

**Quality check**: All happy paths work end-to-end with no errors.

---

### Step 3: Test Error Scenarios

**Actions**:
Test 2-3 error scenarios per feature:

**Example**:
```markdown
## Error Scenario 1: Invalid Student ID

1. Navigate to /students/999999/progress (non-existent ID)
   ✓ Shows "Student not found" message
   ✓ Error message user-friendly (not technical)
   ✓ Provides action: "Return to student list"
   ✓ No console errors (error handled gracefully)

## Error Scenario 2: Network Failure

1. Open Chrome DevTools → Network tab → Set to Offline
2. Try to load dashboard
   ✓ Shows "Unable to load data" message
   ✓ Provides retry button
   ✓ No infinite loading spinners
   ✓ Error logged to console (for debugging)

## Error Scenario 3: Invalid Input

1. Filter by invalid date range (end before start)
   ✓ Shows validation error
   ✓ Error message explains issue
   ✓ Form field highlighted in red
   ✓ Focus moved to invalid field
```

**Quality check**: Errors handled gracefully with user-friendly messages.

---

### Step 4: Test Responsive Design (if HAS_UI=true)

**Actions**:
Test on multiple screen sizes using browser dev tools:

**Example**:
```markdown
## Responsive Testing

**Desktop** (1920x1080):
✓ Dashboard displays full-width chart
✓ All controls visible
✓ No horizontal scrolling

**Tablet** (768x1024):
✓ Chart resizes appropriately
✓ Controls stack vertically
✓ Touch targets ≥44x44px
✓ No content cut off

**Mobile** (375x667):
✓ Chart readable (not cramped)
✓ Navigation accessible (hamburger menu)
✓ Text readable (font size ≥16px)
✓ No tiny tap targets
```

**Quality check**: Works on desktop, tablet, mobile without horizontal scroll or cut-off content.

---

### Step 5: Test Keyboard Navigation

**Actions**:
Test keyboard accessibility:

**Example**:
```markdown
## Keyboard Navigation

1. Tab through all interactive elements
   ✓ Tab order logical (top to bottom, left to right)
   ✓ All buttons/links reachable via Tab
   ✓ Focus indicators visible (blue outline)
   ✓ No keyboard traps (can Tab out of all elements)

2. Activate elements with Enter/Space
   ✓ Buttons activate on Enter or Space
   ✓ Links activate on Enter
   ✓ Dropdowns open with Enter, navigate with arrows

3. Test Escape key
   ✓ Modals close with Esc
   ✓ Dropdowns close with Esc
   ✓ Focus returns to trigger element

4. Test form navigation
   ✓ Tab moves between form fields
   ✓ Enter submits form (if expected)
   ✓ Can navigate back with Shift+Tab
```

**Quality check**: All functionality accessible via keyboard, no keyboard traps.

---

### Step 6: Visual Regression Check

**Actions**:
Compare new UI against design expectations:

**Example**:
```markdown
## Visual Regression Check

**Layout**:
✓ Matches design mockup (if exists)
✓ Spacing consistent (margins, padding)
✓ Alignment correct (left/center/right)
✓ No overlapping elements

**Typography**:
✓ Font sizes match design system
✓ Line heights readable
✓ Text not cut off

**Colors**:
✓ Matches brand colors
✓ Sufficient contrast (≥4.5:1 for text)
✓ Hover/focus states visible

**Images/Icons**:
✓ Images load correctly
✓ Icons aligned properly
✓ No broken image placeholders
✓ Alt text present (check in HTML)

**Animations**:
✓ Smooth (60fps)
✓ Not distracting
✓ Can be disabled (if motion-sensitive)
```

**Quality check**: UI matches design system, no visual regressions.

---

### Step 7: Test Performance in Browser

**Actions**:
Manual performance checks:

**Example**:
```markdown
## Performance Check

**Page Load**:
1. Open Chrome DevTools → Network tab → Hard refresh
   ✓ FCP <1.5s (measured in Performance tab)
   ✓ LCP <2.5s
   ✓ No render-blocking resources >500ms

**Interactions**:
1. Click button, time response
   ✓ Button click → UI update <100ms
   ✓ Form submit → feedback <500ms
   ✓ API call → data displayed <1s

**Smooth Scrolling**:
1. Scroll page with mouse wheel
   ✓ No jank (stuttering)
   ✓ 60fps (check in DevTools Performance)
   ✓ Scroll position maintained on back button
```

**Quality check**: Feels fast and responsive, no janky interactions.

---

### Step 8: Cross-Reference Success Criteria

**Actions**:
For each success criterion from spec.md, manually verify:

**Example**:
```markdown
## Success Criteria Validation

### From spec.md:

1. "User can complete registration in <3 minutes"
   - **Manual test**: Timed user flow
   - **Result**: 2.1 minutes ✓
   - **Notes**: Clear instructions, minimal friction

2. "Dashboard displays completion rate, time spent, last activity"
   - **Manual test**: Verified all 3 fields present
   - **Result**: All present ✓
   - **Notes**: Data accurate (cross-checked with DB)

3. "Lighthouse accessibility score ≥95"
   - **Already tested**: Automated (optimization phase)
   - **Manual verify**: Keyboard nav works ✓

4. "95% of user searches return results in <1 second"
   - **Manual test**: Searched 20 times, 19 were <1s
   - **Result**: 95% ✓
   - **Notes**: One slow search had 5000+ results (edge case)
```

**Quality check**: All success criteria verified through manual testing.

---

### Step 9: Document Issues Found

**Actions**:
If any issues found during preview:

**Example**:
```markdown
## Issues Found During Preview

### Issue 1: Chart not responsive on mobile
**Severity**: Medium
**Steps to reproduce**:
1. Open dashboard on mobile (375px width)
2. Chart overflows container
**Expected**: Chart resizes to fit
**Actual**: Horizontal scroll required
**Fix required**: Yes (blocking)

### Issue 2: Unclear error message
**Severity**: Low
**Steps to reproduce**:
1. Enter invalid date range
2. Error says "Invalid input"
**Expected**: "End date must be after start date"
**Actual**: Generic "Invalid input"
**Fix required**: Optional (nice-to-have)

### Issue 3: Focus indicator barely visible
**Severity**: Medium
**Steps to reproduce**:
1. Tab through elements
2. Focus outline very faint
**Expected**: Clear blue outline (2px solid)
**Actual**: Faint gray outline (1px)
**Fix required**: Yes (accessibility)
```

**Quality check**: All issues documented with severity and fix priority.

---

### Step 10: Generate Release Notes

**Actions**:
Create user-facing release notes:

**Example**:
```markdown
# Release Notes: Student Progress Dashboard

**Release Date**: 2025-10-21
**Version**: 1.3.0

## New Features

### Student Progress Dashboard
Teachers can now view detailed student progress including:
- Lesson completion rate (% of assigned lessons completed)
- Time spent on lessons (hours logged per lesson)
- Last activity date (to identify inactive students)
- Visual chart showing progress over time

**How to use**:
1. Navigate to Students page
2. Click "View Progress" next to student name
3. Filter by date range (7 days, 30 days, 90 days)

## Improvements
- Dashboard loads 80% faster (added caching)
- Mobile-friendly design (works on all screen sizes)
- Keyboard accessible (full keyboard navigation support)

## Bug Fixes
- None (new feature)

## Known Limitations
- Progress data updates every 10 minutes (not real-time)
- Maximum 1000 activities displayed per student

## Support
Questions? Contact support@example.com
```

**Quality check**: Release notes written in user-friendly language, no technical jargon.

---

### Step 11: Approve or Request Fixes

**Decision point**:

**If all checks pass and no blocking issues**:
- Mark preview as approved
- Proceed to deployment (`/ship`)

**If blocking issues found**:
- Document issues in preview-checklist.md
- Return to `/implement` or `/optimize` to fix
- Re-run `/preview` after fixes

**Example decision**:
```markdown
## Preview Decision

**Status**: Approved with minor issues

**Blocking issues**: 0
**Non-blocking issues**: 2 (low severity, defer to future)

**Recommendation**: Proceed to deployment

**Sign-off**: [Your name], [Date]
```

**Quality check**: Clear decision documented, blocking issues identified.

---

### Step 12: Commit Preview Results

**Actions**:
```bash
git add specs/NNN-slug/preview-checklist.md specs/NNN-slug/release-notes.md
git commit -m "docs: complete preview for <feature-name>

Preview testing complete:
- Happy paths: All pass ✓
- Error scenarios: 3 tested, all handled gracefully ✓
- Responsive: Works on desktop, tablet, mobile ✓
- Keyboard nav: Full keyboard access ✓
- Performance: Feels fast and responsive ✓

Issues found: 2 non-blocking (defer to future)
Decision: Approved for deployment

Ready for /ship
"
```

**Quality check**: Preview results committed, ready for deployment.

---

## Common Mistakes to Avoid

### 🚫 Incomplete User Flow Testing

**Impact**: Broken production UX, user complaints

**Scenario**:
```
Tested happy path only:
- User logs in ✓
- Views dashboard ✓
- Didn't test error scenarios

Production: User hits 404 error, sees stack trace (not handled)
```

**Prevention**:
- Test happy path + 2-3 error scenarios
- Test edge cases (empty states, max limits, invalid inputs)
- Don't skip error handling tests

---

### 🚫 Skipping Responsive Testing

**Impact**: Broken mobile experience

**Scenario**:
```
Tested on desktop only (1920px)
Mobile users (375px):
- Chart overflows screen
- Buttons too small to tap
- Text unreadable
```

**Prevention**:
- Test on 3 screen sizes minimum (desktop, tablet, mobile)
- Use browser dev tools responsive mode
- Verify touch targets ≥44x44px on mobile

---

### 🚫 No Keyboard Navigation Testing

**Impact**: Excludes keyboard users, accessibility failures

**Scenario**:
```
Tested with mouse only
Keyboard users cannot:
- Tab to all buttons
- Activate dropdowns
- Close modals
- Navigate forms
```

**Prevention**:
- Tab through entire feature
- Test Enter/Space/Esc keys
- Verify focus indicators visible
- No keyboard traps

---

### 🚫 Rushed Preview (Checkbox Exercise)

**Impact**: Misses real issues

**Bad preview**:
```
✓ Loads (didn't actually test flows)
✓ Looks good (didn't check on mobile)
✓ Works (didn't test error cases)
Time: 5 minutes
```

**Good preview**:
```
✓ Happy path: Teacher views student progress (tested step-by-step)
✓ Error: Invalid student ID shows user-friendly message (tested)
✓ Responsive: Works on 375px, 768px, 1920px (tested each)
✓ Keyboard: All features accessible via Tab/Enter (tested)
✓ Performance: Interactions feel instant (timed)
Time: 45 minutes
```

**Prevention**: Allocate adequate time (30-60 min), test thoroughly

---

## Best Practices

### ✅ Comprehensive Preview Checklist

**Use systematic checklist**:
```markdown
## Preview Checklist

**Happy Paths**:
- [ ] User flow 1: [describe] (all steps work)
- [ ] User flow 2: [describe] (all steps work)
- [ ] User flow 3: [describe] (all steps work)

**Error Handling**:
- [ ] Invalid input: Shows clear error message
- [ ] Network failure: Shows retry option
- [ ] Not found: Shows user-friendly 404

**Responsive**:
- [ ] Desktop (1920px): Layout correct, no scrolling
- [ ] Tablet (768px): Touch targets adequate
- [ ] Mobile (375px): Readable, no content cut off

**Accessibility**:
- [ ] Keyboard nav: All features accessible
- [ ] Focus indicators: Visible on all elements
- [ ] Screen reader: (optional, use if critical)

**Performance**:
- [ ] Page load: <3s
- [ ] Interactions: <500ms
- [ ] Scrolling: Smooth, no jank

**Visual**:
- [ ] Matches design system
- [ ] Colors/fonts consistent
- [ ] No visual regressions
```

**Result**: Systematic validation, nothing missed

---

### ✅ Document Issues with Screenshots

**For each issue, include**:
- Description
- Severity (blocking, high, medium, low)
- Steps to reproduce
- Screenshot or screen recording
- Expected vs actual

**Result**: Clear communication, easy to understand and fix

---

### ✅ User-Centric Release Notes

**Write for users, not developers**:

**Bad** (technical):
```
- Implemented GET /api/v1/students/{id}/progress endpoint
- Added StudentProgressService with calculateProgress() method
- Created React component ProgressDashboard
```

**Good** (user-friendly):
```
- View detailed student progress from Students page
- See completion rate, time spent, and last activity
- Filter progress by date range (7, 30, or 90 days)
```

**Result**: Users understand what changed and how to use it

---

## Phase Checklist

**Pre-phase checks**:
- [ ] Optimization phase completed
- [ ] Local dev server running
- [ ] Feature accessible in browser

**During phase**:
- [ ] Happy paths tested (all work)
- [ ] Error scenarios tested (2-3 scenarios)
- [ ] Responsive tested (desktop, tablet, mobile)
- [ ] Keyboard navigation tested (all accessible)
- [ ] Visual regression checked (matches design)
- [ ] Performance checked (feels fast)
- [ ] Success criteria validated (from spec.md)

**Post-phase validation**:
- [ ] preview-checklist.md created
- [ ] release-notes.md created
- [ ] Issues documented (if any)
- [ ] Decision made (approve or request fixes)
- [ ] Preview results committed
- [ ] workflow-state.yaml updated

---

## Quality Standards

**Preview quality targets**:
- Issues caught in preview: Track all
- Staging bugs from missed preview: <2 per feature
- Time spent: 30-60 minutes (adequate thoroughness)

**What makes good preview**:
- Thorough testing (happy + error paths)
- Multi-device testing (desktop + tablet + mobile)
- Accessibility tested (keyboard nav works)
- Issues documented (with screenshots)
- User-centric release notes

**What makes bad preview**:
- Rushed (5-minute checkbox exercise)
- Desktop-only testing (misses mobile issues)
- No error testing (breaks in production)
- No documentation (issues forgotten)
- Technical release notes (users confused)

---

## Completion Criteria

**Phase is complete when**:
- [ ] All checklist items tested
- [ ] Issues documented (if any)
- [ ] Decision made (approve or fix)
- [ ] Release notes written
- [ ] Preview results committed
- [ ] workflow-state.yaml shows `currentPhase: preview` and `status: completed`

**Ready to proceed to deployment** (`/ship`):
- [ ] No blocking issues
- [ ] All success criteria met
- [ ] Feature works on all devices
- [ ] Keyboard accessible

---

## Troubleshooting

**Issue**: Feature doesn't load on dev server
**Solution**: Check console errors, verify server running, restart dev server (npx kill-port 3000 && npm run dev)

**Issue**: Can't reproduce error scenario
**Solution**: Review spec.md for expected errors, check error handling code, use browser network throttling/offline mode

**Issue**: Layout broken on mobile
**Solution**: Use browser responsive mode, check for fixed widths, verify touch targets ≥44px, test on real device if possible

**Issue**: Keyboard navigation doesn't work
**Solution**: Check tab order, verify focus indicators, ensure interactive elements are focusable, test with Tab/Enter/Esc keys

---

_This SOP guides the preview phase. Refer to reference.md for testing checklists and examples.md for preview patterns._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
