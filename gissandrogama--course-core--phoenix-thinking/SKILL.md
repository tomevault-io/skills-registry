---
name: phoenix-thinking
description: Apply when adding LiveView pages, creating forms, real-time updates, broadcasting, new routes, API endpoints, fixing LiveView bugs, or when the user mentions mount, handle_event, handle_info, handle_params, channels, components, assigns, sockets, PubSub. Essential for avoiding duplicate queries in mount. Use when this capability is needed.
metadata:
  author: gissandrogama
---

# Phoenix Thinking

**Resumo (pt-BR):** Regras para Phoenix/LiveView: nunca fazer queries no mount (é chamado duas vezes). Carregar dados em handle_params. Scopes para autorização; PubSub com tópicos escopados.

Mental shifts for Phoenix applications. These insights challenge typical web framework patterns.

## The Iron Law

```
NO DATABASE QUERIES IN MOUNT
```

mount/3 is called TWICE (HTTP request + WebSocket connection). Queries in mount = duplicate queries.

```elixir
def mount(_params, _session, socket) do
  {:ok, assign(socket, posts: [], loading: true)}
end

def handle_params(params, _uri, socket) do
  posts = Blog.list_posts(socket.assigns.scope)
  {:noreply, assign(socket, posts: posts, loading: false)}
end
```

**mount/3** = setup only (empty assigns, subscriptions, defaults). **handle_params/3** = data loading (all database queries, URL-driven state). No exceptions.

## Scopes: Security-First Pattern (Phoenix 1.8+)

Scopes address Broken Access Control. Authorization context is threaded automatically.

## PubSub Topics Must Be Scoped

Unscoped topics = data leaks between tenants. Use e.g. `"posts:org:#{org.id}"`.

## External Polling: GenServer, Not LiveView

Single GenServer polls, broadcasts to all via PubSub—not one API call per user.

## Components vs LiveViews

- **Functional components:** Display-only, no internal state
- **LiveComponents:** Own state, handle own events
- **LiveViews:** Full page, owns URL, top-level state

## Async Data Loading

Use `assign_async/3` for data that can load after mount.

## Gotchas

- **terminate/2:** Only fires if trapping exits; don't rely on it in LiveView for cleanup—use a GenServer that monitors the LiveView.
- **start_async duplicate names:** Later call wins; use `cancel_async/3` first if aborting previous task.
- **Channel intercept socket:** State in `handle_out` is stale (snapshot at subscription time).
- **CSS class precedence:** Determined by stylesheet order, not HTML order—use variant props instead of class merging.
- **Upload content-type:** User-provided; validate actual file contents (magic bytes).
- **Webhooks:** Read body before Plug.Parsers to verify signatures; don't use `preserve_req_body: true` for all requests.

## Red Flags - STOP and Reconsider

- Database query in mount/3
- Unscoped PubSub topics in multi-tenant app
- LiveView polling external APIs directly
- Using terminate/2 for cleanup without trap_exit
- start_async with same name without cancel_async first
- Relying on socket.assigns in Channel intercepts (stale)
- Trusting `%Plug.Upload{}.content_type` for security

**Any of these? Re-read The Iron Law and the Gotchas section.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
