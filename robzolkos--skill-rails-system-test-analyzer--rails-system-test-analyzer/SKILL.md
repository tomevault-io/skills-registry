---
name: rails-system-test-analyzer
description: Analyze Rails system tests to identify which ones could be converted to faster controller tests. Use when optimizing test suites, reviewing system tests, or when asked about test performance. Use when this capability is needed.
metadata:
  author: robzolkos
---

# Rails System Test Analyzer

Analyze Rails system tests to determine if they truly require browser-based testing or could be converted to faster controller/integration tests.

## Core Principle

System tests should ONLY be used when testing functionality that absolutely requires a real browser:
- JavaScript interactions (Stimulus, Turbo, custom JS)
- Complex client-side state
- Browser-specific features (file uploads with preview, drag-and-drop, etc.)

If a test only verifies server-rendered HTML, form submissions, and redirects, it should be a controller test.

## Analysis Process

### Step 1: Identify System Tests

If $ARGUMENTS is provided, analyze those specific files. Otherwise, find all system tests:

```bash
find test/system -name "*_test.rb" -type f 2>/dev/null || find spec/system -name "*_spec.rb" -type f 2>/dev/null
```

### Step 2: For Each Test, Trace the Code Path

For each test file, analyze each test case:

1. **Extract the test actions**: Look for `visit`, `click_on`, `click_link`, `click_button`, `fill_in`, `select`, `check`, `uncheck`, `choose`, `attach_file`

2. **Identify routes/controllers**: Map `visit some_path` to controller actions using `config/routes.rb`

3. **Find views rendered**: From the controller action, identify:
   - Main view template (`app/views/controller/action.html.erb`)
   - Partials rendered (`render partial:`, `render @collection`)
   - Layouts used
   - Helpers referenced (`app/helpers/`)
   - Stimulus controllers referenced via `data-controller` attributes (check `app/javascript/controllers/`)

4. **Check for JavaScript dependencies** in each view/partial/helper:

### Step 3: JavaScript Detection Checklist

Scan views, partials, and layouts for:

**Stimulus/Hotwire (REQUIRES system test):**
- `data-controller=`
- `data-action=`
- `data-*-target=`
- `<turbo-frame`
- `<turbo-stream`
- `data-turbo-method=`
- `data-turbo-confirm=`
- `data-turbo-frame=`

**Traditional JavaScript (REQUIRES system test):**
- `<script>` tags with logic (not just analytics)
- `onclick=`, `onchange=`, `onsubmit=`, `onfocus=`, `onblur=`
- `javascript:` links
- `remote: true` in form helpers (legacy Rails UJS)

**Capybara JS assertions (suggests JS dependency):**
- `have_css` with `visible:` option
- `have_selector` with `wait:` option
- `accept_alert`, `accept_confirm`, `dismiss_confirm`
- `execute_script`, `evaluate_script`
- `within_window`, `switch_to_window`

### Step 4: Classify Each Test

For each test, determine:

**KEEP AS SYSTEM TEST if:**
- Any JavaScript detected in code path
- Test uses JS-specific Capybara matchers
- Test verifies dynamic content updates
- Test checks client-side validation
- Test involves file uploads with preview
- Test requires specific browser behavior

**CONVERT TO CONTROLLER TEST if:**
- No JavaScript in code path
- Only tests server-rendered content
- Only verifies redirects and flash messages
- Only checks presence of static elements
- Form submissions without JS enhancement

### Step 5: Generate Report

For each test file analyzed, output:

```
## [test_file_name.rb]

### Test: "test description"
- **Code path**: UsersController#create -> views/users/new.html.erb
- **JavaScript detected**: None / [list findings]
- **Recommendation**: CONVERT / KEEP
- **Confidence**: High / Medium / Low
- **Reason**: [brief explanation]

[If CONVERT, provide controller test skeleton]
```

## Controller Test Conversion Template

When recommending conversion, provide a skeleton:

```ruby
# test/controllers/[controller]_test.rb

class [Controller]Test < ActionDispatch::IntegrationTest
  test "[original test name]" do
    # Setup
    user = users(:one)

    # Exercise
    get new_user_path
    assert_response :success

    post users_path, params: { user: { name: "Test" } }

    # Verify
    assert_redirected_to user_path(User.last)
    follow_redirect!
    assert_select "h1", "Test"
  end
end
```

## Summary Statistics

At the end, provide:

```
## Summary

- Total system tests analyzed: X
- Tests requiring system test (JS detected): Y
- Tests convertible to controller tests: Z
- Potential time savings: ~Z * 2-5 seconds per test run

### Quick Wins (High confidence conversions)
1. test_file.rb - test_name
2. ...

### Needs Review (Medium confidence)
1. ...
```

## Important Notes

- Always read the actual test code, views, and controllers - don't guess
- Check both direct JavaScript and inherited from layouts
- Consider that some Turbo features work without JS (progressive enhancement)
- When in doubt, recommend keeping as system test
- Provide actionable, copy-pasteable controller test code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robzolkos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
