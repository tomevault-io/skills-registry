---
name: qa-test-case-writer
description: Write and fix test cases in CSV format with summary tracking tables for QA purposes Use when this capability is needed.
metadata:
  author: droplinked
---

## What I do

- Write comprehensive test cases in CSV format following the existing test folder structure
- Fix and update existing test cases with proper formatting and coverage
- Include a summary table at the top of each file with: Feature, Test Case Count, Status, Writer, Last Sprint
- Ensure all test cases follow the standard format: ID, Scenario Title, Type, Precondition, Given, When, Then, Expected Result, Comment, Tester, Test Status
- Track test status across DEV and main branches

## Summary Table Format

At the top of every test case file, include this summary table:

```csv
Category,Table,Test Case Count,Status,Last Sprint Test,Writer
[Category Name],[Sub-feature],[Number],[Draft/In Progress/Completed],[Sprint XX],[QA Engineer Name]
```

Add a totals row at the end:
```csv
[Category Name] Subtotal,,[Total Count],,,,,
```

## Test Case Format

Each test case must include:
- **Test Case ID**: TC-[CATEGORY]-XXX format (e.g., TC-CART-CREATE-001)
- **Scenario Title**: Clear, descriptive title
- **Type**: Functional, Validation, UI, UI/UX, Edge Case, Performance, etc.
- **Precondition**: Setup required before test
- **Given**: Initial state/context
- **When**: Action performed
- **Then**: Expected outcome
- **Expected Result**: Detailed expected behavior
- **Comment - data**: Additional notes, test data examples
- **Tester**: Assigned tester name
- **Test Status (DEV)**: Current status on dev branch (Pass/Fail/Pending/Invalid)
- **Test Status (main)**: Current status on main branch
- **Last Sprint Test**: Sprint when tested
- **Front Unit test**: Unit test coverage status
- **Back Unit test**: Backend unit test coverage status
- **Front Integration**: Frontend integration test status
- **Api test**: API test status

## CSV Column Headers

```csv
Test Case ID,Scenario Title,Type,Precondition,Given,When,Then,Expected Result,Comment - data,Tester,Test Status (DEV),Test Status (main),Last Sprint Test,Front Unit test,Back Unit test,Front Integration,Api test
```

## How to Work

1. **Identify the Feature**: Ask or determine which feature needs test cases
2. **Check Existing Files**: Look for similar test cases in the test folder
3. **Write Summary Table**: Create the tracking table with all features and counts
4. **Write Test Cases**: Cover:
   - Happy path scenarios
   - Edge cases
   - Validation scenarios
   - Error handling
   - UI/UX verification
   - Performance/security (if applicable)
5. **Number Sequentially**: Use consistent ID numbering (TC-XXX-001, TC-XXX-002, etc.)
6. **Group by Sub-feature**: Use section headers like `✅ Feature Name` to organize
7. **Include All Metadata**: Status tracking, tester assignments, sprint info

## File Naming Convention

- `[Feature Area] - [Specific Feature].csv`
- Example: `Shop Builder - Product Management.csv`, `Shopfront - Cart System.csv`

## Output Template

```csv
Category,Table,Test Case Count,Status,Last Sprint Test,Writer
[Area],[Feature],[Count],[Status],[Sprint],[Name]
...

Feature,Test Case ID Range,,,,,
[Feature Name],[Range],,,,,

═══════════════════════════════════════════════════════════════════════════════════════════════════════
[SECTION TITLE]
═══════════════════════════════════════════════════════════════════════════════════════════════════════

✅ [Sub-feature Name],,,,,,
Test Case ID,Scenario Title,Type,Precondition,Given,When,Then,Expected Result,Comment - data,Tester,Test Status (DEV),Test Status (main),Last Sprint Test,Front Unit test,Back Unit test,Front Integration,Api test
TC-XXX-001,[Scenario],[Type],[Precondition],[Given],[When],[Then],[Expected],[Comment],[Tester],[Status],[Status],[Sprint],,,,
...
```

## Special Notes

- Use `✅` emoji for section headers to improve readability
- Use `═══════════════════════════════════════════════════════════════════════════════════════════════════════` as section dividers
- Mark test status as: `Pass ✅`, `Fail ❌`, `Pending ⏳`, `Invalid ⚠️`, `N/A`
- Include conflicts or notes in the Comment column
- Track last update date and feature ID when applicable

## Test Types to Include

1. **Functional**: Core feature behavior
2. **Validation**: Input validation, constraints
3. **UI**: Visual elements, interactions
4. **UI/UX**: User experience, responsive design
5. **Edge Case**: Boundary conditions, unusual scenarios
6. **Negative**: Error handling, invalid inputs
7. **Performance**: Load, stress, concurrent access

Always ask for clarification on:
- Which specific features/modules need test cases
- Target sprint for testing
- Assigned QA engineer name
- Any existing documentation or requirements to reference

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/droplinked) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
