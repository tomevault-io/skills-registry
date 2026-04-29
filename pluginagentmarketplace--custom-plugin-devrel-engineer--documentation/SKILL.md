---
name: documentation
description: Developer documentation including API references, guides, and getting started content Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Developer Documentation

Create **comprehensive, usable documentation** that helps developers succeed.

## Skill Contract

### Parameters
```yaml
parameters:
  required:
    - doc_type: enum[quickstart, tutorial, howto, concept, reference]
    - product_area: string
  optional:
    - api_spec: object
    - target_ttfhw: duration
```

### Output
```yaml
output:
  documentation:
    content: markdown
    code_samples: array[CodeBlock]
    navigation: object
```

## Documentation Types

| Type | Purpose | Example |
|------|---------|---------|
| **Quickstart** | First 5 minutes | "Hello World in 5 min" |
| **Tutorial** | Learn by doing | "Build your first app" |
| **How-to** | Solve specific task | "How to authenticate" |
| **Concept** | Understand system | "How caching works" |
| **Reference** | Look up details | "API endpoint list" |

## Information Architecture

```
Docs Site
├── Getting Started
│   ├── Quickstart
│   ├── Installation
│   └── First API Call
├── Guides
│   ├── Authentication
│   ├── Error Handling
│   └── Best Practices
├── API Reference
│   ├── Endpoints
│   ├── Parameters
│   └── Response Codes
└── Resources
    ├── FAQ
    ├── Changelog
    └── Migration Guides
```

## Writing Standards

### Structure Every Page
```markdown
# Page Title

Brief intro (what this covers, who it's for)

## Prerequisites
What they need before starting

## Main Content
Step-by-step or organized sections

## Next Steps
What to do after this
```

### Code Samples
- Complete, working examples
- Multiple language options
- Copy button on code blocks
- Show expected output

## Docs-as-Code

```
Write (MD) → Review (PR) → Build (CI) → Deploy (CD)
    ↓          ↓            ↓            ↓
  Local     GitHub       Docusaurus   Vercel
```

## Retry Logic

```yaml
retry_patterns:
  outdated_content:
    strategy: "Sync with latest API version"

  user_confusion:
    strategy: "Add examples, clarify language"

  broken_code_samples:
    strategy: "Test in CI, fix immediately"
```

## Failure Modes & Recovery

| Failure Mode | Detection | Recovery |
|--------------|-----------|----------|
| Outdated | API changed | Update with deprecation |
| Broken links | 404 errors | Fix or redirect |
| User can't complete | Feedback | Add missing steps |

## Debug Checklist

```
□ All code samples tested?
□ Prerequisites complete?
□ Links working?
□ Version numbers specified?
□ Screenshots current?
□ Follows style guide?
```

## Test Template

```yaml
test_documentation:
  unit_tests:
    - test_code_samples:
        assert: "All code runs"
    - test_links:
        assert: "No 404s"

  integration_tests:
    - test_ttfhw:
        assert: "<10 min to first success"
```

## Quality Metrics

| Metric | Target |
|--------|--------|
| Time to first success | <10 min |
| Doc search success | >80% |
| Ticket deflection | 60%+ |
| User satisfaction | >4.0/5 |

## Observability

```yaml
metrics:
  - pages_updated: integer
  - code_samples_tested: integer
  - user_satisfaction: float
  - search_success_rate: float
```

See `assets/` for documentation templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
