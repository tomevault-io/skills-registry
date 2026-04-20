---
name: docs
description: Writing clear, maintainable documentation for code, APIs, and vault documents Use when this capability is needed.
metadata:
  author: bleuropa
---

# Documentation Writing Skill

Guidelines for writing clear, useful documentation that stays current and serves both humans and AI agents.

## Documentation Types

### 1. Code Comments

**When to comment**:
- Why (not what) - Explain reasoning
- Complex algorithms
- Non-obvious solutions
- Workarounds for bugs

**When NOT to comment**:
- Obvious code
- What code does (code shows this)
- Redundant information

```javascript
// Bad
counter++ // Increment counter by 1

// Good
// Use exponential backoff to avoid overwhelming the API
await retryWithBackoff(apiCall)
```

### 2. Function Documentation

```javascript
/**
 * Calculate compound interest for an investment.
 *
 * @param principal - Initial investment amount
 * @param rate - Annual interest rate (decimal)
 * @param years - Number of years
 * @returns Final amount including principal and interest
 */
function calculateCompoundInterest(principal, rate, years) {
  return principal * Math.pow(1 + rate, years)
}
```

### 3. API Documentation

For each endpoint document:
- Purpose and use case
- Authentication requirements
- Request format
- Response format
- Possible errors
- Examples

### 4. README Files

Essential sections:
- Brief description
- Quick Start
- What It Does
- Key Features
- Documentation links
- Tech Stack
- License

### 5. Vault Documents

**Evergreen docs** (product/, architecture/, features/):
- Describe current state
- No "will", "soon", "planned"
- Update when reality changes

**PM docs** (pm/):
- Track progress and status
- Temporal language OK
- Link to evergreen docs

## Writing Style

### Be Concise

```
Bad:  "The function that we have implemented here is designed
      for the purpose of taking into consideration..."

Good: "Calculates compound interest."
```

### Use Active Voice

```
Bad:  "The data is processed by the server"
Good: "The server processes the data"
```

### Be Specific

```
Bad:  "Update the thing when needed"
Good: "Update the cache when user preferences change"
```

### Use Examples

Show, don't just tell with code examples.

## Structuring Documents

### Use Headings Hierarchy

```markdown
# Main Title (H1) - One per document
## Major Section (H2)
### Subsection (H3)
```

### Use Lists for Scanability

Instead of walls of text, break into bullet points.

## Vault Patterns

### Atomic Notes

One concept per file with rich internal linking.

### Evergreen vs Temporal

- **Evergreen**: Describe current state, no temporal language
- **Temporal**: Track progress, temporal language OK

## Documentation Checklist

- [ ] Clear purpose/overview
- [ ] Examples included
- [ ] Technical details accurate
- [ ] Links work
- [ ] Code samples tested
- [ ] Accessible to target audience

## Common Mistakes

Don't:
- Write docs that duplicate code
- Use jargon without explanation
- Skip examples
- Let docs get outdated

Do:
- Explain "why" not just "what"
- Define terms clearly
- Show concrete examples
- Keep docs current

## Remember

- Documentation is code
- Update docs with features
- Write for humans AND agents
- Examples are valuable
- Less is more (be concise)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bleuropa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
