---
name: smartsheet
description: Manage sheets, rows, and columns via Smartsheet API. Automate spreadsheet workflows. Use when this capability is needed.
metadata:
  author: openclaw
---
# Smartsheet
Work management and collaboration.
## Environment
```bash
export SMARTSHEET_ACCESS_TOKEN="xxxxxxxxxx"
```
## List Sheets
```bash
curl "https://api.smartsheet.com/2.0/sheets" -H "Authorization: Bearer $SMARTSHEET_ACCESS_TOKEN"
```
## Get Sheet
```bash
curl "https://api.smartsheet.com/2.0/sheets/{sheetId}" -H "Authorization: Bearer $SMARTSHEET_ACCESS_TOKEN"
```
## Add Row
```bash
curl -X POST "https://api.smartsheet.com/2.0/sheets/{sheetId}/rows" \
  -H "Authorization: Bearer $SMARTSHEET_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"toBottom": true, "cells": [{"columnId": 123, "value": "New Row"}]}'
```
## Links
- Docs: https://smartsheet.redoc.ly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
