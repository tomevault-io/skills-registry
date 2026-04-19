---
name: drupal-review
description: Drupal module code review checklist Use when this capability is needed.
metadata:
  author: nakamura196
---

## Drupal Module Review Checklist

### Drupal Best Practices
- [ ] Dependency Injection via constructor instead of \Drupal:: static calls
- [ ] Services properly declared in .services.yml with autowire or explicit arguments
- [ ] Config schema matches actual config structure
- [ ] Proper use of StringTranslationTrait / $this->t()
- [ ] Route parameters validated in routing.yml requirements

### Security
- [ ] User input sanitized (check_plain, Xss::filter, Html::escape)
- [ ] Access checks on all routes (_permission or _access)
- [ ] CSRF protection on state-changing endpoints
- [ ] Tokens/secrets not exposed in responses or logs
- [ ] No direct SQL queries (use Entity API / Database API with placeholders)

### Performance
- [ ] Cache tags/contexts used appropriately
- [ ] No unnecessary config loads in loops
- [ ] External API calls have timeouts set
- [ ] No blocking operations in hook implementations

### Code Quality
- [ ] Drupal coding standards (PSR-12 base + Drupal CS)
- [ ] Proper PHPDoc on all public methods
- [ ] Single responsibility per class
- [ ] Error handling with meaningful log messages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nakamura196) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
