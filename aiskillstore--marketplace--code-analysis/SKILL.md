---
name: code-analysis
description: Check if code is readable by non-developers - clear names, plain English comments, no jargon Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Code Readability Checker

Analyzes code to ensure non-developers (managers, stakeholders, new team members) can understand it.

## What It Checks

- **Clear naming**: No cryptic abbreviations (usr_tkn → userToken)
- **Plain comments**: Everyday language, not technical jargon
- **Documentation**: What/Why/How for major sections
- **Comment ratio**: At least 20% of lines should be comments

## Usage

```bash
python3 analyze.py --path your-file.py --strictness lenient
```

## Example

**Bad Code** (score: 71/100):
```python
def proc(usr, tkn):
    tmp = usr + tkn
    return tmp * 2
```

Issues: Cryptic names, no comments, unclear purpose.

**Good Code** (score: 95/100):
```python
def process_user_authentication(username, auth_token):
    """Validate user credentials and return auth score"""
    combined_credential = username + auth_token
    return combined_credential * 2
```

## Known Issues

- May flag false positives in documentation files
- Works best on actual production code
- Use `--strictness lenient` to reduce noise

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
