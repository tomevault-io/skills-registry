---
name: add-test
description: Add a new HTTP/1.1 compliance, smuggling, malformed input, or normalization test to Http11Probe. Handles code, docs, dashboard, and table wiring. Use when this capability is needed.
metadata:
  author: mda2av
---

# Add a New Test to Http11Probe

The user wants to add a new test. Gather the following from them (or from context):

1. **What RFC violation or behavior** the test checks
2. **Which category**: Compliance, Smuggling, MalformedInput, or Normalization
3. **The raw HTTP request** to send (or enough detail to construct it)
4. **Expected outcome**: what status code / behavior means pass, warn, or fail
5. **RFC reference** (if applicable): section number and the exact quote with the MUST/SHOULD/MAY keyword

Then perform **all 6 steps below**, in order.

---

## Step 1 — Add the test case to the suite file

Choose the correct file:

| Category | File |
|----------|------|
| Compliance | `src/Http11Probe/TestCases/Suites/ComplianceSuite.cs` |
| Smuggling | `src/Http11Probe/TestCases/Suites/SmugglingSuite.cs` |
| MalformedInput | `src/Http11Probe/TestCases/Suites/MalformedInputSuite.cs` |
| Normalization | `src/Http11Probe/TestCases/Suites/NormalizationSuite.cs` |

Read the file first to understand existing patterns and find the right insertion point.

Append a `yield return new TestCase { ... };` inside `GetTestCases()`:

```csharp
yield return new TestCase
{
    Id = "PREFIX-NAME",
    Description = "What this test checks",
    Category = TestCategory.XXX,
    RfcReference = "RFC 9112 §X.X",          // Use § not "Section". Omit if N/A.
    RfcLevel = RfcLevel.Must,                 // Must|Should|May|OughtTo|NotApplicable
    Scored = true,                            // false for MAY/informational tests
    PayloadFactory = ctx => MakeRequest(
        $"GET / HTTP/1.1\r\nHost: {ctx.HostHeader}\r\n\r\n"
    ),
    Expected = new ExpectedBehavior
    {
        ExpectedStatus = StatusCodeRange.Exact(400),
    },
};
```

**ID prefix conventions:** `COMP-`, `SMUG-`, `MAL-`, `NORM-`, or `RFC9112-X.X-` / `RFC9110-X.X-`

**Validation rules:**
- `Exact(400)` for MUST-400 requirements — NEVER add `AllowConnectionClose = true` for these
- `AllowConnectionClose = true` only when connection close is a valid alternative to a status code
- Use `CustomValidator` for pass/warn/fail logic — always check response BEFORE connection state:
  ```csharp
  CustomValidator = (response, state) =>
  {
      if (response is not null)
          return response.StatusCode == 400 ? TestVerdict.Pass : TestVerdict.Fail;
      if (state is ConnectionState.ClosedByServer or ConnectionState.TimedOut)
          return TestVerdict.Pass;
      return TestVerdict.Fail;
  }
  ```
- `RfcLevel` must match the RFC 2119 keyword. Default is `Must` — only set explicitly for non-Must.
- `Scored = false` only for MAY-level or purely informational tests
- Always use `ctx.HostHeader` in payloads, never hardcoded hosts

**Available helpers:**
- `StatusCodeRange.Exact(int)`, `.Range(int,int)`, `.Range2xx`, `.Range4xx`, `.Range4xxOr5xx`
- `TestVerdict.Pass`, `.Fail`, `.Warn`, `.Skip`, `.Error`
- `ConnectionState.Open`, `.ClosedByServer`, `.TimedOut`, `.Error`

## Step 2 — Add docs URL mapping (if needed)

**File:** `src/Http11Probe.Cli/Reporting/DocsUrlMap.cs`

Only needed for `COMP-*` and `RFC*` prefixed tests. These prefixes are auto-mapped:
- `SMUG-XYZ` → `smuggling/xyz`
- `MAL-XYZ` → `malformed-input/xyz`
- `NORM-XYZ` → `normalization/xyz`

For compliance tests, add to `ComplianceSlugs`:
```csharp
["COMP-EXAMPLE"] = "headers/example",
```

## Step 3 — Create the documentation page

**File:** `docs/content/docs/{category-slug}/{test-slug}.md`

Category slug mapping: `line-endings`, `request-line`, `headers`, `host-header`, `content-length`, `body`, `upgrade`, `smuggling`, `malformed-input`, `normalization`

Use this template:

```markdown
---
title: "TEST NAME"
description: "Test documentation"
weight: 1
---

| | |
|---|---|
| **Test ID** | `PREFIX-NAME` |
| **Category** | Category |
| **RFC** | [RFC 9112 §X.X](https://www.rfc-editor.org/rfc/rfc9112#section-X.X) |
| **Requirement** | MUST |
| **Expected** | `400` or close |

## What it sends

Description of the non-conforming request.

\```http
GET / HTTP/1.1\r\n
Host: localhost:8080\r\n
\r\n
\```

## What the RFC says

> "Exact quote from the RFC." -- RFC 9112 Section X.X

Explanation.

## Why it matters

Security and compatibility implications.

## Sources

- [RFC 9112 §X.X](https://www.rfc-editor.org/rfc/rfc9112#section-X.X)
```

## Step 4 — Add a card to the category index

**File:** `docs/content/docs/{category-slug}/_index.md`

Add inside the `{{</* cards */>}}` block. Scored tests before unscored.

```
{{</* card link="test-slug" title="TEST NAME" subtitle="Short description." */>}}
```

## Step 5 — Add test ID to the website table group

**File:** `docs/content/{compliance,smuggling,malformed-input,normalization}/_index.md`

Find the `GROUPS` array in the `<script>` block and add the new test ID to the appropriate sub-group's `testIds` array.

For example, in `docs/content/compliance/_index.md`:
```javascript
{ key: 'request-parsing', label: 'Request Parsing', testIds: [
    'RFC9112-2.2-BARE-LF-REQUEST-LINE', ..., 'NEW-TEST-ID'
]},
```

This controls which table group the test appears in on the results page.

## Step 6 — Add a row to the RFC Requirement Dashboard

**File:** `docs/content/docs/rfc-requirement-dashboard.md`

1. Add a row to the correct table (MUST/SHOULD/MAY/Unscored)
2. Update all counts: summary table, total, suite breakdown, RFC section cross-reference

---

## Verification

After all steps, run:

```bash
dotnet build Http11Probe.slnx -c Release
```

Must compile without errors. Then confirm the test appears in the output of:

```bash
dotnet run --project src/Http11Probe.Cli -- --host localhost --port 8080 --test PREFIX-NAME
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mda2av) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
