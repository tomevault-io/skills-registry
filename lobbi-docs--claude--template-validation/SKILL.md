---
name: template-validation
description: Validate Cookiecutter templates, Terraform modules, and Harness pipelines Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Template Validation Skill

## When to Use This Skill

Use this skill when you need to:
- Validate Cookiecutter template syntax and structure
- Check Terraform module validity and best practices
- Verify Harness pipeline YAML schemas
- Ensure variable references are correct and consistent
- Catch errors before template generation or deployment
- Perform quality checks on generated templates
- Validate template dependencies and integrations
- Check for security vulnerabilities in templates

## Validation Capabilities

### 1. Jinja2 Syntax Validation

**What We Validate:**
- Template syntax correctness
- Variable interpolation `{{ cookiecutter.variable }}`
- Control structures `{% if %}`, `{% for %}`
- Filter usage `{{ variable | filter }}`
- Macro definitions and calls
- Template inheritance
- Whitespace control

**Validation Method:**
```python
from jinja2 import Environment, FileSystemLoader, TemplateSyntaxError

def validate_jinja2(template_path):
    try:
        env = Environment(loader=FileSystemLoader('.'))
        template = env.get_template(template_path)
        return {"valid": True, "errors": []}
    except TemplateSyntaxError as e:
        return {
            "valid": False,
            "errors": [{
                "line": e.lineno,
                "message": e.message,
                "file": template_path
            }]
        }
```

### 2. Terraform HCL Validation

**What We Validate:**
- HCL syntax correctness
- Resource block structure
- Variable declarations and types
- Output definitions
- Module references
- Provider configurations
- Terraform version constraints
- Resource dependencies

**Validation Commands:**
```bash
# Format check
terraform fmt -check -recursive

# Validation
terraform validate

# Security scanning
tfsec .

# Best practices
tflint --recursive
```

### 3. YAML Schema Validation

**What We Validate:**
- YAML syntax correctness
- Harness pipeline schema compliance
- Required field presence
- Data type correctness
- Enum value validation
- Array/object structure
- Custom schema rules

**Validation Method:**
```python
import yaml
from jsonschema import validate, ValidationError

def validate_harness_pipeline(pipeline_path, schema_path):
    with open(pipeline_path) as f:
        pipeline = yaml.safe_load(f)

    with open(schema_path) as f:
        schema = yaml.safe_load(f)

    try:
        validate(instance=pipeline, schema=schema)
        return {"valid": True, "errors": []}
    except ValidationError as e:
        return {
            "valid": False,
            "errors": [{
                "path": list(e.path),
                "message": e.message
            }]
        }
```

### 4. Variable Reference Checking

**What We Check:**
- All referenced variables are defined
- Variable types match usage
- Default values are appropriate
- Required variables are documented
- No unused variables
- Consistent naming conventions
- Cross-template variable consistency

## Validation Checklists

### Cookiecutter Template Validation

| Check | Description | Command |
|-------|-------------|---------|
| **Structure** | Required files present | `ls cookiecutter.json hooks/ {{cookiecutter.project_slug}}/` |
| **JSON Schema** | `cookiecutter.json` valid | `python -m json.tool cookiecutter.json` |
| **Jinja2 Syntax** | All templates parse | `python -c "from jinja2 import Environment, FileSystemLoader; env = Environment(loader=FileSystemLoader('.')); [env.get_template(t) for t in templates]"` |
| **Variables** | All refs defined | `grep -r "cookiecutter\." --include="*.jinja" \| awk '{print $2}' \| sort \| uniq` |
| **Hooks** | Pre/post hooks work | `python hooks/pre_gen_project.py && python hooks/post_gen_project.py` |
| **Tests** | Template generates | `cookiecutter . --no-input` |
| **Docs** | README complete | `grep -q "## Variables" README.md` |

### Terraform Module Validation

| Check | Description | Command |
|-------|-------------|---------|
| **Syntax** | HCL format correct | `terraform fmt -check -recursive` |
| **Init** | Module initializes | `terraform init` |
| **Validate** | Configuration valid | `terraform validate` |
| **Variables** | All declared/used | `terraform-docs markdown . --output-file VALIDATION.md` |
| **Outputs** | All defined properly | `grep -r "output \"" *.tf` |
| **Security** | No vulnerabilities | `tfsec . --minimum-severity MEDIUM` |
| **Lint** | Best practices | `tflint --recursive --config .tflint.hcl` |
| **Docs** | Auto-generated | `terraform-docs markdown table --output-file README.md .` |

### Harness Pipeline Validation

| Check | Description | Command |
|-------|-------------|---------|
| **YAML Syntax** | Valid YAML | `yamllint pipeline.yaml` |
| **Schema** | Harness schema | `harness pipeline validate --file pipeline.yaml` |
| **Variables** | All defined | `yq '.pipeline.variables' pipeline.yaml` |
| **Stages** | Valid structure | `yq '.pipeline.stages[].stage.type' pipeline.yaml` |
| **Services** | Service refs exist | `harness service list --org ${ORG} --project ${PROJECT}` |
| **Environments** | Env refs exist | `harness environment list --org ${ORG} --project ${PROJECT}` |
| **Connectors** | Connector refs valid | `harness connector list --org ${ORG}` |
| **Secrets** | Secret refs exist | `harness secret list --org ${ORG}` |

## Common Errors and Fixes

### Jinja2 Template Errors

**Error: `TemplateSyntaxError: unexpected '}'`**
```yaml
# ❌ Wrong
{{ cookiecutter.name }}

# ✅ Correct
{{ cookiecutter.name }}
```
**Fix:** Check for balanced braces, proper spacing, and valid variable names.

---

**Error: `UndefinedError: 'dict object' has no attribute 'varname'`**
```yaml
# ❌ Wrong
{{ cookiecutter.missing_var }}

# ✅ Correct (add to cookiecutter.json)
{
  "missing_var": "default_value"
}
```
**Fix:** Ensure all referenced variables are defined in `cookiecutter.json`.

---

**Error: Filter not found**
```yaml
# ❌ Wrong
{{ cookiecutter.name | nonexistent_filter }}

# ✅ Correct
{{ cookiecutter.name | lower }}
```
**Fix:** Use only built-in Jinja2 filters or define custom filters in hooks.

### Terraform Validation Errors

**Error: `Missing required argument`**
```hcl
# ❌ Wrong
resource "azurerm_resource_group" "rg" {
  name = var.resource_group_name
  # missing location
}

# ✅ Correct
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}
```
**Fix:** Add all required arguments for the resource type.

---

**Error: `Invalid reference`**
```hcl
# ❌ Wrong
output "rg_id" {
  value = azurerm_resource_group.nonexistent.id
}

# ✅ Correct
output "rg_id" {
  value = azurerm_resource_group.rg.id
}
```
**Fix:** Ensure resource references match actual resource names.

---

**Error: `tfsec: Azure Storage account not using HTTPS`**
```hcl
# ❌ Wrong
resource "azurerm_storage_account" "sa" {
  # ... other config
}

# ✅ Correct
resource "azurerm_storage_account" "sa" {
  # ... other config
  enable_https_traffic_only = true
  min_tls_version          = "TLS1_2"
}
```
**Fix:** Follow security best practices and add required security configurations.

### Harness Pipeline Errors

**Error: `Invalid pipeline schema`**
```yaml
# ❌ Wrong
pipeline:
  name: My Pipeline
  stage:  # should be stages (plural)
    - type: Deployment

# ✅ Correct
pipeline:
  name: My Pipeline
  stages:
    - stage:
        type: Deployment
```
**Fix:** Follow exact Harness schema structure.

---

**Error: `Service not found`**
```yaml
# ❌ Wrong
serviceRef: nonexistent_service

# ✅ Correct (verify service exists first)
# harness service list --org myorg --project myproj
serviceRef: my_existing_service
```
**Fix:** Verify all referenced entities exist before using them.

## Validation Command Examples

### Complete Cookiecutter Validation

```bash
#!/bin/bash
# validate_cookiecutter.sh

echo "🔍 Validating Cookiecutter Template..."

# 1. Check structure
echo "📁 Checking directory structure..."
required_files=("cookiecutter.json" "{{cookiecutter.project_slug}}/")
for file in "${required_files[@]}"; do
  if [ ! -e "$file" ]; then
    echo "❌ Missing required: $file"
    exit 1
  fi
done

# 2. Validate JSON
echo "📄 Validating cookiecutter.json..."
python -m json.tool cookiecutter.json > /dev/null || {
  echo "❌ Invalid JSON in cookiecutter.json"
  exit 1
}

# 3. Check Jinja2 templates
echo "🎨 Validating Jinja2 templates..."
python << EOF
from jinja2 import Environment, FileSystemLoader
import glob

env = Environment(loader=FileSystemLoader('.'))
templates = glob.glob('**/*.jinja', recursive=True)
errors = []

for template in templates:
    try:
        env.get_template(template)
    except Exception as e:
        errors.append(f"{template}: {str(e)}")

if errors:
    print("❌ Template errors found:")
    for error in errors:
        print(f"  - {error}")
    exit(1)
else:
    print("✅ All Jinja2 templates valid")
EOF

# 4. Test generation
echo "🧪 Testing template generation..."
cookiecutter . --no-input --output-dir /tmp/test-output || {
  echo "❌ Template generation failed"
  exit 1
}

echo "✅ All validations passed!"
```

### Complete Terraform Validation

```bash
#!/bin/bash
# validate_terraform.sh

echo "🔍 Validating Terraform Module..."

# 1. Format check
echo "📐 Checking format..."
terraform fmt -check -recursive || {
  echo "❌ Terraform files not formatted. Run: terraform fmt -recursive"
  exit 1
}

# 2. Initialize
echo "⚙️  Initializing..."
terraform init -backend=false || {
  echo "❌ Terraform init failed"
  exit 1
}

# 3. Validate
echo "✅ Validating configuration..."
terraform validate || {
  echo "❌ Terraform validation failed"
  exit 1
}

# 4. Security scan
echo "🔒 Running security scan..."
tfsec . --minimum-severity MEDIUM || {
  echo "⚠️  Security issues found"
  exit 1
}

# 5. Linting
echo "🔍 Running tflint..."
tflint --recursive || {
  echo "⚠️  Linting issues found"
  exit 1
}

# 6. Generate docs
echo "📚 Generating documentation..."
terraform-docs markdown table --output-file README.md .

echo "✅ All validations passed!"
```

### Complete Harness Pipeline Validation

```bash
#!/bin/bash
# validate_harness_pipeline.sh

echo "🔍 Validating Harness Pipeline..."

PIPELINE_FILE="$1"
ORG="$2"
PROJECT="$3"

# 1. YAML syntax
echo "📄 Checking YAML syntax..."
yamllint "$PIPELINE_FILE" || {
  echo "❌ YAML syntax errors"
  exit 1
}

# 2. Extract references
echo "🔗 Extracting references..."
SERVICE_REF=$(yq '.pipeline.stages[].stage.spec.serviceConfig.serviceRef' "$PIPELINE_FILE")
ENV_REF=$(yq '.pipeline.stages[].stage.spec.infrastructure.environmentRef' "$PIPELINE_FILE")
CONNECTOR_REF=$(yq '.pipeline.stages[].stage.spec.infrastructure.infrastructureDefinition.spec.connectorRef' "$PIPELINE_FILE")

# 3. Verify service exists
if [ -n "$SERVICE_REF" ] && [ "$SERVICE_REF" != "null" ]; then
  echo "🔍 Verifying service: $SERVICE_REF..."
  harness service get --org "$ORG" --project "$PROJECT" --id "$SERVICE_REF" > /dev/null || {
    echo "❌ Service not found: $SERVICE_REF"
    exit 1
  }
fi

# 4. Verify environment exists
if [ -n "$ENV_REF" ] && [ "$ENV_REF" != "null" ]; then
  echo "🔍 Verifying environment: $ENV_REF..."
  harness environment get --org "$ORG" --project "$PROJECT" --id "$ENV_REF" > /dev/null || {
    echo "❌ Environment not found: $ENV_REF"
    exit 1
  }
fi

# 5. Verify connector exists
if [ -n "$CONNECTOR_REF" ] && [ "$CONNECTOR_REF" != "null" ]; then
  echo "🔍 Verifying connector: $CONNECTOR_REF..."
  harness connector get --org "$ORG" --id "$CONNECTOR_REF" > /dev/null || {
    echo "❌ Connector not found: $CONNECTOR_REF"
    exit 1
  }
fi

# 6. Schema validation (if available)
echo "📋 Validating against Harness schema..."
harness pipeline validate --file "$PIPELINE_FILE" || {
  echo "❌ Schema validation failed"
  exit 1
}

echo "✅ All validations passed!"
```

## Validation Decision Tree

```
Start: Need to validate template?
│
├─ Cookiecutter Template?
│  ├─ YES → Run Cookiecutter validation checklist
│  │       ├─ JSON valid?
│  │       │  ├─ NO → Fix cookiecutter.json syntax
│  │       │  └─ YES → Continue
│  │       ├─ Jinja2 valid?
│  │       │  ├─ NO → Fix template syntax errors
│  │       │  └─ YES → Continue
│  │       ├─ Variables defined?
│  │       │  ├─ NO → Add missing variables
│  │       │  └─ YES → Continue
│  │       └─ Test generation?
│  │          ├─ FAIL → Debug template logic
│  │          └─ PASS → ✅ Valid
│  │
├─ Terraform Module?
│  ├─ YES → Run Terraform validation checklist
│  │       ├─ Format check?
│  │       │  ├─ FAIL → Run terraform fmt -recursive
│  │       │  └─ PASS → Continue
│  │       ├─ Init successful?
│  │       │  ├─ NO → Fix provider/module issues
│  │       │  └─ YES → Continue
│  │       ├─ Validate passes?
│  │       │  ├─ NO → Fix configuration errors
│  │       │  └─ YES → Continue
│  │       ├─ Security scan clean?
│  │       │  ├─ NO → Fix security issues
│  │       │  └─ YES → Continue
│  │       └─ Lint clean?
│  │          ├─ NO → Fix best practice violations
│  │          └─ PASS → ✅ Valid
│  │
└─ Harness Pipeline?
   ├─ YES → Run Harness validation checklist
   │       ├─ YAML syntax valid?
   │       │  ├─ NO → Fix YAML syntax
   │       │  └─ YES → Continue
   │       ├─ Schema compliant?
   │       │  ├─ NO → Fix schema violations
   │       │  └─ YES → Continue
   │       ├─ Services exist?
   │       │  ├─ NO → Create services or fix refs
   │       │  └─ YES → Continue
   │       ├─ Environments exist?
   │       │  ├─ NO → Create envs or fix refs
   │       │  └─ YES → Continue
   │       └─ Connectors valid?
   │          ├─ NO → Create connectors or fix refs
   │          └─ YES → ✅ Valid
   │
   └─ NO → Determine template type first
```

## Best Practices

### 1. Validate Early and Often

```bash
# Pre-commit hook for validation
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
echo "🔍 Running template validation..."

# Detect template type
if [ -f "cookiecutter.json" ]; then
  ./scripts/validate_cookiecutter.sh || exit 1
elif [ -f "main.tf" ]; then
  ./scripts/validate_terraform.sh || exit 1
elif [ -f "pipeline.yaml" ]; then
  ./scripts/validate_harness_pipeline.sh pipeline.yaml || exit 1
fi

echo "✅ Validation passed"
EOF

chmod +x .git/hooks/pre-commit
```

### 2. Automate Validation in CI/CD

```yaml
# .github/workflows/validate.yml
name: Template Validation

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Detect Template Type
        id: detect
        run: |
          if [ -f "cookiecutter.json" ]; then
            echo "type=cookiecutter" >> $GITHUB_OUTPUT
          elif [ -f "main.tf" ]; then
            echo "type=terraform" >> $GITHUB_OUTPUT
          elif [ -f "pipeline.yaml" ]; then
            echo "type=harness" >> $GITHUB_OUTPUT
          fi

      - name: Validate Cookiecutter
        if: steps.detect.outputs.type == 'cookiecutter'
        run: |
          pip install cookiecutter jinja2
          ./scripts/validate_cookiecutter.sh

      - name: Validate Terraform
        if: steps.detect.outputs.type == 'terraform'
        run: |
          terraform init
          terraform fmt -check -recursive
          terraform validate
          tfsec . --minimum-severity MEDIUM

      - name: Validate Harness
        if: steps.detect.outputs.type == 'harness'
        run: |
          pip install yamllint yq
          yamllint pipeline.yaml
```

### 3. Create Validation Reports

```python
# generate_validation_report.py
import json
from datetime import datetime

def generate_report(validation_results):
    """Generate validation report"""
    report = {
        "timestamp": datetime.now().isoformat(),
        "template_type": validation_results["type"],
        "checks": [],
        "passed": 0,
        "failed": 0,
        "warnings": 0
    }

    for check in validation_results["checks"]:
        report["checks"].append({
            "name": check["name"],
            "status": check["status"],
            "message": check.get("message", ""),
            "details": check.get("details", {})
        })

        if check["status"] == "passed":
            report["passed"] += 1
        elif check["status"] == "failed":
            report["failed"] += 1
        else:
            report["warnings"] += 1

    # Write report
    with open("validation-report.json", "w") as f:
        json.dump(report, indent=2, fp=f)

    # Write summary
    print(f"\n{'='*60}")
    print(f"Validation Report - {report['timestamp']}")
    print(f"{'='*60}")
    print(f"Template Type: {report['template_type']}")
    print(f"✅ Passed:   {report['passed']}")
    print(f"❌ Failed:   {report['failed']}")
    print(f"⚠️  Warnings: {report['warnings']}")
    print(f"{'='*60}\n")

    return report["failed"] == 0
```

### 4. Use Validation Schemas

```python
# schemas/cookiecutter_schema.json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["project_name", "project_slug"],
  "properties": {
    "project_name": {
      "type": "string",
      "pattern": "^[A-Za-z][A-Za-z0-9 -]*$"
    },
    "project_slug": {
      "type": "string",
      "pattern": "^[a-z][a-z0-9-]*$"
    },
    "version": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$"
    }
  }
}
```

### 5. Progressive Enhancement

```bash
# validation_levels.sh

# Level 1: Basic syntax
validate_syntax() {
  echo "Level 1: Syntax validation"
  # Quick syntax checks
}

# Level 2: Structure
validate_structure() {
  echo "Level 2: Structure validation"
  # File structure and organization
}

# Level 3: Logic
validate_logic() {
  echo "Level 3: Logic validation"
  # Template logic and references
}

# Level 4: Security
validate_security() {
  echo "Level 4: Security validation"
  # Security scans and best practices
}

# Level 5: Performance
validate_performance() {
  echo "Level 5: Performance validation"
  # Performance and optimization checks
}

# Run all levels
validate_syntax && \
validate_structure && \
validate_logic && \
validate_security && \
validate_performance
```

## Related Skills

- **template-generation** - Generate templates that can be validated
- **template-customization** - Customize validated templates
- **infrastructure-deployment** - Deploy validated infrastructure
- **pipeline-management** - Manage validated pipelines
- **quality-assurance** - QA processes for templates

## Example Usage Scenarios

### Scenario 1: Pre-commit Validation

```bash
# User creates new Terraform module
terraform init
terraform fmt -recursive

# Validate before commit
claude validate terraform module

# Output:
# ✅ Format: Passed
# ✅ Init: Successful
# ✅ Validate: Passed
# ⚠️  Security: 2 warnings found
# ❌ Lint: 1 error found
#
# Fix required issues before committing
```

### Scenario 2: CI/CD Pipeline Validation

```bash
# In GitHub Actions
- name: Validate Templates
  run: claude validate template --type cookiecutter --ci-mode

# Output written to validation-report.json
# Exit code 1 if any validation fails
```

### Scenario 3: Interactive Validation

```bash
# User runs interactive validation
claude validate template

# Claude prompts:
# "Detected Terraform module. Run full validation? [Y/n]"
# "Format check passed ✅"
# "Security scan found 2 issues. View details? [Y/n]"
# "Would you like to auto-fix formatting? [Y/n]"
```

## Success Criteria

A template validation is successful when:

- ✅ All syntax checks pass
- ✅ No security vulnerabilities detected
- ✅ All referenced entities exist
- ✅ Documentation is complete
- ✅ Best practices are followed
- ✅ Test generation succeeds
- ✅ No linting errors
- ✅ Schema validation passes

## Notes

- Always validate before committing or deploying
- Use automation to catch errors early
- Keep validation scripts up to date
- Document validation failures and fixes
- Create custom validation rules for your organization
- Integrate validation into development workflow
- Monitor validation trends over time
- Share validation reports with team

---

**Version:** 1.0.0
**Last Updated:** 2026-01-19
**Maintainer:** Infrastructure Template Generator Plugin

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
