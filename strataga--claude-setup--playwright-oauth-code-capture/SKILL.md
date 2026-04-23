---
name: playwright-oauth-code-capture
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# Playwright OAuth Code Capture

## Problem

When automating OAuth flows with Playwright, the authorization code is passed via redirect
to `http://localhost?code=...`. If no server is listening on localhost, the page shows
"localhost refused to connect" and the authorization code in the URL is lost before you
can extract it.

## Context / Trigger Conditions

- Automating Google OAuth, GitHub OAuth, or similar OAuth 2.0 flows
- `redirect_uri` is set to `http://localhost` (common for desktop apps)
- No local server running to receive the callback
- Browser shows "This site can't be reached" or "localhost refused to connect"
- Need to capture the authorization code from the redirect URL

## Solution

Use Playwright's `page.route()` to intercept the localhost request before it fails:

```javascript
let authCode = null;

// Set up route interception BEFORE starting OAuth flow
await page.route('http://localhost/**', async (route) => {
  const url = route.request().url();
  authCode = url;  // Capture the full URL with code parameter
  await route.abort();  // Abort the request (it would fail anyway)
});

// Now proceed with OAuth flow...
// Click authorize button, etc.

// Wait for the redirect to be intercepted
await page.waitForTimeout(2000);

// Extract the code from the captured URL
if (authCode) {
  const urlParams = new URL(authCode).searchParams;
  const code = urlParams.get('code');
  console.log('Authorization code:', code);
}
```

### Key Points

1. **Set up route BEFORE the redirect happens** - The interception must be registered
   before clicking the authorize button.

2. **Use `route.abort()`** - Since no server is listening, abort the request cleanly
   rather than letting it fail with a connection error.

3. **Pattern matching** - Use `http://localhost/**` to catch any path on localhost.

4. **Extract from URL** - Parse the captured URL to extract the `code` parameter.

## Verification

After the OAuth authorization is completed:
1. The `authCode` variable contains the full redirect URL
2. The URL includes `?code=...` with the authorization code
3. You can exchange this code for access/refresh tokens via the token endpoint

## Example

Complete flow for Google OAuth:

```javascript
// Intercept localhost redirects
let capturedUrl = null;
await page.route('http://localhost/**', async (route) => {
  capturedUrl = route.request().url();
  await route.abort();
});

// Navigate to Google OAuth consent screen
await page.goto('https://accounts.google.com/o/oauth2/v2/auth?' +
  'client_id=YOUR_CLIENT_ID&' +
  'redirect_uri=http://localhost&' +
  'response_type=code&' +
  'scope=https://www.googleapis.com/auth/webmasters.readonly&' +
  'access_type=offline');

// User logs in and authorizes...
// (automation or manual interaction)

// After authorization, the redirect is intercepted
await page.waitForTimeout(3000);

// Extract and use the code
const code = new URL(capturedUrl).searchParams.get('code');

// Exchange for tokens
const tokenResponse = await fetch('https://oauth2.googleapis.com/token', {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
  body: new URLSearchParams({
    code: code,
    client_id: 'YOUR_CLIENT_ID',
    client_secret: 'YOUR_CLIENT_SECRET',
    redirect_uri: 'http://localhost',
    grant_type: 'authorization_code'
  })
});
```

## Notes

- This technique works for any OAuth provider that supports localhost redirects
- The captured URL is only available for the duration of the page session
- For production OAuth flows, run an actual local server to handle callbacks
- Some OAuth providers require exact redirect_uri matching (including trailing slashes)
- Google OAuth apps in "Testing" mode require users to be added as test users first

## Related

- Google OAuth apps in testing mode: Must add user emails to "Test users" in
  Google Cloud Console > APIs & Services > OAuth consent screen > Audience
- Token exchange: Use the captured code within minutes before it expires

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
