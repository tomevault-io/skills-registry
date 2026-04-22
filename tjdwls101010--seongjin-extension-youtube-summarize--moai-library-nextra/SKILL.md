---
name: moai-library-nextra
description: Enterprise Nextra documentation framework with Next.js Use when this capability is needed.
metadata:
  author: tjdwls101010
---

## Advanced Documentation

This Skill uses Progressive Disclosure. For detailed patterns:

- [modules/configuration.md](modules/configuration.md) - Complete theme.config reference
- [modules/mdx-components.md](modules/mdx-components.md) - MDX component library
- [modules/i18n-setup.md](modules/i18n-setup.md) - Internationalization guide
- [modules/deployment.md](modules/deployment.md) - Hosting & deployment

---

## Theme Options

Built-in Themes:

- nextra-theme-docs (recommended for documentation)
- nextra-theme-blog (for blogs)

Customization:

- CSS variables for colors
- Custom sidebar components
- Footer customization
- Navigation layout

---

## Deployment

Popular Platforms:

- Vercel (zero-config, recommended)
- GitHub Pages (free, self-hosted)
- Netlify (flexible, CI/CD)
- Custom servers (full control)

Vercel Deployment:

```bash
npm install -g vercel
vercel
# Select project and deploy
```

---

## Integration with Other Skills

Complementary Skills:

- Skill("moai-docs-generation") - Auto-generate docs from code
- Skill("moai-workflow-docs") - Validate documentation quality
- Skill("moai-cc-claude-md") - Markdown formatting

---

## Version History

1.0.1 (2025-11-23)

- Refactored with Progressive Disclosure
- Configuration patterns highlighted
- MDX integration guide

1.0.0 (2025-11-12)

- Nextra architecture guide
- Theme configuration
- i18n support

---

Maintained by: alfred
Domain: Documentation Architecture
Generated with: MoAI-ADK Skill Factory

---

## Works Well With

Agents:
- workflow-docs - Documentation generation
- code-frontend - Nextra implementation
- workflow-spec - Architecture documentation

Skills:
- moai-docs-generation - Content generation
- moai-workflow-docs - Documentation validation
- moai-library-mermaid - Diagram integration

Commands:
- `/moai:3-sync` - Documentation deployment
- `/moai:0-project` - Nextra project initialization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjdwls101010) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
