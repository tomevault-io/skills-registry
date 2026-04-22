---
name: generate-qa-test-cases
description: >- Use when this capability is needed.
metadata:
  author: michael0520
---

Analyze project code and generate comprehensive test cases for QA testing. Reports are written in Traditional Chinese.

## Arguments

- `$ARGUMENTS` - Target path to analyze (e.g., `libs/mxsecurity/account-page`)

## Analysis Process

### Step 1: Locate and Read Files

Read all relevant files from the target path:

- `**/*.ts` - TypeScript files (components, services, models)
- `**/*.html` - Template files
- `**/*.model.ts` - Data models
- `**/*.def.ts` - Constants and definitions
- `**/*.api.ts` - API service files

### Step 2: Extract Form Fields and Validation Rules

**From TypeScript files, extract:**

1. **Form Group Definitions** - `this.fb.group({...})`, `this.fb.nonNullable.group({...})`, `new FormGroup({...})`
2. **Form Controls with Validators** - `Validators.*`, `OneValidators.*`, custom validators
3. **Conditional Validators** - `setValidators()` / `clearValidators()` calls, dynamic validation logic

**Output Format:**

```markdown
## Form Fields and Validation Rules

### Form: {formName}

| Field Name | Type     | Required | Validators                                 | Conditions       |
| ---------- | -------- | -------- | ------------------------------------------ | ---------------- |
| username   | text     | Yes      | required, maxLength(32), pattern(alphanum) | Always           |
| password   | password | Yes      | required, minLength(8), complexity         | Create mode only |
```

### Step 3: Extract Error Messages

**From HTML templates, extract:**

1. **mat-error elements** - Error conditions (`*ngIf` / `@if`), translation keys
2. **Error message mappings** - `hasError('required')` to message, `hasError('minlength')` to message, custom error keys
3. **oneUiFormError directive usage** - Auto-generated error messages

**Output Format:**

```markdown
## Validation Error Messages

### Field: {fieldName}

| Validation Rule | Error Condition         | Error Message (Translation Key)     |
| --------------- | ----------------------- | ----------------------------------- |
| required        | `hasError('required')`  | `COMMON.VALIDATION.REQUIRED`        |
| minLength(8)    | `hasError('minlength')` | `COMMON.VALIDATION.MIN_LENGTH`      |
| pattern         | `hasError('pattern')`   | `ACCOUNT.VALIDATION.INVALID_FORMAT` |
```

### Step 4: Extract Conditional Field Visibility

**From templates and components, extract:**

1. **Conditional rendering** - `@if (condition)` / `*ngIf="condition"`, `[hidden]`, `[disabled]`
2. **Mode-based visibility** - Create vs Edit mode differences, role-based field visibility
3. **Dependent field logic** - Field A value affects Field B visibility, cascading selections

**Output Format:**

```markdown
## Conditional Field Visibility

### Trigger: {triggerField} = {value}

| Affected Field  | Visibility | Enabled | Notes                            |
| --------------- | ---------- | ------- | -------------------------------- |
| passwordField   | Hidden     | -       | Only shown in create mode        |
| confirmPassword | Shown      | Enabled | Appears when password is entered |
```

### Step 5: Extract API Payload Format

**From API service files, extract:**

1. **POST/PUT endpoints** - Method signatures, payload interfaces
2. **Request payload structure** - Required/optional fields, field types
3. **Data transformations** - Form data to API payload mapping

**Output Format:**

````markdown
## API Payload Format

### Endpoint: POST /auth/accountSet

#### Create Account (mode: 1)

```json
{
  "userName": "string (required)",
  "mode": 1,
  "userEnable": "number (0|1)",
  "authority": "number (1|2)",
  "new_pw": "string (required)",
  "confirm_pw": "string (required)"
}
```

#### Edit Account (mode: 2)

```json
{
  "userName": "string (required)",
  "mode": 2,
  "userEnable": "number (0|1)",
  "authority": "number (1|2)"
}
```
````

### Step 6: Generate Boundary Test Cases

Based on extracted validation rules, generate test cases:

1. **String Length Boundaries** - minLength: test with (min-1), min, (min+1); maxLength: test with (max-1), max, (max+1)
2. **Numeric Range Boundaries** - min: test with (min-1), min, (min+1); max: test with (max-1), max, (max+1)
3. **Pattern Validation** - Valid pattern examples, invalid pattern examples, edge cases (empty, special chars)
4. **Required Fields** - Empty string, whitespace only, null/undefined

**Output Format:**

```markdown
## Boundary Test Cases

### Field: username

| Test Case ID | Input Value | Expected Result | Validation Rule |
|--------------|-------------|-----------------|-----------------|
| TC-USER-001 | "" (empty) | Error: Required | required |
| TC-USER-002 | "   " (spaces) | Error: Required | required |
| TC-USER-003 | "a" (1 char) | Error: Min length | minLength(4) |
| TC-USER-004 | "abcd" (4 chars) | Valid | minLength(4) |
| TC-USER-005 | "a".repeat(32) | Valid | maxLength(32) |
| TC-USER-006 | "a".repeat(33) | Error: Max length | maxLength(32) |
| TC-USER-007 | "user@123" | Error: Invalid char | pattern(alphanum) |
| TC-USER-008 | "user_123" | Valid | pattern(alphanum) |
```

### Step 7: Generate Interaction Test Cases

Based on conditional visibility and mode logic:

1. **Mode Switching** - Create mode field states, edit mode field states, transitions
2. **Dependent Fields** - Trigger field changes, expected field state changes
3. **Form Submission** - Valid submission, partial validation errors, server error handling

**Output Format:**

```markdown
## Interaction Test Cases

### Scenario: Create New Account

| Step | Action                               | Expected Result                    |
| ---- | ------------------------------------ | ---------------------------------- |
| 1    | Click "Create" button                | Dialog opens with empty form       |
| 2    | Leave all fields empty, click Submit | Show required errors on all fields |
| 3    | Enter username "admin"               | Show "username exists" error       |
| 4    | Enter valid username                 | Error clears                       |
| 5    | Enter password "123"                 | Show "min length" error            |
| 6    | Enter password "ValidPass1!"         | Error clears                       |
| 7    | Enter mismatched confirm password    | Show "password mismatch" error     |
| 8    | Enter matching confirm password      | Error clears                       |
| 9    | Click Submit                         | Account created, dialog closes     |
```

## Output

Generate a comprehensive test case document at:
`{target}/domain/src/lib/docs/QA-TEST-CASES.md`

### Document Structure

```markdown
# QA Test Cases - {Feature Name}

Generated: {current_date}
Source: {target_path}

## Table of Contents

1. [Form Fields and Validation Rules](#form-fields-and-validation-rules)
2. [Validation Error Messages](#validation-error-messages)
3. [Conditional Field Visibility](#conditional-field-visibility)
4. [API Payload Format](#api-payload-format)
5. [Boundary Test Cases](#boundary-test-cases)
6. [Interaction Test Cases](#interaction-test-cases)

---

{Generated sections from Steps 2-7}
```

## Notes

- Reports are written in Traditional Chinese
- Focus on extracting actual validation rules from code, not assumptions
- Include translation keys for error messages to help QA verify correct text
- Cross-reference form field names with API payload field names
- Highlight any discrepancies between form validation and API expectations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michael0520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
