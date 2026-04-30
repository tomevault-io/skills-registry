---
name: evernote
description: Manage notes, notebooks, and tags via Evernote API. Create, search, and organize notes programmatically. Use when this capability is needed.
metadata:
  author: openclaw
---
# Evernote
Note-taking and organization.
## Environment
```bash
export EVERNOTE_ACCESS_TOKEN="xxxxxxxxxx"
export EVERNOTE_BASE="https://www.evernote.com/shard/s1/notestore"
```
## List Notebooks
```bash
curl "$EVERNOTE_BASE/listNotebooks" -H "Authorization: Bearer $EVERNOTE_ACCESS_TOKEN"
```
## Create Note
```bash
curl -X POST "$EVERNOTE_BASE/createNote" \
  -H "Authorization: Bearer $EVERNOTE_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "New Note", "content": "<?xml version=\"1.0\" encoding=\"UTF-8\"?><!DOCTYPE en-note SYSTEM \"http://xml.evernote.com/pub/enml2.dtd\"><en-note>Hello World</en-note>"}'
```
## Links
- Docs: https://dev.evernote.com

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
