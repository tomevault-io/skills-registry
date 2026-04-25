---
name: pandoc-markdown
description: Convert Markdown files to HTML with pandoc, optionally add TOC + header backlinks via Lua filter, and optionally serve HTML remotely via a background Python HTTP server. Use when this capability is needed.
metadata:
  author: ramblurr
---

# Pandoc Markdown to HTML

## Purpose

Use this skill when a human asks to convert a Markdown file to an HTML file.

Optional behaviors:

- generate a TOC (`--toc`)
- add "back to TOC" arrows on headings via a Lua filter
- start a background HTTP server for remote viewing

## Inputs to gather

- Markdown source file path
- Optional output HTML path
- Whether TOC is requested
- Whether heading backlinks to TOC are requested
- Whether remote viewing is requested

## Default behavior

If output path is not provided, write HTML next to the source file using the same base name.

Example:

- Input: `/path/docs/ROADMAP.md`
- Output: `/path/docs/ROADMAP.html`

## Core conversion workflow

1. Validate source file exists.
2. Check `pandoc` is installed.
3. Convert Markdown to HTML.
4. Confirm output file exists.
5. Report output path.

Commands:

```bash
# 1) validate input
[ -f "$INPUT_MD" ]

# 2) check pandoc
command -v pandoc

# 3) convert (basic)
pandoc "$INPUT_MD" -o "$OUTPUT_HTML"

# 4) verify
[ -f "$OUTPUT_HTML" ] && file "$OUTPUT_HTML"
```

## Optional: TOC output

Use this when user asks for a table of contents in the generated HTML.

```bash
pandoc --standalone --toc "$INPUT_MD" -o "$OUTPUT_HTML"
```

## Optional: TOC backlinks on headings (Lua filter)

Use this when user asks for heading links back to TOC.

Create a filter file (example path: `pandoc-toc-backlink.lua` in the working directory):

```lua
function Header(h)
  if h.identifier ~= "table-of-contents" then
    h.classes:insert("has-toc-backlink")
  end
  return h
end

function Pandoc(doc)
  local css = pandoc.RawBlock('html', [[
<style>
.toc-backlink {
  margin-left: 0.35em;
  text-decoration: none;
  font-size: 0.9em;
}
</style>
]])

  local script = pandoc.RawBlock('html', [[
<script>
document.addEventListener('DOMContentLoaded', function () {
  var tocId = 'TOC';
  if (!document.getElementById(tocId)) return;

  var selector = 'h1.has-toc-backlink, h2.has-toc-backlink, h3.has-toc-backlink, h4.has-toc-backlink, h5.has-toc-backlink, h6.has-toc-backlink';
  document.querySelectorAll(selector).forEach(function (h) {
    if (h.id === 'table-of-contents' || h.id === 'TOC') return;

    var a = document.createElement('a');
    a.href = '#'+tocId;
    a.className = 'toc-backlink';
    a.setAttribute('aria-label', 'Back to table of contents');
    a.textContent = '↑';

    h.appendChild(document.createTextNode(' '));
    h.appendChild(a);
  });
});
</script>
]])

  table.insert(doc.blocks, 1, css)
  table.insert(doc.blocks, 2, script)
  return doc
end
```

Run pandoc with TOC + filter:

```bash
pandoc --standalone --toc \
  --lua-filter pandoc-toc-backlink.lua \
  "$INPUT_MD" -o "$OUTPUT_HTML"
```

Why this filter shape:

- keeps TOC labels clean (no `↑` text in TOC entries)
- adds arrows to headers at runtime, linking to `#TOC`

Verification checks:

```bash
# TOC container exists
rg -n '<nav id="TOC"' "$OUTPUT_HTML"

# Filter marker/class exists
rg -n 'has-toc-backlink|toc-backlink' "$OUTPUT_HTML"
```

## Optional: remote HTML viewing

Only do this when the user asks to view the HTML remotely.

Requirements:

- Preferred port: `8081`
- Fallback ports in order: `8090`, `3000`, `3001`
- Server must run in background so the agent does not block

### Port selection

Pick the first free port from: `8081 8090 3000 3001`.

```bash
PORT=""
for p in 8081 8090 3000 3001; do
  if ! ss -ltn "( sport = :$p )" | tail -n +2 | grep -q .; then
    PORT="$p"
    break
  fi
done
[ -n "$PORT" ]
```

### Start background server

Serve from directory containing the HTML file.

```bash
HTML_DIR="$(dirname "$OUTPUT_HTML")"
HTML_FILE="$(basename "$OUTPUT_HTML")"

cd "$HTML_DIR"
nohup python3 -m http.server "$PORT" --bind 0.0.0.0 \
  > "/tmp/pandoc-http-${PORT}.log" 2>&1 &
PID=$!
echo "$PID" > "/tmp/pandoc-http-${PORT}.pid"

# verify process is alive
kill -0 "$PID"
```

### Return URL to user

```bash
HOST_IP="$(hostname -I 2>/dev/null | awk '{print $1}')"
```

Share:

- `http://$HOST_IP:$PORT/$HTML_FILE` (if `HOST_IP` is available)
- `http://localhost:$PORT/$HTML_FILE`

### Stop server command

```bash
kill "$(cat /tmp/pandoc-http-${PORT}.pid)"
```

## Failure handling

- If `pandoc` is missing: report conversion cannot run until pandoc is installed.
- If TOC filter file is missing when requested: create it before conversion.
- If no candidate ports are free: report all preferred ports are occupied and ask for an alternate port.
- If background server launch fails: report failure and include `/tmp/pandoc-http-<port>.log` path.

## Response checklist

After execution, report:

- Input markdown path
- Output HTML path
- Whether conversion succeeded
- Whether TOC was included
- Whether TOC backlink filter was applied
- If remote serving was requested:
  - chosen port
  - PID
  - URL(s)
  - stop command

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramblurr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
