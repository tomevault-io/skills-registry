---
name: security-design
description: Design security controls and threat mitigations. Use for features involving auth, data, or external exposure. Use when this capability is needed.
metadata:
  author: amattas
---

# Security Design

Identify threats and design appropriate security controls for a feature.

## Process

1. Identify assets to protect
2. Model potential threats
3. Define required controls
4. Specify data handling rules
5. Note compliance requirements

## Output

Create `security-requirements.md` using the template in `templates/security-requirements.md`.

## Tips

- Consider OWASP Top 10 threats
- Define what data is sensitive
- Specify authentication/authorization needs
- Document logging requirements (without sensitive data)
- Consider rate limiting and abuse prevention

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amattas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
