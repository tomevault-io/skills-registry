---
name: openclaw-rpa
description: Record browser, Excel, Word & API actions once — replay without the LLM: faster, cheaper, no hallucinations. github.com/laziobird/openclaw-rpa . Supports computer-use automation: web clicks/fill/extract, local Excel (.xlsx via openpyxl), Word (.docx via python-docx), HTTP API calls (httpx GET/POST), and auto-login cookie reuse. · Triggers: #rpa #RPA #rpa-api #rpa-login #rpa-login-done #rpa-autologin #rpa-autologin-list #rpa-list #rpa-run #rpa-help · Use when user says RPA, 录制自动化, browser automation, or asks to automate browser/file tasks. Use when this capability is needed.
metadata:
  author: laziobird
---

# openclaw-rpa — **Locale router (read this first)**

**GitHub:** **[https://github.com/laziobird/openclaw-rpa](https://github.com/laziobird/openclaw-rpa)** — source, README, install, sample scripts under `rpa/`.

## What this skill does

**openclaw-rpa** is an **LLM-based RPA Agent framework**. You describe a task in plain language; the AI executes it step by step in a **real browser, on your computer, or via API services** — with screenshot proof at every step — then compiles everything into a **standalone Playwright Python script**. Replay runs that script directly — **no model call, no token burn, no hallucination risk** — faster and cheaper than having the AI click every time.

**Why this matters**

1. **Saves compute and money** — Having a **large model** drive the browser on **every** run can cost **roughly single-digit to tens of US dollars** per heavy session (tokens, tools, long context). After you **record once**, repeat work **does not call the model**—replay is **much faster** and **near-zero** LLM cost for those steps.
2. **Verify once, run the same way every time** — During recording you **confirm** the flow works; later, replay **executes the saved steps** deterministically. You avoid asking the AI to “do it again” on every run, which **hurts consistency** and **raises hallucination risk**.

**What you can automate** (record once, replay many times — follow each site’s terms and local law):

| Category | Examples |
|----------|---------|
| **Browser** | Login, navigate, click, fill forms, extract text, sort / filter tables |
| **HTTP API** | `GET` / `POST` any REST endpoint, save JSON, embed API keys directly in the script (`#rpa-api`) |
| **Excel (`.xlsx`)** | Create / update workbooks, multiple sheets, headers, freeze panes, dynamic rows from JSON or another file |
| **Word (`.docx`)** | Generate reports with paragraphs and tables — no Microsoft Office required |
| **Auto-login** | Save cookies once with `#rpa-login`, auto-inject on every recording and replay — skip OTP / CAPTCHA / QR-code flows |
| **Mixed flows** | Any combination above in a single recorded task (e.g. API + Excel + Word, or browser + login + extract) |

---

## 🚀 Real-world cases — already recorded, ready to run

> **These scripts are registered in `registry.json`. Run any of them instantly — no re-recording, no tokens.**
> ```
> #rpa-list                          ← see all registered tasks
> #rpa-run:amazonbestseller          ← run one directly
> python3 rpa_manager.py run <name>  ← or via CLI
> ```

| # | Case | What it does | Run name |
|---|------|-------------|----------|
| 🛒 | **[Amazon Best Sellers Scraper](articles/scenario-amazon-bestsellers.en-US.md)** | Scrape top 40 products (title, price, rating, reviews, URL) → Word table | `amazonbestseller` |
| 🏨 | **[Airbnb Competitor Price Tracker](articles/scenario-airbnb-compare.en-US.md)** | Open browser → vision recognition → extract prices & ratings → Word report | `airbnb民宿比价分析v11` |
| 🏦 | **[AP Reconciliation (EN)](articles/scenario-ap-reconciliation.en-US.md)** | Mock GET open payables → Excel match → Word table report | `reconciliationV2` |
| 🔑 | **[Auto-login: Ctrip Hotel](articles/autologin-tutorial.en-US.md)** | Save cookies once → skip OTP forever → extract hotel info → Word doc | `携程酒店V3` |
| 📈 | **Yahoo Finance News** | Search NVDA → News tab → save top 5 headlines to Desktop | `YahooNew` |
| 🎬 | **Douban Movie** | Search a film → detail page → title, rating, synopsis → Desktop file | `获取豆瓣电影数据` |
| 🌐 | **Alpha Vantage API** | `TIME_SERIES_DAILY` for NVDA → `nvda_time_series_daily.json` (no browser) | `apiV3` |
| 🛍️ | **Sauce Demo Shopping** | Sign in → sort by price → add two most expensive → log out | `onlineShoppingV1` |

📖 **[Full case gallery & videos → README.md](README.md)**

---

## When to use

| You want to… | Send |
|----------------|------|
| **Start recording** a new flow | `#automation robot`, `#RPA`, `#rpa`, or mention **Playwright automation** |
| **See saved tasks** you can run | `#rpa-list` |
| **Run a saved task** (e.g. new chat) | `#rpa-run:{task name}` |
| **Run in this chat** | `run:{task name}` (`zh-CN`: `#run:{task-name}`) |

## Quick start (after install)

```text
#rpa-list
#rpa-run:your-task-name
```

Full protocol, state machine, **two-line signup** (task name + capability **A–G/N**), **`deps-check` / `deps-install`**, `record-step` JSON, **progressive probing**, and **selector strength** (composite CSS — container + tag / attributes / `:has()`; avoid bare `h3`) live in the locale file below.

## Output

Generated file is **ordinary Python** (`rpa/*.py`) — runs standalone with `python3`, editable, no OpenClaw dependency at replay time.

## Runtime (OS)

**macOS (`darwin`), Linux, and Windows (`win32`).** The skill metadata lists all three for marketplaces (e.g. [ClawHub](https://clawhub.ai/laziobird/openclaw-rpa#runtime)). Stack: Python 3 + Playwright + `httpx` / `openpyxl` / `python-docx` — all support Windows. Details: **README.md** → *Platforms (runtime)*.

## Scope

**Browser** — `goto`, `click`, `fill`, `select_option`, `scroll`, `wait`, `snapshot`, `extract_text`, `dom_inspect`.  
**HTTP API** — `api_call` (httpx GET/POST, key embedding, `save_response_to`); independent of the browser page.  
**Local files** — `merge_files` (concatenate Desktop files); `extract_text` writes to disk; patch `rpa/*.py` for folder / file ops after recording.  
**Excel / Word** — `excel_write` (openpyxl, multi-sheet, dynamic rows from JSON or another file); `word_write` (python-docx, paragraphs + tables); no Microsoft apps required.  
**Computed logic** — `python_snippet` injects arbitrary Python into the generated script; **executed and validated at record time**.  
**Out of scope** — large ETL, databases, heavy OS automation.

## Recommended sites

**Good fits** — predictable structure, works well out of the box:

| Category | Examples |
|----------|---------|
| Finance / data | Yahoo Finance, investing.com |
| E-commerce | Sauce Demo (`saucedemo.com`), AliExpress, eBay |
| News / media | BBC News, Reuters, Hacker News, Reddit listing pages |
| Reference | Wikipedia, GitHub public repo / issues pages |

**Not recommended** — likely to break or require manual intervention:

| Situation | Why | Workaround |
|-----------|-----|------------|
| Login-gated flows (password / SMS OTP / slider / QR code) | Credentials and 2FA must be handled manually | **Use `#rpa-login` to log in once manually → cookies saved automatically → `#rpa-autologin` injects them on every future recording and replay, skipping the login flow entirely** |

> **Tip:** on a new site, start with `goto` + `snapshot` to confirm the page structure is readable before building a full flow.

## Mandatory: load the correct instruction file

1. **Read** `config.json` in this skill directory. If it does not exist, read **`config.example.json`** (same shape; default `locale` is **`en-US`**).
2. Read the `"locale"` field. Allowed values: **`zh-CN`** and **`en-US`** (repository default in **`config.example.json`**: **`en-US`**).
3. **Immediately use the Read tool** to load the **full** skill body:
   - `zh-CN` → **`SKILL.zh-CN.md`**
   - `en-US` → **`SKILL.en-US.md`**

4. **Follow only that file** for state machine, triggers, `record-step` JSON, onboarding text, and user-facing replies.

5. **Reply to the user in the active locale’s language:**
   - `zh-CN` → Simplified Chinese for agent messages (user may still type English).
   - `en-US` → English for agent messages (user may still type Chinese).

## Changing language

- Copy `config.example.json` → `config.json` if needed (`python3 scripts/bootstrap_config.py`), then edit `"locale"`, **or**
- Run: `python3 scripts/set_locale.py en-US` / `python3 scripts/set_locale.py zh-CN` (creates `config.json` from the example when missing).

After a locale change, the agent should **re-read** the matching `SKILL.*.md` in a new turn or session. See **README.md** in this directory for the full workflow.

## ClawHub / discovery

- **SKILL.md** (this file): short router + **when to use** + **quick start** for listings like [ClawHub](https://clawhub.ai/).
- **SKILL.zh-CN.md** / **SKILL.en-US.md**: full **onboarding**, **recording**, **RUN/LIST**, and anti-timeout rules.
- **Scenario docs:** [Amazon Best Sellers](articles/scenario-amazon-bestsellers.en-US.md) · [Airbnb Price Tracker](articles/scenario-airbnb-compare.en-US.md) · [AP Reconciliation EN](articles/scenario-ap-reconciliation.en-US.md) · [AP Reconciliation CN](articles/scenario-ap-reconciliation.md) · [Auto-login](articles/autologin-tutorial.en-US.md).

## Relative paths

When the loaded file references `playwright-templates.md`, `synthesis-prompt.md`, or `rpa_manager.py`, resolve paths **relative to this skill directory** (parent of `SKILL.md`).

---
> Source: [laziobird/openclaw-rpa](https://github.com/laziobird/openclaw-rpa) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
