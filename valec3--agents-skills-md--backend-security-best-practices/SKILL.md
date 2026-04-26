---
name: backend-security-best-practices
description: SQL injection prevention, XSS, CSRF, validation. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend Security Best Practices

## When to use this skill
- All application development
- Security reviews
- Penetration testing prep
- Compliance requirements

## Workflow
- [ ] Validate all input
- [ ] Use prepared statements
- [ ] Escape output
- [ ] Enable CSRF protection
- [ ] Use HTTPS

## Instructions

### SQL Injection Prevention
```php
<?php

// ❌ Bad
$sql = "SELECT * FROM users WHERE email = '{$email}'";

// ✅ Good: Query builder
$this->db->table('users')->where('email', $email)->get();

// ✅ Good: Prepared statement
$this->db->query("SELECT * FROM users WHERE email = ?", [$email]);
```

### XSS Prevention
```php
<?php

// In views
<?= esc($user->name) ?> // HTML escape
<?= esc($user->bio, 'js') ?> // JavaScript escape
```

### CSRF Protection
```php
<?php

// Config/Security.php
public $csrfProtection = 'session';

// In forms
<?= csrf_field() ?>

// In AJAX
headers: {
    'X-CSRF-TOKEN': '<?= csrf_hash() ?>'
}
```

### Environment Variables
```php
<?php

// ❌ Bad
$apiKey = 'hardcoded-key';

// ✅ Good
$apiKey = env('API_KEY');
```

## Resources
- Validate and sanitize all input
- Never trust user data
- Use framework security features
- Keep dependencies updated

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
