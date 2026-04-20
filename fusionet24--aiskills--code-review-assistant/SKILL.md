---
name: code-review-assistant
description: Activates when reviewing code to identify quality issues, security vulnerabilities, and suggest improvements Use when this capability is needed.
metadata:
  author: fusionet24
---

# Code Review Assistant

## Overview
This skill helps conduct thorough code reviews by analyzing code structure, identifying potential issues, and suggesting improvements. It focuses on code quality, security, performance, and maintainability.

## Instructions
When reviewing code, follow these steps:

1. **Understand Context**: Review the purpose and scope of the code changes
2. **Analyze Structure**: Examine the overall architecture and organization
3. **Check Quality**: Look for code smells, anti-patterns, and violations of best practices
4. **Security Review**: Identify potential security vulnerabilities
5. **Performance**: Consider performance implications
6. **Suggest Improvements**: Provide actionable feedback with examples

## Review Checklist
- [ ] Code follows consistent style and conventions
- [ ] Functions and variables have clear, descriptive names
- [ ] Logic is clear and not overly complex
- [ ] Error handling is appropriate
- [ ] No security vulnerabilities (SQL injection, XSS, etc.)
- [ ] No hardcoded secrets or credentials
- [ ] Performance considerations addressed
- [ ] Edge cases handled
- [ ] Tests included (if applicable)
- [ ] Documentation is clear

## Examples

### Example 1: Identifying Security Issue
**Input:**
```python
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    return db.execute(query)
```

**Output:**
```
⚠️ Security Issue: SQL Injection Vulnerability

The current code is vulnerable to SQL injection attacks because it uses string formatting to build the SQL query.

Recommended fix:
```python
def get_user(user_id):
    query = "SELECT * FROM users WHERE id = ?"
    return db.execute(query, (user_id,))
```

### Example 2: Code Quality Improvement
**Input:**
```javascript
function calc(a, b, op) {
    if (op == 'add') return a + b;
    if (op == 'sub') return a - b;
    if (op == 'mul') return a * b;
    if (op == 'div') return a / b;
}
```

**Output:**
```
💡 Improvement: Use more descriptive names and handle edge cases

Suggested refactoring:
```javascript
function calculate(operand1, operand2, operation) {
    const operations = {
        'add': (a, b) => a + b,
        'subtract': (a, b) => a - b,
        'multiply': (a, b) => a * b,
        'divide': (a, b) => {
            if (b === 0) throw new Error('Division by zero');
            return a / b;
        }
    };
    
    if (!operations[operation]) {
        throw new Error(`Unknown operation: ${operation}`);
    }
    
    return operations[operation](operand1, operand2);
}
```

## Notes
- Prioritize security issues and critical bugs
- Be constructive and specific in feedback
- Provide examples when suggesting improvements
- Consider the context and constraints of the project
- Balance thoroughness with practicality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fusionet24) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
