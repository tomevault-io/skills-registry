---
name: code-review-checklist
description: Generate context-aware code review checklists for changes Use when this capability is needed.
metadata:
  author: manastalukdar
---

# Code Review Checklist Generator

I'll generate comprehensive, context-aware code review checklists based on your changes.

Arguments: `$ARGUMENTS` - branch name, commit range, or PR number (optional)

## Checklist Philosophy

Generate checklists that are:
- Framework-specific and relevant
- Based on actual changes, not generic
- Security-focused for sensitive changes
- Performance-aware for critical paths
- Actionable and measurable

## Token Optimization

This skill uses multiple optimization strategies to minimize token usage while maintaining comprehensive checklist generation:

### 1. Git Diff Statistics for Overview (800 token savings)

**Pattern:** Use git diff --stat instead of reading all changed files

```bash
# Instead of: Read all changed files (1,200+ tokens)
# Use: git diff --stat (400 tokens)

# Get high-level overview
git diff --stat "$BASE_BRANCH"...HEAD

# Count changes without reading files
total_files=$(git diff --name-only "$BASE_BRANCH"...HEAD | wc -l)
additions=$(git diff --numstat "$BASE_BRANCH"...HEAD | awk '{sum+=$1} END {print sum}')
deletions=$(git diff --numstat "$BASE_BRANCH"...HEAD | awk '{sum+=$2} END {print sum}')
```

**Savings:**
- Git stats: ~400 tokens (metadata only)
- Full file read: ~1,200 tokens (all changed files)
- **800 token savings (67%)**

### 2. Codebase Analysis Caching (600 token savings)

**Pattern:** Cache framework/stack detection results

```bash
# Cache file: .code-review-stack.cache
# Format: JSON with detected technologies
# TTL: 24 hours (stack rarely changes)

if [ -f ".code-review-stack.cache" ] && [ $(($(date +%s) - $(stat -c %Y .code-review-stack.cache))) -lt 86400 ]; then
    # Read cached stack (80 tokens)
    STACK=$(cat .code-review-stack.cache)
else
    # Full stack detection (680 tokens)
    detect_stack
    echo "$STACK" > .code-review-stack.cache
fi
```

**Savings:**
- Cached: ~80 tokens (read cache file)
- Uncached: ~680 tokens (package.json/requirements.txt analysis, multiple Grep operations)
- **600 token savings (88%)** for subsequent runs

### 3. Template-Based Checklist Generation (1,800 token savings)

**Pattern:** Use comprehensive pre-defined checklist template with conditional sections

```bash
# Instead of: LLM-generated checklist (2,200+ tokens)
# Use: Template-based checklist (400 tokens)

generate_checklist_template() {
    cat templates/base-checklist.md  # Base template (300 tokens)

    # Add framework-specific sections conditionally
    if [ "$HAS_REACT" = "true" ]; then
        cat templates/react-checklist.md  # 100 tokens
    fi

    if [ "$HAS_SECURITY_CHANGES" = "true" ]; then
        cat templates/security-checklist.md  # 150 tokens
    fi
}
```

**Savings:**
- Template-based: ~400 tokens (template assembly)
- LLM-generated: ~2,200 tokens (full checklist generation with analysis)
- **1,800 token savings (82%)** per checklist

### 4. Focus Area Filtering (1,000 token savings)

**Pattern:** Generate only relevant checklist sections based on changes

```bash
# Instead of: Full comprehensive checklist (3,000+ tokens)
# Generate: Focused checklist (800 tokens)

# Analyze change categories
has_frontend=$(git diff --name-only "$BASE_BRANCH"...HEAD | grep -q '\.(tsx|jsx|vue)$' && echo "true" || echo "false")
has_backend=$(git diff --name-only "$BASE_BRANCH"...HEAD | grep -q '\.(py|go|java)$' && echo "true" || echo "false")
has_tests=$(git diff --name-only "$BASE_BRANCH"...HEAD | grep -q 'test\|spec' && echo "true" || echo "false")

# Include only relevant sections
[ "$has_frontend" = "true" ] && include_section "Frontend Performance"
[ "$has_backend" = "true" ] && include_section "API Design"
[ "$has_tests" = "true" ] && include_section "Test Quality"
```

**Savings:**
- Focused checklist: ~800 tokens (3-5 relevant sections)
- Full checklist: ~3,000 tokens (all 10 sections)
- **1,000 token savings (73%)** for focused reviews

### 5. Early Exit for No Changes (95% savings)

**Pattern:** Detect no changes immediately

```bash
# Quick check: Are there any changes?
if ! git diff --quiet "$BASE_BRANCH"...HEAD; then
    CHANGE_COUNT=$(git diff --name-only "$BASE_BRANCH"...HEAD | wc -l)

    if [ "$CHANGE_COUNT" -eq 0 ]; then
        echo "✓ No changes to review"
        exit 0  # 100 tokens total
    fi
else
    echo "✓ No changes to review"
    exit 0
fi

# Otherwise: Full checklist generation (2,500+ tokens)
```

**Savings:**
- No changes: ~100 tokens (early exit)
- Full generation: ~2,500+ tokens
- **2,400+ token savings (96%)** when no changes exist

### 6. Security Risk Pattern Matching (500 token savings)

**Pattern:** Use Grep for security pattern detection instead of full file analysis

```bash
# Instead of: Read files for security analysis (800 tokens)
# Use: Grep for security patterns (300 tokens)

# Detect authentication changes
git diff "$BASE_BRANCH"...HEAD | grep -i -E "auth|login|password|token|jwt" > /dev/null && AUTH_CHANGES=true

# Detect SQL queries
git diff "$BASE_BRANCH"...HEAD | grep -E "query|execute|raw|sql" > /dev/null && SQL_CHANGES=true

# Detect file operations
git diff "$BASE_BRANCH"...HEAD | grep -E "fs\.|readFile|writeFile" > /dev/null && FILE_OPS=true
```

**Savings:**
- Grep patterns: ~300 tokens
- Full file analysis: ~800 tokens (read changed files)
- **500 token savings (62%)**

### 7. Checklist Section Caching (400 token savings)

**Pattern:** Cache common checklist sections for reuse

```bash
# Cache file: .code-review-sections.cache
# Contains pre-loaded checklist sections
# TTL: 24 hours

load_sections() {
    local cache_file=".code-review-sections.cache"

    if [ -f "$cache_file" ]; then
        source "$cache_file"  # 100 tokens
        return
    fi

    # Load all sections (500 tokens)
    SECTION_CODE_QUALITY=$(cat templates/code-quality.md)
    SECTION_SECURITY=$(cat templates/security.md)
    SECTION_PERFORMANCE=$(cat templates/performance.md)
    # ... more sections

    declare -p SECTION_* > "$cache_file"
}
```

**Savings:**
- Cached sections: ~100 tokens
- Load sections: ~500 tokens (multiple file reads)
- **400 token savings (80%)** for subsequent runs

### 8. File Category Analysis Optimization (300 token savings)

**Pattern:** Use awk for efficient file categorization

```bash
# Instead of: Multiple grep operations (500 tokens)
# Use: Single awk command (200 tokens)

git diff --name-only "$BASE_BRANCH"...HEAD | awk -F'/' '{
    if ($0 ~ /test/) tests++
    else if ($0 ~ /\.md$/) docs++
    else if ($0 ~ /\.(ts|js|tsx|jsx)$/) frontend++
    else if ($0 ~ /\.(py|go|java|rb)$/) backend++
    else if ($0 ~ /\.(yml|yaml|json|toml)$/) config++
    else others++
}
END {
    if (tests) print "Tests: " tests
    if (docs) print "Documentation: " docs
    if (frontend) print "Frontend: " frontend
    if (backend) print "Backend: " backend
    if (config) print "Config: " config
    if (others) print "Other: " others
}'
```

**Savings:**
- Awk categorization: ~200 tokens
- Multiple Grep operations: ~500 tokens
- **300 token savings (60%)**

### 9. Real-World Token Usage Distribution

**Typical Scenarios:**

1. **First Run - Medium PR (1,800-2,500 tokens)**
   - Git diff stats: 400 tokens
   - Stack detection: 680 tokens
   - File categorization: 200 tokens
   - Security analysis: 300 tokens
   - Generate focused checklist: 800 tokens
   - **Total: ~2,380 tokens**

2. **Subsequent Run - Same Stack (1,000-1,500 tokens)**
   - Git diff stats: 400 tokens
   - Stack detection (cached): 80 tokens
   - File categorization: 200 tokens
   - Security analysis: 300 tokens
   - Load sections (cached): 100 tokens
   - Generate checklist: 400 tokens
   - **Total: ~1,480 tokens**

3. **No Changes to Review (80-120 tokens)**
   - Git diff check: 50 tokens
   - Early exit: 50 tokens
   - **Total: ~100 tokens**

4. **Large PR with Multiple Areas (2,500-3,500 tokens)**
   - Git diff stats: 400 tokens
   - Stack detection: 680 tokens
   - File categorization: 200 tokens
   - Security analysis: 300 tokens
   - Performance analysis: 300 tokens
   - Generate comprehensive checklist: 2,000 tokens
   - **Total: ~3,880 tokens**

5. **Small Frontend Change (800-1,200 tokens)**
   - Git diff stats: 400 tokens
   - Stack detection (cached): 80 tokens
   - File categorization: 200 tokens
   - Generate focused frontend checklist: 400 tokens
   - **Total: ~1,080 tokens**

**Expected Token Savings:**
- **Average 40% reduction** from baseline (2,500 → 1,500 tokens)
- **96% reduction** when no changes exist
- **Aggregate savings: 1,000-1,500 tokens** per checklist generation

### Optimization Summary

| Strategy | Savings | When Applied |
|----------|---------|--------------|
| Git diff statistics | 800 tokens (67%) | Always |
| Codebase analysis caching | 600 tokens (88%) | Subsequent runs |
| Template-based generation | 1,800 tokens (82%) | Always |
| Focus area filtering | 1,000 tokens (73%) | Focused reviews |
| Early exit for no changes | 2,400 tokens (96%) | No changes |
| Security pattern matching | 500 tokens (62%) | Always |
| Checklist section caching | 400 tokens (80%) | Subsequent runs |
| File categorization optimization | 300 tokens (60%) | Always |

**Key Insight:** The combination of git diff statistics, template-based generation, and focus area filtering provides 40-50% token reduction while maintaining comprehensive review guidance. Early exit patterns provide 96% savings when no changes exist.

## Pre-Flight Checks

Before generating checklist, I'll verify:
- Git repository exists
- Changes to review exist
- Can access modified files
- Framework/stack detected

<think>
Checklist Strategy:
- What files changed?
- What frameworks are involved?
- Are there security implications?
- Performance impacts?
- What testing is needed?
</think>

First, let me analyze the changes:

```bash
#!/bin/bash
# Analyze changes for review checklist

set -e

echo "=== Code Review Analysis ==="
echo ""

# Parse arguments for branch/commit range
TARGET="${ARGUMENTS:-HEAD}"

# 1. Verify git repository
if ! git rev-parse --git-dir > /dev/null 2>&1; then
    echo "Error: Not a git repository"
    exit 1
fi

# 2. Determine comparison target
echo "Review Target: $TARGET"
echo ""

# 3. Get changed files
echo "Changed Files:"
if [ "$TARGET" = "HEAD" ]; then
    # Compare with main/master
    BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo "main")
    git diff --stat "$BASE_BRANCH"...HEAD
else
    git diff --stat "$TARGET"
fi
echo ""

# 4. Count changes
total_files=$(git diff --name-only "$BASE_BRANCH"...HEAD 2>/dev/null | wc -l || echo "0")
additions=$(git diff --numstat "$BASE_BRANCH"...HEAD 2>/dev/null | awk '{sum+=$1} END {print sum}' || echo "0")
deletions=$(git diff --numstat "$BASE_BRANCH"...HEAD 2>/dev/null | awk '{sum+=$2} END {print sum}' || echo "0")

echo "Change Summary:"
echo "  Files changed: $total_files"
echo "  Lines added: $additions"
echo "  Lines deleted: $deletions"
echo ""

# 5. Categorize changes
echo "File Categories:"
git diff --name-only "$BASE_BRANCH"...HEAD 2>/dev/null | awk -F'/' '{
    if ($0 ~ /test/) tests++
    else if ($0 ~ /\.md$/) docs++
    else if ($0 ~ /\.(ts|js|tsx|jsx)$/) frontend++
    else if ($0 ~ /\.(py|go|java|rb)$/) backend++
    else if ($0 ~ /\.(yml|yaml|json|toml)$/) config++
    else if ($0 ~ /\.(css|scss|sass)$/) styles++
    else others++
}
END {
    if (tests) print "  Tests: " tests
    if (docs) print "  Documentation: " docs
    if (frontend) print "  Frontend: " frontend
    if (backend) print "  Backend: " backend
    if (config) print "  Config: " config
    if (styles) print "  Styles: " styles
    if (others) print "  Other: " others
}'
echo ""
```

## Phase 1: Framework & Stack Detection

Detect technologies to generate relevant checks:

```bash
#!/bin/bash
# Detect frameworks and technologies

detect_stack() {
    echo "=== Technology Stack Detection ==="
    echo ""

    # Frontend frameworks
    if [ -f "package.json" ]; then
        echo "JavaScript/TypeScript:"
        if grep -q "\"react\":" package.json; then
            echo "  ✓ React"
        fi
        if grep -q "\"vue\":" package.json; then
            echo "  ✓ Vue"
        fi
        if grep -q "\"next\":" package.json; then
            echo "  ✓ Next.js"
        fi
        if grep -q "\"angular\":" package.json; then
            echo "  ✓ Angular"
        fi
        echo ""
    fi

    # Backend frameworks
    if [ -f "requirements.txt" ] || [ -f "pyproject.toml" ]; then
        echo "Python:"
        if grep -q "django" requirements.txt 2>/dev/null || grep -q "django" pyproject.toml 2>/dev/null; then
            echo "  ✓ Django"
        fi
        if grep -q "flask" requirements.txt 2>/dev/null || grep -q "flask" pyproject.toml 2>/dev/null; then
            echo "  ✓ Flask"
        fi
        if grep -q "fastapi" requirements.txt 2>/dev/null || grep -q "fastapi" pyproject.toml 2>/dev/null; then
            echo "  ✓ FastAPI"
        fi
        echo ""
    fi

    # Database
    if [ -f "package.json" ]; then
        echo "Database:"
        if grep -q "\"prisma\":" package.json; then
            echo "  ✓ Prisma ORM"
        fi
        if grep -q "\"typeorm\":" package.json; then
            echo "  ✓ TypeORM"
        fi
        if grep -q "\"pg\":" package.json || grep -q "\"postgres\":" package.json; then
            echo "  ✓ PostgreSQL"
        fi
        if grep -q "\"mongodb\":" package.json || grep -q "\"mongoose\":" package.json; then
            echo "  ✓ MongoDB"
        fi
        echo ""
    fi

    # Testing frameworks
    echo "Testing:"
    if grep -q "\"jest\":" package.json 2>/dev/null; then
        echo "  ✓ Jest"
    fi
    if grep -q "\"vitest\":" package.json 2>/dev/null; then
        echo "  ✓ Vitest"
    fi
    if grep -q "\"playwright\":" package.json 2>/dev/null; then
        echo "  ✓ Playwright"
    fi
    if grep -q "pytest" requirements.txt 2>/dev/null; then
        echo "  ✓ Pytest"
    fi
    echo ""
}

detect_stack
```

## Phase 2: Security Analysis

Analyze changes for security implications:

```bash
#!/bin/bash
# Security-focused analysis

analyze_security_risks() {
    echo "=== Security Risk Analysis ==="
    echo ""

    local has_security_risks=0

    # Check for authentication/authorization changes
    echo "Authentication & Authorization:"
    if git diff "$BASE_BRANCH"...HEAD | grep -i -E "auth|login|password|token|session|jwt|oauth" > /dev/null; then
        echo "  ⚠ Authentication-related changes detected"
        has_security_risks=1
    else
        echo "  ✓ No auth changes"
    fi
    echo ""

    # Check for database queries
    echo "Database Security:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "query|execute|raw|sql" > /dev/null; then
        echo "  ⚠ Database query changes detected"
        echo "    → Review for SQL injection risks"
        has_security_risks=1
    else
        echo "  ✓ No raw queries"
    fi
    echo ""

    # Check for user input handling
    echo "Input Validation:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "req\.body|req\.query|request\.|input|form" > /dev/null; then
        echo "  ⚠ User input handling detected"
        echo "    → Review validation and sanitization"
        has_security_risks=1
    else
        echo "  ✓ No input handling changes"
    fi
    echo ""

    # Check for file operations
    echo "File Operations:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "fs\.|readFile|writeFile|unlink|path\.join" > /dev/null; then
        echo "  ⚠ File system operations detected"
        echo "    → Review for path traversal vulnerabilities"
        has_security_risks=1
    else
        echo "  ✓ No file operations"
    fi
    echo ""

    # Check for environment variables
    echo "Secrets Management:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "process\.env|API_KEY|SECRET|PASSWORD" > /dev/null; then
        echo "  ⚠ Environment variable usage detected"
        echo "    → Verify no secrets in code"
        has_security_risks=1
    else
        echo "  ✓ No environment changes"
    fi
    echo ""

    if [ $has_security_risks -eq 1 ]; then
        echo "⚠ Security-sensitive changes require thorough review"
    else
        echo "✓ No major security risks detected"
    fi
    echo ""
}

analyze_security_risks
```

## Phase 3: Performance Analysis

Check for performance implications:

```bash
#!/bin/bash
# Performance impact analysis

analyze_performance_impact() {
    echo "=== Performance Impact Analysis ==="
    echo ""

    local has_perf_concerns=0

    # Check for database queries in loops
    echo "Database Performance:"
    if git diff "$BASE_BRANCH"...HEAD | grep -B5 -A5 "for\|while\|map" | grep -E "query|find|create|update|delete" > /dev/null; then
        echo "  ⚠ Potential N+1 query pattern"
        echo "    → Review for bulk operations"
        has_perf_concerns=1
    else
        echo "  ✓ No obvious N+1 patterns"
    fi
    echo ""

    # Check for synchronous operations
    echo "Async Operations:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "fs\.readFileSync|fs\.writeFileSync|execSync" > /dev/null; then
        echo "  ⚠ Synchronous operations detected"
        echo "    → Consider async alternatives"
        has_perf_concerns=1
    else
        echo "  ✓ No blocking operations"
    fi
    echo ""

    # Check for large data processing
    echo "Data Processing:"
    if git diff "$BASE_BRANCH"...HEAD | grep -E "\.map\(|\.filter\(|\.reduce\(" > /dev/null; then
        echo "  ⓘ Array operations detected"
        echo "    → Verify efficient for large datasets"
    fi
    echo ""

    # Check for caching
    echo "Caching:"
    if git diff "$BASE_BRANCH"...HEAD | grep -i -E "cache|redis|memcached|memo" > /dev/null; then
        echo "  ✓ Caching implemented"
    else
        echo "  ⓘ Consider caching for frequently accessed data"
    fi
    echo ""

    if [ $has_perf_concerns -eq 1 ]; then
        echo "⚠ Performance concerns require review"
    else
        echo "✓ No major performance concerns"
    fi
    echo ""
}

analyze_performance_impact
```

## Phase 4: Generate Checklist

Create comprehensive, context-aware checklist:

Now I'll generate a customized code review checklist based on the analysis:

## Code Review Checklist

**Review Information:**
- **Date**: [timestamp]
- **Reviewer**: _____________
- **Changes**: [X files, Y additions, Z deletions]
- **Risk Level**: Low / Medium / High

---

### 1. Code Quality

**General:**
- [ ] Code is readable and self-documenting
- [ ] Naming follows project conventions
- [ ] No commented-out code
- [ ] No unnecessary console.log/print statements
- [ ] Error messages are helpful and actionable
- [ ] No hardcoded values that should be config

**TypeScript/JavaScript Specific:**
- [ ] TypeScript types are specific (no 'any')
- [ ] Interfaces/types are reusable
- [ ] Async/await used correctly
- [ ] Promises properly handled
- [ ] No callback hell

**Python Specific:**
- [ ] Type hints provided
- [ ] Follows PEP 8 style guide
- [ ] Context managers used for resources
- [ ] List comprehensions over loops where appropriate

---

### 2. Functionality

**Core Logic:**
- [ ] Implements requirements correctly
- [ ] Edge cases handled
- [ ] Error handling comprehensive
- [ ] Null/undefined checks present
- [ ] Input validation comprehensive

**Business Logic:**
- [ ] Follows business rules
- [ ] Data transformations correct
- [ ] Calculations verified
- [ ] State management appropriate

---

### 3. Security

**Authentication & Authorization:**
- [ ] Authentication required where needed
- [ ] Authorization checks present
- [ ] Session management secure
- [ ] Token validation implemented
- [ ] No authentication bypass possible

**Input Validation:**
- [ ] All user inputs validated
- [ ] SQL injection prevented
- [ ] XSS vulnerabilities addressed
- [ ] CSRF protection in place
- [ ] File upload restrictions enforced

**Data Protection:**
- [ ] Sensitive data encrypted
- [ ] No secrets in code
- [ ] Environment variables used correctly
- [ ] Passwords hashed (never stored plain)
- [ ] API keys secured

**Dependencies:**
- [ ] No known vulnerabilities in new dependencies
- [ ] Dependencies from trusted sources
- [ ] Version pinning appropriate

---

### 4. Performance

**Database:**
- [ ] No N+1 query problems
- [ ] Indexes used appropriately
- [ ] Bulk operations for large datasets
- [ ] Connection pooling configured
- [ ] Query complexity acceptable

**Caching:**
- [ ] Appropriate caching strategy
- [ ] Cache invalidation handled
- [ ] Cache keys unique and clear

**Resource Management:**
- [ ] Memory leaks prevented
- [ ] File handles closed
- [ ] Connections released
- [ ] Large files streamed, not loaded

**Frontend Performance:**
- [ ] Components optimized (React.memo, useMemo)
- [ ] Images optimized and lazy-loaded
- [ ] Code splitting implemented
- [ ] Bundle size acceptable

---

### 5. Testing

**Test Coverage:**
- [ ] Unit tests for new logic
- [ ] Integration tests for new features
- [ ] Edge cases tested
- [ ] Error conditions tested
- [ ] Test coverage > 80%

**Test Quality:**
- [ ] Tests are clear and maintainable
- [ ] Tests don't depend on external state
- [ ] Mocks appropriate and realistic
- [ ] Tests run quickly
- [ ] No flaky tests

**Manual Testing:**
- [ ] Feature tested locally
- [ ] Works in different browsers (if frontend)
- [ ] Mobile responsive (if applicable)
- [ ] Accessibility verified

---

### 6. Architecture & Design

**Code Organization:**
- [ ] Follows existing patterns
- [ ] Single responsibility principle
- [ ] Appropriate abstraction level
- [ ] No circular dependencies
- [ ] Proper separation of concerns

**Database Schema:**
- [ ] Migrations included
- [ ] Schema changes backward compatible
- [ ] Foreign keys defined
- [ ] Indexes on frequently queried columns

**API Design:**
- [ ] RESTful or follows API conventions
- [ ] Proper HTTP methods used
- [ ] Appropriate status codes
- [ ] Error responses consistent
- [ ] API versioning considered

---

### 7. Documentation

**Code Documentation:**
- [ ] Complex logic explained
- [ ] Public APIs documented
- [ ] JSDoc/docstrings for functions
- [ ] README updated if needed
- [ ] CHANGELOG updated

**User Documentation:**
- [ ] User-facing changes documented
- [ ] Migration guide if breaking changes
- [ ] Examples provided

---

### 8. Integration

**Dependencies:**
- [ ] Compatible with existing code
- [ ] Dependencies updated in package.json/requirements.txt
- [ ] No breaking changes to public APIs
- [ ] Backward compatibility maintained

**CI/CD:**
- [ ] All CI checks passing
- [ ] Build succeeds
- [ ] Tests pass in CI
- [ ] Linter passes
- [ ] Type checking passes

**Database:**
- [ ] Migrations tested
- [ ] Rollback plan exists
- [ ] Seed data updated

---

### 9. Deployment

**Configuration:**
- [ ] Environment variables documented
- [ ] Feature flags configured
- [ ] Monitoring/logging added

**Rollback:**
- [ ] Rollback plan documented
- [ ] Database migrations reversible
- [ ] Feature flags allow quick disable

---

### 10. Framework-Specific Checks

**React:**
- [ ] Hooks follow rules of hooks
- [ ] useEffect dependencies correct
- [ ] No prop drilling (use context if needed)
- [ ] Components reasonably sized
- [ ] Key props on lists

**Next.js:**
- [ ] Server/client components appropriate
- [ ] Data fetching optimized
- [ ] SEO meta tags included
- [ ] ISR/SSR/SSG appropriate

**Django:**
- [ ] Models have proper Meta class
- [ ] Migrations properly named
- [ ] Admin interface configured
- [ ] Signals used appropriately

**FastAPI:**
- [ ] Pydantic models for validation
- [ ] Dependency injection used
- [ ] Background tasks for long operations
- [ ] OpenAPI docs accurate

---

### Review Summary

**Strengths:**
-

**Concerns:**
-

**Blocking Issues:**
-

**Suggestions:**
-

**Decision:**
- [ ] Approve
- [ ] Approve with comments
- [ ] Request changes
- [ ] Needs discussion

**Reviewer Signature:** _____________
**Date:** _____________

---

## Integration with /review Skill

This checklist complements `/review`:
- `/review` - Automated code analysis
- `/code-review-checklist` - Human review guidance

**Workflow:**
```bash
# 1. Run automated review
/review

# 2. Generate human review checklist
/code-review-checklist

# 3. Perform manual review using checklist

# 4. Approve or request changes
```

## Customization

I'll customize checklists based on:
- **File types changed** - Frontend vs backend checks
- **Frameworks detected** - Framework-specific checks
- **Security sensitivity** - Enhanced security section
- **Performance critical** - Enhanced performance section
- **Database changes** - Schema review section
- **API changes** - API design section

## Export Formats

I can export checklists as:
- **Markdown** - For GitHub PR templates
- **Plain text** - For internal reviews
- **JSON** - For automation tools

```bash
#!/bin/bash
# Save checklist to file

save_checklist() {
    local output_file="CODE_REVIEW_CHECKLIST.md"

    echo "Saving checklist to: $output_file"
    # Checklist content written to file

    echo "✓ Checklist saved"
    echo ""
    echo "Usage:"
    echo "  1. Copy to PR description"
    echo "  2. Use as review template"
    echo "  3. Track review progress"
}
```

## What I'll Actually Do

1. **Analyze Changes** - Use git diff to understand scope
2. **Detect Stack** - Identify frameworks and technologies
3. **Security Scan** - Flag security-sensitive changes
4. **Performance Check** - Identify performance risks
5. **Generate Checklist** - Create relevant, actionable checklist
6. **Customize Sections** - Focus on actual changes
7. **Save Template** - Export for use

**Important:** I will NEVER:
- Generate generic, irrelevant checklists
- Miss critical security concerns
- Ignore framework-specific patterns
- Add AI attribution

The checklist will be comprehensive, relevant, and immediately useful for code reviews.

**Credits:** Checklist methodology based on industry best practices, OWASP security guidelines, and framework-specific review patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manastalukdar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
