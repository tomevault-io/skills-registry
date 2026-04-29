---
name: technical-writing
description: Technical writing skills for blogs, documentation, and developer content Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Technical Writing for DevRel

Create **clear, effective technical content** for developer audiences.

## Skill Contract

### Parameters
```yaml
parameters:
  required:
    - content_type: enum[blog, docs, tutorial, api_reference]
    - topic: string
  optional:
    - word_count_target: integer
    - seo_keywords: array[string]
    - code_language: string
```

### Output
```yaml
output:
  content:
    title: string
    body: markdown
    meta_description: string
  quality:
    readability_score: float
    code_tested: boolean
```

## Writing Principles

### Clarity First
```
Bad:  "The utilization of the aforementioned methodology..."
Good: "Use this method to..."

Bad:  "It is recommended that developers should..."
Good: "You should..."
```

### Structure for Scanning
```markdown
# Main Topic (H1 - one per page)

Brief intro paragraph explaining what and why.

## Section (H2 - major topics)

One paragraph context.

### Subsection (H3 - specific items)

- Bullet points for lists
- Code blocks for examples
- Tables for comparisons
```

## Content Types

| Type | Purpose | Structure |
|------|---------|-----------|
| **Tutorial** | Teach task | Step-by-step |
| **How-to** | Solve problem | Problem→Solution |
| **Concept** | Explain idea | What→Why→How |
| **Reference** | Look up info | Alphabetical/API |

## Blog Post Formula

```
1. Hook (problem/question)
2. Promise (what they'll learn)
3. Context (why it matters)
4. Content (the meat)
5. Examples (code/demos)
6. Summary (key takeaways)
7. CTA (what to do next)
```

## SEO for Technical Content

- Keyword in title and H1
- Keywords in first paragraph
- Meta description (150-160 chars)
- Internal linking
- Code snippets for featured snippets

## Retry Logic

```yaml
retry_patterns:
  code_sample_fails:
    strategy: "Test in clean environment"
    fallback: "Add version requirements note"

  unclear_explanation:
    strategy: "Rewrite with example first"

  seo_not_ranking:
    strategy: "Audit keywords, improve title"
```

## Failure Modes & Recovery

| Failure Mode | Detection | Recovery |
|--------------|-----------|----------|
| Code doesn't work | Reader reports | Hotfix, add version note |
| Outdated info | API changed | Update + deprecation notice |
| Poor engagement | Low time on page | Rewrite hook |

## Debug Checklist

```
□ Code samples tested?
□ Links verified?
□ Images have alt text?
□ Consistent terminology?
□ Spell check completed?
□ Mobile-friendly formatting?
□ Meta description ready?
□ Clear CTA at end?
```

## Test Template

```yaml
test_technical_writing:
  unit_tests:
    - test_code_compiles:
        assert: "Code runs without errors"
    - test_links_valid:
        assert: "200 OK response"

  integration_tests:
    - test_reader_journey:
        assert: "Achieves stated outcome"
```

## Quality Metrics

| Metric | Target |
|--------|--------|
| Readability | Flesch 60+ |
| Code accuracy | 100% working |
| Time on page | >3 min |

## Observability

```yaml
metrics:
  - word_count: integer
  - code_blocks_count: integer
  - reading_time: duration
```

See `assets/` for writing templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
