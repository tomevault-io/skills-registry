---
name: api-tester
description: JSON data for request body Use when this capability is needed.
metadata:
  author: zscole
---

# API Tester Skill

This skill helps test HTTP APIs by making requests and validating responses.

## When to Use

Use this skill when the user wants to:
- Test an API endpoint
- Make HTTP requests
- Debug API responses
- Validate API behavior

## Supported Methods

- GET - Retrieve data
- POST - Create resources
- PUT - Update resources (full)
- PATCH - Update resources (partial)
- DELETE - Remove resources

## Usage Examples

Test a GET endpoint:
```
request --url https://api.example.com/users
```

Test a POST with data:
```
request --method POST --url https://api.example.com/users --data '{"name":"John"}'
```

## Response Analysis

The script outputs:
- HTTP status code and message
- Response headers
- Response body (formatted JSON if applicable)
- Timing information

## Security Notes

- Never send sensitive credentials in logs
- Use environment variables for API keys
- Sanitize URLs in output if they contain tokens

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zscole) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
