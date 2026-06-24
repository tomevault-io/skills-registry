---
name: apple-notes
description: Search, read, and create Apple Notes using osascript/JXA on macOS. Use when this capability is needed.
metadata:
  author: kody-w
---

# Apple Notes

Interact with Apple Notes via JXA (JavaScript for Automation) on macOS.

## Read Notes

```bash
osascript -l JavaScript -e '
  const app = Application("Notes");
  const notes = app.notes();
  notes.slice(0, 10).map(n => n.name());
'
```

## Search Notes

```bash
osascript -l JavaScript -e '
  const app = Application("Notes");
  app.notes.whose({name: {_contains: "search term"}})().map(n => ({
    name: n.name(),
    modified: n.modificationDate()
  }));
'
```

## Create a Note

```bash
osascript -l JavaScript -e '
  const app = Application("Notes");
  const folder = app.defaultAccount.folders.byName("Notes");
  app.make({new: "note", at: folder, withProperties: {name: "Title", body: "<h1>Title</h1><p>Content</p>"}});
'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
