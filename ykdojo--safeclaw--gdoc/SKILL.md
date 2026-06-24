---
name: gdoc
description: Read publicly shared Google Docs using curl to download into a file. Use when this capability is needed.
metadata:
  author: ykdojo
---

# Google Docs Reader

To read a Google Doc:

1. Replace `/edit` (or any suffix after the doc ID) with `/mobilebasic`
2. **ALWAYS use curl, NOT WebFetch.** WebFetch summarizes/truncates content. curl gets the full document:

```bash
curl -sL 'https://docs.google.com/document/d/DOC_ID/mobilebasic' > /tmp/doc.txt
```

3. Read the downloaded file with the Read tool

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ykdojo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
