---
name: workspace-setup
description: Initialize development environment for a project. Use when setting up a new project, onboarding a developer, or configuring dependencies. Use when this capability is needed.
metadata:
  author: hscspring
---

# Workspace Setup

Set up a consistent development environment.

## Common Setups

### Node.js
```bash
npm install
npm run dev
```

### Python
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Rust
```bash
cargo build
cargo test
```

### Docker
```bash
docker-compose up -d
```

## Checklist

- [ ] Dependencies installed
- [ ] `.env` configured (from `.env.example`)
- [ ] Dev server starts
- [ ] Tests pass

## Environment Template

```bash
# .env.example
DATABASE_URL=postgresql://user:pass@localhost:5432/db
API_KEY=your-key
DEBUG=true
```

## Tips
- Document setup in README
- Use `.env.example` as template
- Record issues in `wiki/experience/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hscspring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
