---
name: mcp-server
description: MCP server for AI agents to create video ads via Streamable HTTP. Primary auth is OAuth 2.1 + Dynamic Client Registration (the modern Claude Desktop / Claude.ai / ChatGPT / Cursor "Connect" flow); long-lived API keys remain supported for headless clients. Use when this capability is needed.
metadata:
  author: prizmad
---

# Prizmad MCP Server

Prizmad exposes a remote Model Context Protocol (MCP) server. Any MCP-compatible host (Claude Desktop, Claude.ai web, ChatGPT, Cursor, Continue, Zed, Claude Code CLI, n8n, custom SDKs) connects and drives the full UGC video studio programmatically.

- **Endpoint**: `https://prizmad.com/api/mcp`
- **Transport**: `streamable-http`
- **Server card**: <https://prizmad.com/.well-known/mcp/server-card.json>
- **Source code (stdio bridge for stdio-only clients)**: <https://github.com/prizmad/Prizmad-MCP-server>

## Connect (primary path — OAuth Connect button)

Modern MCP clients click **Add custom connector → enter the MCP URL** and the OAuth flow runs automatically:

1. Client probes `https://prizmad.com/api/mcp` → 401 with `WWW-Authenticate: Bearer resource_metadata="…"`.
2. Client reads `/.well-known/oauth-protected-resource` and `/.well-known/oauth-authorization-server`.
3. Client `POST /oauth/register` (RFC 7591 Dynamic Client Registration) — no manual app pre-registration needed.
4. Browser opens `https://prizmad.com/oauth/authorize` → user signs in (NextAuth) → consent screen → redirect back with `code`.
5. Client exchanges `code` for an RS256 JWT (audience `https://prizmad.com/api/mcp`).
6. Client calls `https://prizmad.com/api/mcp` with `Authorization: Bearer <jwt>`. Refresh tokens rotate; PKCE S256 mandatory.

Compatible clients: **Claude Desktop, Claude.ai, ChatGPT, Cursor, Zed, Claude Code CLI**. Same-origin design (authorize/token/register all live on `prizmad.com`) sidesteps the Claude.ai web bug that ignores cross-origin authorization endpoints.

### Streamable HTTP config (when manual config is needed)

```json
{
  "mcpServers": {
    "prizmad": {
      "transport": "streamable-http",
      "url": "https://prizmad.com/api/mcp"
    }
  }
}
```

The Connect flow handles the Bearer token automatically. To pre-fill an existing API key instead, add `"headers": { "Authorization": "Bearer przmad_sk_live_..." }`.

### stdio-only clients (via the published bridge)

```json
{
  "mcpServers": {
    "prizmad": {
      "command": "npx",
      "args": ["-y", "@prizmad/mcp-server"],
      "env": { "PRIZMAD_API_KEY": "przmad_sk_live_..." }
    }
  }
}
```

Or via the generic bridge: `["mcp-remote", "https://prizmad.com/api/mcp"]`.

## Authentication options at a glance

| Path | When | Token shape |
|---|---|---|
| OAuth 2.1 Authorization Code + PKCE + DCR | Interactive clients (Claude Desktop, etc.) | RS256 JWT (1 h), refresh rotated (30 d) |
| OAuth 2.0 client_credentials | Headless / server-to-server | HS256 JWT (1 h) |
| API key (Bearer) | Quickest for dev, scripts, internal tools | `przmad_sk_live_...` |

All three resolve to the same per-user identity. Discovery: `/.well-known/oauth-authorization-server`, `/.well-known/oauth-protected-resource`, `/.well-known/jwks.json`.

## Available tools

| Tool | Auth | Purpose |
|------|:----:|---------|
| `list_templates` | No | Full template catalog with features and token costs |
| `list_avatars` | No | Built-in avatar presets with recommended voices |
| `recommend_template` | No | Pick top-3 templates from intent + constraints (voice / avatar / duration / budget). Use this **before** `create_video` instead of guessing from the catalog. |
| `list_my_videos` | Yes | Recent projects with projectUrl / shareUrl / downloadUrl |
| `upload_image` | Yes | Upload an image (URL or base64) → returns prizmad.com-hosted URL for use as productImages / avatar |
| `create_video` | Yes | Start a render. Returns `videoId`. |
| `get_video_status` | Yes | Snapshot status by default; with `wait: true` it blocks server-side and streams `notifications/progress` until terminal (up to 10 min) — preferred over polling. |
| `get_download_url` | Yes | Authenticated download URL on prizmad.com for a completed video |
| `create_video_batch` | Yes | Launch up to 20 renders in parallel; pre-checks total token cost |

`list_templates` and `list_avatars` are deliberately public so an agent can browse before authenticating.

## Customization knobs on `create_video` (and per-item in `create_video_batch`)

All optional; omit any field for a randomised pick at render time.

### `captionStyle` — on-video subtitles

| Value | Look |
|-------|------|
| `classic` | TikTok yellow highlight, white sans-serif (default fallback) |
| `bold-impact` | MrBeast / Hormozi — uppercase Anton with heavy black stroke and green highlight |
| `karaoke` | Active word colour-sweeps left-to-right as it's spoken |
| `pop` | Active word springs up in scale, inactive words dim |
| `bounce` | Active word hops up with a low-damping spring (Fredoka) |
| `neon` | Pink/cyan glowing halo (Audiowide) |
| `typewriter` | Characters reveal one-by-one with a blinking caret (JetBrains Mono) |
| `glow` | Soft white halo, gentle scale on the active word (Montserrat) |

### `musicStyle` — background music

`energetic`, `friendly`, `professional`, `luxury`, `funny`, `cinematic`, `lo-fi`, `hip-hop`, `acoustic`.

### `ctaStyle` — end-card

| Value | Look |
|-------|------|
| `classic` | Solid dark backdrop, centered title + price, green "LINK BELOW" pill |
| `blurred-photo` | First creative blurred behind minimal cream title + price + circled down-arrow |
| `dark-solid` | Same minimal layout on a solid warm-dark backdrop |

### `imageStyle` — lighting/palette preset for AI creatives (10 options)

`warm-golden`, `bright-neutral`, `cool-diffused`, `window-light`, `earthy-ambient`, `studio-clean`, `moody-dramatic`, `pastel-soft`, `nordic-minimal`, `sunset-warm`.

### Free-text prompt hints

`imagePromptHint`, `videoPromptHint`, `musicPromptHint` — each ≤ 400 chars, layered on top of style presets.

## Output URLs (what to give the user)

`get_video_status` returns three URLs once rendering is complete — they map cleanly:

| Field | Goes to |
|---|---|
| `projectUrl` | `https://prizmad.com/projects/<id>` — owner-only dashboard with player, remix, edit, asset library. **Primary link** when handing the result back to the signed-in user. |
| `shareUrl` | `https://prizmad.com/share/<token>` — public share page, useful only when forwarding the video to someone *outside* the account. |
| `downloadUrl` | `https://prizmad.com/api/v1/videos/<id>/download` — authenticated mp4 stream proxied via prizmad.com. |

The raw Vercel Blob URL is **never** surfaced to the agent.

## Typical flow

```text
recommend_template ─► create_video ─► get_video_status (wait: true) ─► projectUrl + downloadUrl
```

1. (No auth) `recommend_template({ intent, hasVoiceover, hasAvatar, targetDurationSec, maxTokens })` — pick a template.
2. (Optional) `upload_image` if the agent wants to attach product photos that aren't yet on the web.
3. `create_video` with `templateId` + `productUrl` (scraped) or explicit `productTitle` / `productDescription` / `productImages` + any customisation. Returns `videoId`.
4. `get_video_status({ videoId, wait: true })` — blocks server-side, streams progress, returns the final payload when ready (3–8 minutes typical, capped at 10 min).
5. Hand `projectUrl` to the user.

For bulk campaigns use `create_video_batch` (1–20 per request).

## Plan + token rules

- API video generation requires a **Pro subscription** (`/api/v1/videos` returns 403 with a clear upgrade message otherwise).
- Tokens come from the user's monthly plan first, then any top-up balance.
- Insufficient balance returns a 402 with a top-up URL.
- The MCP layer renders both errors as plain English so the assistant can repeat them verbatim.

## Rate limits

- Per-key / per-OAuth-client rate limit on the MCP endpoint.
- Each successful `create_video` debits the caller's Prizmad token balance.

---
> Source: [prizmad/agent-skills](https://github.com/prizmad/agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
