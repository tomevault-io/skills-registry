---
name: x-scraping
description: Read tweets when X/Twitter API blocks you, rate-limits you, or requires $42,000/month for basic access. Use when this capability is needed.
metadata:
  author: victoriadigital
---

# X/Twitter Scraping Skill

## The Problem

X (formerly Twitter) has made API access increasingly hostile:
- Free tier is basically useless
- Paid tiers cost absurd amounts
- Rate limits are aggressive
- Bot detection blocks legitimate automation

## The Solution: Nitter Instances

[Nitter](https://github.com/zedeus/nitter) is an open-source Twitter frontend that scrapes the web interface. Public instances let you read tweets without authentication.

### Finding Working Instances

Nitter instances come and go. Check:
- https://status.d420.de/
- https://github.com/zedeus/nitter/wiki/Instances

### Usage

#### Basic Tweet Reading

```bash
# Replace twitter.com/x.com with a nitter instance
curl -s "https://nitter.privacydev.net/elonmusk/status/1234567890" | \
  grep -oP '(?<=<div class="tweet-content media-body" dir="auto">).*?(?=</div>)'
```

#### Using web_fetch (for Clawdbot agents)

```javascript
// In your agent
web_fetch({
  url: "https://nitter.privacydev.net/username/status/12345",
  extractMode: "text"
})
```

#### Shell Function (add to your toolbox)

```bash
xread() {
  local input="$1"
  local tweet_path=""
  
  # Handle full URLs or just IDs
  if [[ "$input" =~ ^https?://(twitter\.com|x\.com)/([^/]+)/status/([0-9]+) ]]; then
    tweet_path="${BASH_REMATCH[2]}/status/${BASH_REMATCH[3]}"
  elif [[ "$input" =~ ^[0-9]+$ ]]; then
    tweet_path="i/status/$input"
  else
    tweet_path="$input"
  fi
  
  # Try multiple instances (they go down frequently)
  local instances=(
    "nitter.privacydev.net"
    "nitter.poast.org"
    "nitter.woodland.cafe"
  )
  
  for instance in "${instances[@]}"; do
    result=$(curl -sL "https://$instance/$tweet_path" 2>/dev/null)
    if [[ -n "$result" && ! "$result" =~ "Instance has been rate limited" ]]; then
      echo "$result" | grep -oP '(?<=tweet-content media-body).*?(?=</div>)' | head -1
      return 0
    fi
  done
  
  echo "All instances failed or rate limited"
  return 1
}
```

### Browser Automation Alternative

When Nitter instances are all down, use browser automation:

```python
# Using Playwright
from playwright.sync_api import sync_playwright

def read_tweet(url):
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=True)
        page = browser.new_page()
        
        # Set realistic user agent
        page.set_extra_http_headers({
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
        })
        
        page.goto(url)
        page.wait_for_selector('[data-testid="tweetText"]', timeout=10000)
        
        tweet_text = page.query_selector('[data-testid="tweetText"]').inner_text()
        browser.close()
        
        return tweet_text
```

## Rate Limit Strategies

1. **Rotate instances** — Don't hammer one Nitter instance
2. **Add delays** — 2-5 seconds between requests
3. **Cache aggressively** — Tweets don't change, store what you fetch
4. **Browser as fallback** — Slower but more reliable

## Known Working Instances (as of 2026)

⚠️ This list goes stale fast. Always check status pages.

- `nitter.privacydev.net`
- `nitter.poast.org`
- `nitter.woodland.cafe`
- `nitter.esmailelbob.xyz`

## When All Else Fails

1. Try FixTweet/FxTwitter embeds: `https://fxtwitter.com/user/status/123`
2. Check Wayback Machine for archived tweets
3. Use RSS bridges that still work
4. Accept defeat (temporarily) and try again later

---

*Last updated: 2026-02-01*
*Contributions welcome — especially new working instances!*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/victoriadigital) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
