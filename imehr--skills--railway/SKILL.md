---
name: railway
description: Railway.com deployment and management - deployment, logs, migrations, troubleshooting, monorepo strategies, security, and CLI reference. Use when deploying to Railway, configuring services, managing environment variables, or debugging deployment issues. Use when this capability is needed.
metadata:
  author: imehr
---

# Railway Deployment & Management

## Verification

**BEFORE using this skill, verify the project uses Railway:**

1. **User explicitly mentions "Railway"**, OR
2. **Check for Railway artifacts:** `railway.json`, `railway.toml`, `.railway/` directory
3. **When in doubt, ASK:** "Is this project deployed to Railway?"

**DO NOT use for:** Vercel, AWS, Heroku, Fly.io, or unknown platforms.

## Quick Reference (CLI 2026-02)

| Task | Command |
|------|---------|
| Deploy | `railway up --detach` |
| Deploy subdirectory | `railway up --path-as-root ./web` |
| Check status | `railway status` |
| View logs | `railway logs` |
| Build logs | `railway logs --build` |
| Last N lines | `railway logs -n 100` |
| Set variable | `railway variable set KEY=VALUE` |
| Set multiple | `railway variable set K1=V1 K2=V2` |
| List variables | `railway variable list` |
| List as KV | `railway variable list --kv` |
| Delete variable | `railway variable delete KEY` |
| Skip redeploy on set | `railway variable set K=V --skip-deploys` |
| Connect to DB | `railway connect postgres` |
| Run with env | `railway run npm start` |
| Open dashboard | `railway open` |
| Redeploy | `railway redeploy --yes` |
| Switch env | `railway environment staging` |
| SSH into container | `railway ssh` |
| Service link | `railway service backend` |
| Deployment list | `railway deployment list --limit 5` |
| Domain | `railway domain` |

## Global Options

These flags work across most commands:

| Option | Short | Purpose |
|--------|-------|---------|
| `--service <SERVICE>` | `-s` | Target specific service |
| `--environment <ENV>` | `-e` | Target specific environment |
| `--project <ID>` | `-p` | Target specific project |
| `--json` | — | Scriptable JSON output |
| `--yes` | `-y` | Skip confirmation prompts |

## Essential Patterns

**Deploy Workflow:**
```bash
railway status && railway up --detach
railway logs -n 20  # check after deploy
```

**Monorepo Deploy (CRITICAL: use --path-as-root):**
```bash
railway up --path-as-root ./web --service web
railway up --path-as-root ./backend --service backend
```

**Debug Failed Deployment:**
```bash
railway logs --build           # Build errors
railway variable list          # Verify env vars
railway run npm run build      # Test locally with Railway env
```

**Database Migration (ALWAYS backup first):**
```bash
railway run pg_dump -Fc > backup.dump  # 1. BACKUP
railway connect postgres               # 2. Verify state
railway run npm run migrate            # 3. Migrate
railway connect postgres               # 4. Verify
```

**Set Variables:**
```bash
railway variable set API_KEY=secret123 NODE_ENV=production
railway variable set KEY=VALUE --skip-deploys  # no redeploy
echo "multiline" | railway variable set KEY --stdin
```

## Key Gotchas

- **`--path-as-root`** is required for monorepo subdirectory deploys — without it, subdirectory nests in archive
- **`--project` requires `--environment`** — both must be specified together
- **Variable changes trigger redeploy** by default — use `--skip-deploys` to prevent
- **`railway logs`** streams live by default; `-n N` fetches history and exits (NOT `--limit`)
- **`railway variable`** (singular) is the command — `variables`, `vars`, `var` are aliases
- **TLS between Railway services** may fail with `UNABLE_TO_GET_ISSUER_CERT_LOCALLY` — use `NODE_TLS_REJECT_UNAUTHORIZED=0` or private networking
- **CI/CD tokens**: `RAILWAY_TOKEN` (project-scoped) or `RAILWAY_API_TOKEN` (account-level)

## Common Errors

| Error | Fix |
|-------|-----|
| `undefined variable 'npm'` in Nixpacks | Don't add npm to nixPkgs — comes with nodejs |
| `Tracker 'idealTree' already exists` | `npm cache clean --force && npm install` |
| `gyp ERR! build error` | Add `nixPkgs = ["nodejs", "python3", "gcc", "gnumake"]` |
| `Cannot find module` (workspace) | Use repo root, not subdirectory, as Root Directory |
| `EACCES: permission denied` | Use `/tmp` for temp files or add a volume |
| Service crashes on start | Check `railway logs`, verify env vars, check healthcheck |

## Additional Resources

**Detailed docs in supporting files:**
- **[reference.md](reference.md)** — Complete CLI command reference with all flags
- **[examples.md](examples.md)** — Real-world workflows, scripts, CI/CD pipelines
- **[troubleshooting.md](troubleshooting.md)** — Error messages, diagnosis, solutions
- **[migrations.md](migrations.md)** — Database migration strategies

**Official:** [docs.railway.com](https://docs.railway.com/) · [CLI Reference](https://docs.railway.com/reference/cli-api)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
