---
name: spectral-linting
description: Spectral API linting configuration, rules, and validation for OpenAPI specs Use when this capability is needed.
metadata:
  author: zerobias-org
---

# API Linting Configuration

## 🚨 CRITICAL RULES

### 1. Use Spectral for OpenAPI Linting
ALL api.yml files MUST be linted with Spectral before merge.

**Install Spectral:**
```bash
npm install -g @stoplight/spectral-cli
# or use npx
npx @stoplight/spectral-cli lint api.yml
```

### 2. Minimum .spectral.yml Configuration
Every module SHOULD have a `.spectral.yml` file in the module root:

```yaml
extends: spectral:oas

rules:
  # Critical rules (errors)
  oas3-valid-schema-example: error
  operation-operationId: error
  operation-success-response: error

  # Important rules (warnings)
  operation-description: warn
  operation-tags: warn
  info-description: error
```

### 3. Zero Errors Before Merge
- ❌ API specs with linting errors CANNOT be merged
- ⚠️ Warnings should be fixed but don't block merge
- ℹ️ Info-level issues are optional

## 🟡 STANDARD RULES

### Standard .spectral.yml Template

**Basic configuration:**

```yaml
# .spectral.yml - OpenAPI linting configuration

# Extend Spectral's built-in OpenAPI ruleset
extends: spectral:oas

# Custom rules and severity overrides
rules:
  # ========================================
  # Critical Rules (error = must fix)
  # ========================================

  # All examples must be valid against their schemas
  oas3-valid-schema-example: error

  # Every operation must have an operationId
  operation-operationId: error

  # Every operation must have a success response (200-299)
  operation-success-response: error

  # All tags used in operations must be defined
  operation-tag-defined: error

  # Info section must have description
  info-description: error

  # ========================================
  # Important Rules (warn = should fix)
  # ========================================

  # Operations should have descriptions
  operation-description: warn

  # Operations should have tags
  operation-tags: warn

  # Parameters should have descriptions
  operation-parameters: warn

  # Paths should use kebab-case
  path-keys-no-trailing-slash: warn

  # ========================================
  # Optional Rules (info = nice to have)
  # ========================================

  # Contact information recommended
  info-contact: info

  # License information recommended
  info-license: info
```

### Spectral Built-in Rules

**Spectral:oas includes these rules by default:**

| Rule | Default | Description |
|------|---------|-------------|
| `oas3-valid-schema-example` | warn | Examples must validate against schemas |
| `operation-operationId` | warn | Operations should have operationId |
| `operation-success-response` | warn | Operations should have 2xx response |
| `operation-description` | warn | Operations should have description |
| `operation-tags` | warn | Operations should have tags |
| `operation-tag-defined` | warn | Tags must be defined in global tags |
| `info-description` | warn | Info section should have description |
| `info-contact` | off | Info section should have contact |
| `info-license` | off | Info section should have license |
| `path-keys-no-trailing-slash` | warn | Paths should not end with / |

### Severity Levels

**Four severity levels:**

```yaml
rules:
  rule-name: error   # MUST fix - blocks merge
  rule-name: warn    # SHOULD fix - doesn't block
  rule-name: info    # OPTIONAL - informational only
  rule-name: off     # Disabled - not checked
```

**When to use each:**
- `error`: Critical issues that break spec or cause generation failures
- `warn`: Important issues that should be fixed but don't break functionality
- `info`: Nice-to-have improvements
- `off`: Rule doesn't apply or is too strict

### Custom Rules

**Add custom rules for project-specific requirements:**

```yaml
rules:
  # Custom rule: Require x-auditmation-operation metadata
  require-auditmation-operation-tag:
    description: Operations must have x-auditmation-operation metadata
    given: $.paths.*[get,post,put,delete,patch]
    severity: error
    then:
      field: x-auditmation-operation
      function: truthy

  # Custom rule: Require examples in responses
  require-response-examples:
    description: Success responses should have examples
    given: $.paths.*.*.responses[?(@property >= 200 && @property < 300)]
    severity: warn
    then:
      field: content.application/json.example
      function: truthy

  # Custom rule: Consistent error response structure
  require-error-schema:
    description: Error responses should use standard error schema
    given: $.paths.*.*.responses[?(@property >= 400)]
    severity: warn
    then:
      field: content.application/json.schema.$ref
      function: pattern
      functionOptions:
        match: "#/components/schemas/Error"
```

### Disabling Rules

**Disable rules with justification:**

```yaml
rules:
  # Disabled: Legacy API doesn't follow this pattern
  # Date: 2024-10-15
  # Reason: Historical API design, can't change without breaking clients
  operation-description: off

  # Disabled: Service doesn't use tags
  # Date: 2024-10-15
  # Reason: Small API, tags add unnecessary complexity
  operation-tags: off
```

**When to disable:**
- ✅ Legacy APIs that can't be changed
- ✅ Rules that don't fit your use case
- ✅ Temporarily during development
- ❌ To avoid fixing legitimate issues
- ❌ Without documenting why

### Inline Overrides

**Disable specific rules for specific operations:**

```yaml
paths:
  /legacy/endpoint:
    get:
      # spectral:disable operation-description
      summary: Legacy endpoint
      operationId: legacyOperation
      # ... rest of operation
```

**Use sparingly:**
- Only for exceptions
- Document why in comments
- Prefer fixing the issue over disabling

## 🟢 GUIDELINES

### Running Spectral

**Basic usage:**
```bash
# Lint single file
npx @stoplight/spectral-cli lint api.yml

# Lint with custom config
npx @stoplight/spectral-cli lint -r .spectral.yml api.yml

# Output formats
npx @stoplight/spectral-cli lint api.yml -f json
npx @stoplight/spectral-cli lint api.yml -f html > report.html

# Lint multiple files
npx @stoplight/spectral-cli lint **/*.yml
```

**In package.json scripts:**
```json
{
  "scripts": {
    "lint:api": "spectral lint api.yml",
    "lint:api:fix": "spectral lint api.yml --format pretty"
  }
}
```

**In CI/CD:**
```yaml
# .github/workflows/ci.yml
- name: Lint API Spec
  run: npx @stoplight/spectral-cli lint api.yml
```

### Common Rules to Enable

**For strict API design:**
```yaml
rules:
  # Require descriptions everywhere
  operation-description: error
  operation-parameters: error
  components-examples: error

  # Require tags
  operation-tags: error
  operation-tag-defined: error

  # Require 2xx and 4xx responses
  operation-success-response: error
  operation-4xx-response: warn

  # Schema validation
  oas3-valid-schema-example: error
  typed-enum: error

  # Naming conventions
  path-keys-no-trailing-slash: error
  path-params: error
```

**For relaxed API design:**
```yaml
rules:
  # Only critical validation
  oas3-valid-schema-example: error
  operation-operationId: error

  # Everything else is warnings or off
  operation-description: warn
  operation-tags: off
```

### JSONPath Expressions

**Target specific parts of spec:**

```yaml
rules:
  # All GET operations
  given: $.paths.*.get

  # All operations (any HTTP method)
  given: $.paths.*[get,post,put,delete,patch]

  # All parameters
  given: $.paths.*.*.parameters[*]

  # All 2xx responses
  given: $.paths.*.*.responses[?(@property >= 200 && @property < 300)]

  # All schemas
  given: $.components.schemas[*]
```

**Functions:**
- `truthy` - field must exist and not be empty
- `falsy` - field must not exist or be empty
- `pattern` - field must match regex
- `length` - field length validation
- `schema` - validate against JSON schema

### Multiple Rulesets

**Extend multiple rulesets:**

```yaml
extends:
  - spectral:oas
  - spectral:asyncapi
  # - custom-ruleset.yml

rules:
  # Your rules
```

**Create custom ruleset file:**

```yaml
# custom-auditmation-rules.yml
rules:
  require-x-auditmation-operation:
    description: Operations must have x-auditmation-operation
    given: $.paths.*[get,post,put,delete,patch]
    severity: error
    then:
      field: x-auditmation-operation
      function: truthy

  require-examples:
    description: All schemas should have examples
    given: $.components.schemas[*]
    severity: warn
    then:
      field: example
      function: truthy
```

**Then extend it:**
```yaml
# .spectral.yml
extends:
  - spectral:oas
  - ./custom-auditmation-rules.yml
```

## Validation

### Check .spectral.yml Exists
```bash
# Check if .spectral.yml exists
if [ -f .spectral.yml ]; then
  echo "✅ PASS: .spectral.yml found"
else
  echo "⚠️ WARN: No .spectral.yml (using default rules)"
fi
```

### Validate .spectral.yml Syntax
```bash
# Validate YAML syntax
if [ -f .spectral.yml ]; then
  if command -v yq &> /dev/null; then
    if yq . .spectral.yml > /dev/null 2>&1; then
      echo "✅ PASS: Valid YAML syntax"
    else
      echo "❌ FAIL: Invalid YAML syntax"
      exit 1
    fi
  else
    echo "⚠️ WARN: yq not installed, cannot validate YAML"
  fi
fi
```

### Check Extends Spectral:oas
```bash
# Check extends spectral:oas
if [ -f .spectral.yml ]; then
  if grep -q "extends.*spectral:oas" .spectral.yml; then
    echo "✅ PASS: Extends spectral:oas"
  else
    echo "⚠️ WARN: Should extend spectral:oas"
  fi
fi
```

### Run Spectral Lint
```bash
# Run spectral lint on api.yml
if command -v spectral &> /dev/null || command -v npx &> /dev/null; then
  echo "Running Spectral lint..."

  if [ -f api.yml ]; then
    if npx @stoplight/spectral-cli lint api.yml; then
      echo "✅ PASS: API spec passes linting"
    else
      echo "❌ FAIL: API spec has linting errors"
      exit 1
    fi
  else
    echo "⚠️ WARN: No api.yml found"
  fi
else
  echo "⚠️ WARN: Spectral not installed"
fi
```

### Check for Critical Rules
```bash
# Check critical rules are enabled
if [ -f .spectral.yml ]; then
  echo "Checking critical rules..."

  # Check oas3-valid-schema-example
  if grep -q "oas3-valid-schema-example.*error" .spectral.yml; then
    echo "  ✅ oas3-valid-schema-example: error"
  else
    echo "  ⚠️  Should set oas3-valid-schema-example to error"
  fi

  # Check operation-operationId
  if grep -q "operation-operationId.*error" .spectral.yml; then
    echo "  ✅ operation-operationId: error"
  else
    echo "  ⚠️  Should set operation-operationId to error"
  fi

  # Check operation-success-response
  if grep -q "operation-success-response.*error" .spectral.yml; then
    echo "  ✅ operation-success-response: error"
  else
    echo "  ⚠️  Should set operation-success-response to error"
  fi
fi
```

### Complete Validation Script
```bash
#!/bin/bash
# validate-spectral.sh - Validate Spectral configuration and run linting

echo "=== Spectral API Linting Validation ==="
echo ""

ERRORS=0

# 1. Check .spectral.yml exists
echo "1. Configuration File:"
if [ -f .spectral.yml ]; then
  echo "  ✅ .spectral.yml exists"

  # Validate YAML syntax
  if command -v yq &> /dev/null; then
    if yq . .spectral.yml > /dev/null 2>&1; then
      echo "  ✅ Valid YAML syntax"
    else
      echo "  ❌ Invalid YAML syntax"
      ERRORS=$((ERRORS + 1))
    fi
  fi
else
  echo "  ⚠️  No .spectral.yml (will use defaults)"
fi
echo ""

# 2. Check extends spectral:oas
if [ -f .spectral.yml ]; then
  echo "2. Ruleset Configuration:"
  if grep -q "extends.*spectral:oas" .spectral.yml; then
    echo "  ✅ Extends spectral:oas"
  else
    echo "  ⚠️  Should extend spectral:oas"
  fi
  echo ""
fi

# 3. Check critical rules
if [ -f .spectral.yml ]; then
  echo "3. Critical Rules:"

  if grep -q "oas3-valid-schema-example.*error" .spectral.yml; then
    echo "  ✅ oas3-valid-schema-example: error"
  else
    echo "  ⚠️  Recommend: oas3-valid-schema-example: error"
  fi

  if grep -q "operation-operationId.*error" .spectral.yml; then
    echo "  ✅ operation-operationId: error"
  else
    echo "  ⚠️  Recommend: operation-operationId: error"
  fi

  if grep -q "operation-success-response.*error" .spectral.yml; then
    echo "  ✅ operation-success-response: error"
  else
    echo "  ⚠️  Recommend: operation-success-response: error"
  fi
  echo ""
fi

# 4. Check Spectral is available
echo "4. Spectral Installation:"
if command -v spectral &> /dev/null; then
  VERSION=$(spectral --version)
  echo "  ✅ Spectral installed: $VERSION"
elif command -v npx &> /dev/null; then
  echo "  ✅ Can use npx @stoplight/spectral-cli"
else
  echo "  ❌ Spectral not available (install with: npm install -g @stoplight/spectral-cli)"
  ERRORS=$((ERRORS + 1))
fi
echo ""

# 5. Run lint on api.yml
echo "5. API Spec Linting:"
if [ -f api.yml ]; then
  echo "  Found api.yml, running lint..."
  echo ""

  if command -v spectral &> /dev/null; then
    LINT_CMD="spectral lint api.yml"
  elif command -v npx &> /dev/null; then
    LINT_CMD="npx @stoplight/spectral-cli lint api.yml"
  else
    echo "  ❌ Cannot run lint - Spectral not available"
    ERRORS=$((ERRORS + 1))
  fi

  if [ -n "$LINT_CMD" ]; then
    if $LINT_CMD; then
      echo ""
      echo "  ✅ API spec passes linting"
    else
      echo ""
      echo "  ❌ API spec has linting errors"
      ERRORS=$((ERRORS + 1))
    fi
  fi
else
  echo "  ⚠️  No api.yml found to lint"
fi
echo ""

# Summary
if [ $ERRORS -eq 0 ]; then
  echo "=== ✅ VALIDATION PASSED ==="
  exit 0
else
  echo "=== ❌ VALIDATION FAILED ($ERRORS errors) ==="
  exit 1
fi
```

## Common Issues

### Issue: Spectral not found
**Solution:**
```bash
# Install globally
npm install -g @stoplight/spectral-cli

# Or use npx (no install needed)
npx @stoplight/spectral-cli lint api.yml
```

### Issue: Many warnings overwhelming output
**Solution:**
```yaml
# Reduce verbosity by turning some rules off
rules:
  operation-description: off
  operation-tags: off
  info-contact: off
```

### Issue: Custom rule not working
**Check:**
1. JSONPath expression is correct
2. Field name matches spec
3. Function is valid Spectral function
4. Severity is set

**Debug:**
```bash
# Use --verbose to see rule evaluation
npx @stoplight/spectral-cli lint api.yml --verbose
```

### Issue: Valid spec fails linting
**Common causes:**
1. Example doesn't match schema
2. Missing operationId
3. Missing success response
4. Tag not defined in global tags

**Fix:**
```yaml
# In api.yml
paths:
  /users:
    get:
      operationId: listUsers  # Add this
      tags:
        - Users  # Must be in global tags
      responses:
        '200':  # Must have success response
          description: Success
```

## Anti-Patterns

### ❌ BAD: Disabling all rules
```yaml
rules:
  oas3-valid-schema-example: off
  operation-operationId: off
  operation-success-response: off
  # Everything disabled
```

### ✅ GOOD: Enable strict validation
```yaml
rules:
  oas3-valid-schema-example: error
  operation-operationId: error
  operation-success-response: error
```

### ❌ BAD: No .spectral.yml
```bash
# No configuration, using defaults
# No customization, no documentation
```

### ✅ GOOD: Explicit configuration
```yaml
# .spectral.yml with documented rules
extends: spectral:oas

rules:
  # Critical rules
  oas3-valid-schema-example: error
  # ... with comments explaining choices
```

### ❌ BAD: Inline disables everywhere
```yaml
paths:
  /endpoint1:
    get:
      # spectral:disable operation-description
      ...
  /endpoint2:
    post:
      # spectral:disable operation-description
      ...
```

### ✅ GOOD: Fix the issues or disable globally with justification
```yaml
rules:
  # Disabled: Legacy API pattern
  operation-description: off
```

## Integration

### Pre-commit Hook
```bash
# .git/hooks/pre-commit
#!/bin/bash

if [ -f api.yml ]; then
  echo "Linting API spec..."
  if ! npx @stoplight/spectral-cli lint api.yml; then
    echo "❌ API linting failed. Fix errors before committing."
    exit 1
  fi
fi
```

### GitHub Actions
```yaml
# .github/workflows/lint-api.yml
name: Lint API Spec

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - name: Lint API
        run: npx @stoplight/spectral-cli lint api.yml
```

### npm Scripts
```json
{
  "scripts": {
    "lint:api": "spectral lint api.yml",
    "lint:api:json": "spectral lint api.yml -f json",
    "lint:api:html": "spectral lint api.yml -f html -o api-lint-report.html",
    "test": "npm run lint:api && jest"
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zerobias-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
