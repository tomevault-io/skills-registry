---
name: wordpress-legacy
description: WPCS, security, plugin development, and theme best practices. Use when this capability is needed.
metadata:
  author: sraloff
---

# WordPress Legacy

## When to use this skill
- Creating custom themes or plugins.
- Securing WordPress installations.
- Debugging WP sites.

## 1. Coding Standards
- **Naming**: Use `snake_case` for variables/functions (WordPress style).
- **Prefixing**: Prefix ALL functions/classes/globals with the plugin/theme slug (e.g., `mytheme_init`).
- **Escaping/Sanitizing**: MANDATORY.
  - Output: `esc_html()`, `esc_attr()`, `esc_url()`.
  - Input: `sanitize_text_field()`, `intval()`.

## 2. Performance
- **Transients**: Use transients (`set_transient`) for expensive queries.
- **Autoload**: Be careful with `wp_options` autoloading; clean up unused options.

## 3. Security
- **Nonce**: Verify nonces on all form submissions (`wp_verify_nonce`).
- **Capabilities**: Check user capabilities (`current_user_can`) before actions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
