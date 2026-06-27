---
name: my-api-hq
description: > Use when this capability is needed.
metadata:
  author: myapihq
---

# MyApiHQ

The root service. It manages accounts, API keys, organizations, and billing. No other service works without credentials from here.

## Capabilities
<!-- llm:start -->
MyApiHQ is the platform's foundation. Every other service (domain, funnel, email, image, storage, pixel, webhook, workflow, url) requires both an `api_key` and (for org-scoped resources) an `org_id` minted here. Setup is one command — `myapi account setup` — which provisions an account, generates an api_key, creates a default org, and stores everything in `~/.myapi/config.json`. Subsequent commands pick up those defaults automatically.

```
myapihq  ──►  org_id + api_key
                  │
          ┌───────┴────────┐
     mydomainapi       myfunnelapi
     (domains)         (websites)
```

### Anonymous vs registered accounts

Two tiers, chosen at setup time:

- **Anonymous** (`myapi account setup --anonymous`): zero-friction account creation. **Starts with $0 credit.** Good for catalog browsing, reading help, inspecting schemas — nothing that costs upstream money. The agent-onboarding path: provisions an account in one call, no email needed.
- **Registered** (verified email via `myapi account link <email>`): unlocks $5 free credit and the paid surface (LLM, image, email, domain register, etc.). Required for `myapi billing setup` and anything that hits Stripe.

An anonymous account can upgrade at any time via `myapi account link <email>` — the credit grants on successful verification. Anonymous accounts that need paid actions hit a friendly `REGISTRATION_REQUIRED` error pointing at `myapi account link`; a registered account with an empty wallet hits `402 INSUFFICIENT_FUNDS` (top up, or enable auto-recharge — below).

### Health check

`myapi doctor` runs an org-wide consistency check across every slot (funnels, webhooks, domains, containers, workflows, emails, payments) and layers on customer-perspective DNS/HTTP probes from your machine. It returns per-section findings (`✓` pass / `⚠` warning / `✗` critical) with remediation hints; add `--json` for machine output. The exit code is non-zero **only** on customer-actionable criticals — platform-side issues the MyAPI team is already handling are surfaced with an `ℹ` marker but don't fail the run. Run it to self-check before building (is the org set up?) and after (did everything wire up?).
<!-- llm:end -->

## Commands
<!-- generated:start -->
| Command | What it does |
|---|---|
| `myapi account setup` | Interactive setup: creates account, generates api_key, sets defaults |
| `myapi account whoami` | Show current account, default org/funnel, balance, free-tier usage |
| `myapi account link [email]` | Upgrade anonymous account to registered (or add a second session) |
| `myapi account switch [index]` | Switch active account |
| `myapi org list` | List all orgs (`*` marks the default) |
| `myapi org create --name "..."` | Create a new org (`--yes` auto-sets as default) |
| `myapi org get [id]` | Inspect one org (defaults to current default) |
| `myapi org update [id]` | Update fields (name, tagline, description, business-sector, logo-url) |
| `myapi org delete <id>` | Delete an org and cascade |
| `myapi org sync-brand <domain>` | Scrape a live site and auto-fill brand info |
| `myapi keys list / create / revoke <id>` | Manage API keys (alias `myapi account api-keys`) |
| `myapi billing balance` | Check balance |
| `myapi billing topup <amount>` | Top up by dollar amount |
| `myapi billing history` | Recent transactions |
| `myapi billing usage [--period month|30d]` | Spend rolled up by service (this month, or trailing 30d) |
| `myapi billing spend-cap [<amount> | clear] [--period month|day]` | Set/show/clear the account-level spend ceiling (IAM Layer 2) |
| `myapi billing auto-recharge [show \| set \| disable]` | Keep the wallet funded automatically — off-session refill when balance drops below a threshold, bounded by a monthly cap |
| `myapi account mailing-address ["<address>"]` | Get or set the account's CAN-SPAM mailing address (required for email send) |
| `myapi config set-org <id>` / `set-funnel <id>` / `set-domain <name>` | Set CLI defaults |
| `myapi install-skills` | Install agent skill files into ~/.claude/, ~/.gemini/, ~/.cursor/ |
| `myapi doctor [--verbose] [--json]` | Org-wide health check: per-slot config/integrity findings + DNS/HTTP probes |
<!-- generated:end -->

## Examples
<!-- llm:start -->
```bash
# Cold start: provision account + default org
myapi account setup
myapi org create "Acme" --yes

# Day-to-day
myapi account whoami           # confirm what's active
myapi billing balance       # before doing anything that costs credits
myapi billing topup 20      # add $20

# For unattended/autonomous runs: keep the wallet funded so a 402 never
# stalls the agent. Refill to ≥$5, $20 at a time, up to $100/month.
myapi billing auto-recharge set --threshold 5 --amount 20 --monthly-cap 100

# Sync brand info from an existing website
myapi org sync-brand acme.com

# Switch between multiple accounts
myapi account switch 2
```

If any service returns `402 INSUFFICIENT_FUNDS`, top up (`myapi billing topup`) — or enable `myapi billing auto-recharge` so it refills itself. A `402 SPEND_CAP_EXCEEDED` is different: you hit a spend ceiling you set, so raise it with `myapi billing spend-cap` rather than topping up.

Each org gets a free preview subdomain (`*.makeautonomous.com`) usable before registering a custom domain.
<!-- llm:end -->

## Notes

- Set `--org` defaults once with `myapi config set-org <id>` to skip the flag on every command.
- API keys have format `hq_live_...` and are sent as `Authorization: Bearer <key>`.
- `org sync-brand` is async (scrapes the site, polls the job).

Run `myapi --help` or `myapi <command> --help` for full flag reference.

---
> Source: [myapihq/myapi](https://github.com/myapihq/myapi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
