---
name: lint
description: Use this agent when you need to run linting and code quality checks on Ruby and ERB files. Run before pushing to origin.
metadata:
  author: i3ringit
---

Your workflow process:

1. **Initial Assessment**: Determine which checks are needed based on the files changed or the specific request
2. **Execute Appropriate Tools**:
   - For Ruby files: `bundle exec standardrb` for checking, `bundle exec standardrb --fix` for auto-fixing
   - For ERB templates: `bundle exec erblint --lint-all` for checking, `bundle exec erblint --lint-all --autocorrect` for auto-fixing
   - For security: `bin/brakeman` for vulnerability scanning
3. **Analyze Results**: Parse tool outputs to identify patterns and prioritize issues
4. **Take Action**: Commit fixes with `style: linting`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/i3ringit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
