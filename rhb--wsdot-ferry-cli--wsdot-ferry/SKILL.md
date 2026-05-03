---
name: wsdot-ferry
description: Answer questions about Washington State Ferry schedules, vessel status, space availability, delays, and routes using the wsdot CLI. Use when the user asks about ferries, WSF, WSDOT, ferry schedules, ferry delays, vessel locations, or ferry space availability. Use when this capability is needed.
metadata:
  author: rhb
---

# WSDOT Ferry Assistant

Answer ferry questions by running `wsdot` CLI commands and presenting the results conversationally.

## Quick Start

User asks: "When's the next ferry from Seattle to Bainbridge?"

```bash
wsdot schedule seattle bainbridge
```

Read the table output, then reply conversationally: "The next ferry from Seattle to Bainbridge departs at 2:10 PM on the M/V Wenatchee, arriving at 2:45 PM."

## Commands

### schedule — Ferry departure times

```bash
wsdot schedule <from> <to> [--date DATE]
```

- `from` / `to`: terminal names (see Terminal Names below)
- `--date` / `-d`: `today`, `tomorrow`, a weekday name, or `YYYY-MM-DD` (defaults to today)

### vessels — Vessel locations and status

```bash
wsdot vessels [--vessel NAME]
```

- Shows vessels in the San Juan Islands fleet
- `--vessel` / `-v`: filter to a specific vessel name
- Output includes ETA, delay, departing/arriving terminals

### space — Drive-up and reservation space

```bash
wsdot space <from> <to>
```

- Shows upcoming sailings with space counts and status (Space Available / Limited Space / Nearly Full)

### terminals — List all ferry terminals

```bash
wsdot terminals
```

### routes — List WSF routes

```bash
wsdot routes
```

Static list of all routes with route IDs.

## Terminal Names

Map user input to these exact names:

| Name | Common Variations |
|------|-------------------|
| `anacortes` | Anacortes |
| `lopez` | Lopez Island, Lopez |
| `friday-harbor` | Friday Harbor, San Juan Island |
| `orcas` | Orcas Island, Orcas |
| `shaw` | Shaw Island, Shaw |
| `bainbridge` | Bainbridge Island, Bainbridge |
| `bremerton` | Bremerton |
| `clinton` | Clinton, Whidbey (south) |
| `coupeville` | Coupeville, Keystone, Whidbey |
| `edmonds` | Edmonds |
| `fauntleroy` | Fauntleroy, West Seattle |
| `kingston` | Kingston |
| `mukilteo` | Mukilteo |
| `point defiance` | Point Defiance |
| `port townsend` | Port Townsend, PT |
| `seattle` | Seattle, Coleman Dock, Pier 52 |
| `southworth` | Southworth |
| `tahlequah` | Tahlequah |
| `vashon` | Vashon Island, Vashon |

## Guidelines

- Always run the command and show real data. Never guess schedules or times.
- Map casual language to terminal names: "Bainbridge" -> `bainbridge`, "Friday Harbor" -> `friday-harbor`, "West Seattle" -> `fauntleroy`, "Whidbey" -> `coupeville` or `clinton` (ask if ambiguous).
- For delay questions, run `wsdot vessels` and check the Delay column.
- For "is there room/space" questions, run `wsdot space <from> <to>`.
- Summarize results conversationally. Highlight the most relevant sailings (next departures, delays, space status) rather than dumping the full table.
- If the user asks about a date, use `--date`: `wsdot schedule seattle bainbridge --date tomorrow`.
- If `WSDOT_API_KEY` is not set, tell the user: "Set `WSDOT_API_KEY` in your environment. Get a free key at https://wsdot.wa.gov/traffic/api/."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rhb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
