---
name: netlify-tool
description: Deploy web sites and manage Netlify sites Use when this capability is needed.
metadata:
  author: lex-1401
---

# Netlify Tool

Integrates with the `netlify-cli` to manage sites and deployments.

## Prerequisites

- Install Netlify CLI: `npm install -g netlify-cli`
- Login: `netlify login`

## Actions

### Deploy

Deploy the current site.

```bash
netlify deploy --prod
```

### List Sites

List sites connected to your account.

```bash
netlify sites:list
```

### Status

Check the status of the current site.

```bash
netlify status
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lex-1401) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
