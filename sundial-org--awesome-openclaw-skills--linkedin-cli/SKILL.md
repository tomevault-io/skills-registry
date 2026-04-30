---
name: linkedin-cli
description: Use when working with a bird-like LinkedIn CLI for searching profiles, checking messages, and summarizing your feed using session cookies.
metadata:
  author: sundial-org
---

# LinkedIn CLI (lk)

A witty, punchy LinkedIn CLI inspired by the `bird` CLI. It uses session cookies for authentication, allowing for automated profile scouting, feed summaries, and message checks without a browser.

## Setup

1.  **Extract Cookies**: Open LinkedIn in Chrome/Firefox.
2.  Go to **DevTools (F12)** -> **Application** -> **Cookies** -> `www.linkedin.com`.
3.  Copy the values for `li_at` and `JSESSIONID`.
4.  Set them in your environment:
    ```bash
    export LINKEDIN_LI_AT="your_li_at_value"
    export LINKEDIN_JSESSIONID="your_jsessionid_value"
    ```

## Usage

- `lk whoami`: Display your current profile details.
- `lk search "query"`: Search for people by keywords.
- `lk profile <public_id>`: Get a detailed summary of a specific profile.
- `lk feed -n 10`: Summarize the top N posts from your timeline.
- `lk messages`: Quick peek at your recent conversations.
- `lk check`: Combined whoami and messages check.

## Dependencies

Requires the `linkedin-api` Python package:
```bash
pip install linkedin-api
```

## Authors
- Built by Fido 🐶

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
