---
name: github-image-hosting
description: > Use when this capability is needed.
metadata:
  author: img402
---

# Image Upload for GitHub

Upload an image to img402.dev's free tier and embed the returned URL in GitHub markdown.

## Quick reference

```bash
# Upload (multipart)
curl -s -X POST https://img402.dev/api/free -F image=@/tmp/screenshot.png

# Response
# {"url":"https://i.img402.dev/aBcDeFgHiJ.png","id":"aBcDeFgHiJ","contentType":"image/png","sizeBytes":182400,"expiresAt":"2026-02-17T..."}
```

## Workflow

1. **Get image**: Use an existing file, or capture a screenshot:
   ```bash
   screencapture -x /tmp/screenshot.png        # macOS — full screen
   screencapture -xw /tmp/screenshot.png       # macOS — frontmost window
   ```
2. **Verify size**: Must be under 1MB. If larger, resize:
   ```bash
   sips -Z 1600 /tmp/screenshot.png  # macOS — scale longest edge to 1600px
   ```
3. **Upload**:
   ```bash
   curl -s -X POST https://img402.dev/api/free -F image=@/tmp/screenshot.png
   ```
4. **Embed** the returned `url` in GitHub markdown:
   ```markdown
   ![Screenshot description](https://i.img402.dev/aBcDeFgHiJ.png)
   ```

## GitHub integration

Use `gh` CLI to embed images in PRs and issues:

```bash
# Add to PR description
gh pr edit --body "$(gh pr view --json body -q .body)

![Screenshot](https://i.img402.dev/aBcDeFgHiJ.png)"

# Add as PR comment
gh pr comment --body "![Screenshot](https://i.img402.dev/aBcDeFgHiJ.png)"

# Add to issue
gh issue comment 123 --body "![Screenshot](https://i.img402.dev/aBcDeFgHiJ.png)"
```

## Constraints

- **Max size**: 1MB
- **Retention**: 7 days — suitable for PR reviews, not permanent docs
- **Formats**: PNG, JPEG, GIF, WebP
- **Rate limit**: 1,000 free uploads/day (global)
- **No auth required**

## Tips

- Prefer PNG for UI screenshots (sharp text). Use JPEG for photos.
- If a screenshot is too large, reduce dimensions with `sips -Z 1600` before uploading.
- When adding to a PR body or comment, use `gh pr comment` or `gh pr edit` with the image markdown.

## Paid tier

For permanent images (1 year, 5MB max), use the paid endpoint at $0.01 USDC via x402. See https://img402.dev/blog/paying-x402-apis for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/img402) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
