---
name: review
description: Code review for files or recent changes Use when this capability is needed.
metadata:
  author: karimatayuta
---

# Code Review

Perform a structured code review for the neo4j-graph-rag Python project.

## Scope Detection

Determine what to review based on the user's request:

1. **Specific file**: User names a file path -> review that file
2. **Recent changes**: "review changes" or "review diff" -> `git diff`
3. **Staged changes**: "review staged" -> `git diff --cached`
4. **A branch**: User names a branch -> `git diff main...<branch>`
5. **No argument**: review all uncommitted changes

## Project Conventions

### Code Style
- Type hints on all function parameters and return types
- Classes use PascalCase, functions/methods use snake_case
- Imports organized: stdlib, third-party, local (blank lines between groups)
- `TYPE_CHECKING` guard for type-only imports

### Architecture Patterns
- **Repository pattern**: Data access through `src/neo4j_graph_rag/repository/` submodules (graphdb/, embedding/, llm/, graph_ingestion/)
- **Pydantic models**: All data structures in `models/` use Pydantic v2
- **Settings**: Environment config via `pydantic-settings` in `config.py`
- **Lazy imports**: Heavy libraries (litellm, torch, sentence-transformers) imported inside functions, not at module top level

### Neo4j Specifics
- Cypher queries use parameterized queries (`$param` syntax, not f-strings)
- Connection management uses context managers
- Graph schema: Object, Slide, Group nodes with DEPENDS_ON, MEMBER_OF, ON_SLIDE, HAS_GROUP edges

### Testing Conventions
- Test methods use Japanese names describing behavior (e.g., `test_最小スライドを作成できる`)
- Each test has an English docstring
- `@pytest.mark.slow` / `@pytest.mark.external` markers
- Fixtures in `conftest.py` or within test classes

## Review Checklist

### Correctness
- Logic is correct and handles edge cases
- Error handling is appropriate
- Resource cleanup uses context managers or try/finally

### Security
- No hardcoded credentials or API keys
- No Cypher injection vulnerabilities
- `.env` values not logged or exposed

### Performance
- No N+1 query patterns with Neo4j
- Heavy imports are lazy
- Embedding operations use batch methods where possible

### Maintainability
- Functions are focused (single responsibility)
- Type hints present and accurate
- No dead code or commented-out blocks
- Consistent naming with existing codebase

### Testing
- New functionality has corresponding tests
- Tests follow project conventions
- Test isolation (no external state dependency for unit tests)

## Output Format

1. **Summary**: One-paragraph overview of what the code does and overall quality
2. **Issues** (by severity):
   - **Critical**: Bugs, security issues, data corruption risks
   - **Important**: Missing error handling, performance problems, missing types
   - **Suggestion**: Style improvements, potential refactors
3. **Positive**: What is done well
4. **Specific recommendations**: Concrete code suggestions for issues found

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karimatayuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
