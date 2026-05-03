---
name: xhs-browsing
description: >- Use when this capability is needed.
metadata:
  author: 8de4732a
---

# XHS Browsing Skill

Automate browsing xiaohongshu.com to search for topics, extract post content, and compile structured summaries using agent-browser.

## Prerequisites

- The `agent-browser` plugin must be installed and available
- A saved session state file OR willingness to log in manually in headed mode

## Core Workflow

### 1. Session Management

**First-time login:**

1. Open xiaohongshu in headed mode with a named session:
   ```
   agent-browser --headed --session-name xiaohongshu open https://www.xiaohongshu.com/explore
   ```
2. Inform the user: "Please log in to xiaohongshu in the browser window. Tell me when done."
3. After login confirmation, save state:
   ```
   agent-browser --session-name xiaohongshu state save xiaohongshu-auth.json
   ```

**Returning sessions:**

1. Load saved state and open the site:
   ```
   agent-browser --session-name xiaohongshu state load xiaohongshu-auth.json
   agent-browser --session-name xiaohongshu open https://www.xiaohongshu.com/explore
   ```
2. If the page shows a login prompt, fall back to first-time login flow.

### 2. Search for Topics

Navigate to search results using a URL with encoded keyword. Always quote the URL to prevent shell expansion:

```
agent-browser --session-name xiaohongshu open 'https://www.xiaohongshu.com/search_result?keyword=<ENCODED_KEYWORD>&source=web_search_result_notes&type=51'
```

Wait for the page to load:

```
agent-browser --session-name xiaohongshu wait --load networkidle
```

### 3. Extract Post List

Use JavaScript evaluation to extract post metadata from the search results page. Use `eval --stdin` with heredoc to avoid shell quoting issues.

Refer to `references/extract-scripts.md` for the complete extraction scripts.

The key extraction pattern targets `section.note-item` elements and extracts title, author, likes count, and the `a.cover` link href (which includes the `xsec_token` parameter needed for access).

### 4. Open and Extract Individual Posts

**Critical**: Do NOT navigate directly to `/explore/<id>` URLs — these will return 404 due to anti-scraping protection. Instead, click post cards via JavaScript from the search results page:

```
agent-browser --session-name xiaohongshu eval --stdin <<'EVALEOF'
document.querySelectorAll('section.note-item')[INDEX].querySelector('a.cover').click();
'clicked'
EVALEOF
```

Wait 3 seconds for the modal to load, then extract content from the detail overlay:

```
agent-browser --session-name xiaohongshu eval --stdin <<'EVALEOF'
const noteContainer = document.querySelector('.note-detail-mask')
  || document.querySelector('[class*="note-detail"]')
  || document.querySelector('.note-scroller');
const content = noteContainer ? noteContainer.innerText : 'not found';
content.substring(0, 3000);
EVALEOF
```

Close the modal with Escape before opening the next post:

```
agent-browser --session-name xiaohongshu press Escape
```

### 5. Post Prioritization

Sort posts by engagement (likes count) and prioritize:
- Posts with high like counts (>50)
- Posts from recent timeframes (today, yesterday)
- Posts with informative titles (skip ads, irrelevant content)

Aim to extract **8-12 high-quality posts** per search session.

### 6. Compile Summary

Organize extracted content into a structured Markdown report with these sections:

1. **Header**: Topic, date, source
2. **Major Events**: Biggest news items with details
3. **Industry Trends**: Broader patterns and analysis
4. **Technology Updates**: New releases, benchmarks, technical developments
5. **Notable Opinions**: Interesting viewpoints from posts and comments
6. **Summary Table**: Quick-reference table of key topics

Save the report as a Markdown file with naming convention: `xhs-<topic>-<YYYYMMDD>.md`

## Important Notes

- Always use `--session-name xiaohongshu` for session persistence across commands
- Always quote URLs containing special characters with single quotes
- Use `eval --stdin <<'EVALEOF'` for all JavaScript to avoid shell escaping issues
- The `xsec_token` in search result links is session-bound; do not reuse across sessions
- Post detail modals overlay the search results page; press Escape to return
- Rate limit: wait 1-3 seconds between post extractions to avoid triggering anti-bot measures

## Additional Resources

### Reference Files

- **`references/extract-scripts.md`** — Complete JavaScript extraction code snippets for post lists and post content

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/8de4732a) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
