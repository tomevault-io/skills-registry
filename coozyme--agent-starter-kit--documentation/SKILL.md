---
name: documentation
description: Skill for writing effective technical documentation Use when this capability is needed.
metadata:
  author: coozyme
---

# Documentation Skill

Guidelines for creating clear, useful, and maintainable documentation.

## When to Use This Skill

- When creating a new project or feature
- When existing documentation is missing or outdated
- When onboarding new team members
- When making architectural decisions

## Documentation Types

### 1. README.md
Every project must have a README with:
- **What**: Project description and purpose
- **Why**: Problem it solves
- **How**: Setup, installation, and running instructions
- **Contributing**: How to contribute
- **License**: Licensing information

### 2. API Documentation
- Endpoint description, method, URL
- Request parameters and body schema
- Response format with examples
- Error codes and messages
- Authentication requirements
- Use tools: Swagger/OpenAPI, Postman collections

### 3. Inline Documentation
- Use JSDoc (JS/TS), docstrings (Python), or GoDoc (Go)
- Document public functions, classes, and interfaces
- Focus on **why** and **contract**, not implementation
- Include parameter types, return types, and exceptions

```python
def calculate_discount(price: float, threshold: float) -> float:
    """Calculate discount for prices above threshold.

    Applies a 10% discount when the price exceeds the given threshold.

    Args:
        price: The original item price.
        threshold: The minimum price to qualify for discount.

    Returns:
        The discount amount. Returns 0 if price is below threshold.

    Raises:
        ValueError: If price or threshold is negative.
    """
```

### 4. Architecture Decision Records (ADRs)
Document significant decisions using the template at `templates/adr-template.md`:
- Context and problem statement
- Decision made
- Consequences and trade-offs
- Alternatives considered

### 5. Changelog
- Follow [Keep a Changelog](https://keepachangelog.com/) format
- Categories: Added, Changed, Deprecated, Removed, Fixed, Security
- Link to relevant PRs/issues

## Documentation Quality Checklist

- [ ] Accurate and up-to-date
- [ ] Written for the target audience
- [ ] Includes working code examples
- [ ] Has clear structure with headings
- [ ] Tested (commands and code snippets actually work)
- [ ] Free of jargon or jargon is defined
- [ ] Includes diagrams where helpful

---
> Source: [coozyme/agent-starter-kit](https://github.com/coozyme/agent-starter-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
