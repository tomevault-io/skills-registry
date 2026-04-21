---
name: sensitive-data-leak-prevention
description: Prevents accidental leaks of secrets, credentials, and private data in the SulitFinds repo. Automates detection, removal, and documentation of sensitive values. Use when this capability is needed.
metadata:
  author: dibelelyrian
---

Use this skill to scan for and prevent sensitive data leaks in any code, content, or configuration change.

Core requirements:
- Never commit .env, .env.*, or secret files; ensure .gitignore includes these patterns
- No hardcoded credentials, API keys, tokens, passwords, or private values in any file
- Scan for common secret patterns: API_KEY, SECRET, TOKEN, PASSWORD, PRIVATE, SUPABASE, STRIPE, AWS, GCP, OPENAI, BEARER, AUTH, CLIENT_SECRET
- Remove any detected secrets immediately and replace with environment variables or safe placeholders
- Document all changes made to remove sensitive data

Operational steps:
1. On every code/content change, scan the repo for sensitive data patterns
2. Check .gitignore for proper exclusion of secret files
3. If any sensitive value is found: remove from the repo, replace with environment variables or placeholders, document what was changed and why
4. Confirm no sensitive data remains before publishing or pushing

Checklist:
- Regex search for secret patterns in all files
- Validate .gitignore includes .env, .env.*, and common secret filenames
- Flag any hardcoded credential or secret for immediate action
- Run as part of pre-publish and code review process

Exception handling:
- If a user requests to add a secret, require explicit approval and recommend using environment variables

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dibelelyrian) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
