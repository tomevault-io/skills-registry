---
name: web-browsing
description: Fetch and read web pages using command-line tools Use when this capability is needed.
metadata:
  author: pinpox
---

You can browse the web using command-line tools via the bash tool.

### Quick fetch (raw HTML or API responses)

```bash
curl -sL "https://example.com"
```

Use `-sL` to follow redirects silently. Add `-o /dev/null -w "%{http_code}"` to check status codes.

### Rendered text (readable output)

```bash
lynx -dump "https://example.com"
```

Or with w3m:

```bash
w3m -dump "https://example.com"
```

These render HTML to plain text, making web pages readable without HTML tags.

### Downloading files

```bash
curl -sLO "https://example.com/file.tar.gz"
```

### JSON APIs

```bash
curl -s "https://api.example.com/data" | jq .
```

Use `jq` to parse and filter JSON responses.

### Tips

- Always use `curl -sL` (silent + follow redirects) as the default.
- For long pages, pipe through `head -n 100` to avoid excessive output.
- Use `lynx -dump` when you need to read a web page — it handles tables, links, and formatting.
- Check if `lynx` or `w3m` are available with `which lynx w3m` before using them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pinpox) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
