---
name: mqtt
description: Implement MQTT messaging avoiding security, QoS, and connection management pitfalls. Use when this capability is needed.
metadata:
  author: openclaw
---

## Security Traps
- Default Mosquitto allows anonymous connections — bots scan constantly, always configure auth
- TLS mandatory for external access — credentials travel plaintext otherwise
- Duplicate client IDs cause connection fights — both clients repeatedly disconnect each other
- ACLs should restrict topic access — one compromised device shouldn't read all topics

## QoS Misunderstandings
- Effective QoS is minimum of publisher and subscriber — broker downgrades if subscriber requests lower
- QoS 1 may duplicate messages — handlers must be idempotent
- QoS 2 has significant overhead — only use for commands where duplicates cause problems
- QoS applies per-message — can mix within same topic

## Topic Design Pitfalls
- Starting with `/` creates empty first level — `home/temp` not `/home/temp`
- Wildcards only work in subscriptions — can't publish to `home/+/temperature`
- `#` matches everything including nested — `home/#` gets `home/a/b/c/d`
- Some brokers limit topic depth — check before designing deep hierarchies

## Connection Management
- Clean session false preserves subscriptions — messages queue while disconnected, can surprise
- Keep-alive too long = delayed dead client detection — 60s is reasonable default
- Reconnection logic is client responsibility — most libraries don't auto-reconnect by default
- Will message only fires on unexpected disconnect — clean disconnect doesn't trigger it

## Retained Message Traps
- Retained messages persist until explicitly cleared — old data confuses new subscribers
- Clear retained with empty message + retain flag — not obvious from docs
- Birth/will pattern: publish "online" retained on connect, will publishes "offline"

## Mosquitto Specifics
- `persistence true` survives restarts — without it, retained messages and subscriptions lost
- `max_queued_messages` prevents memory exhaustion — one slow subscriber shouldn't crash broker
- `listener 1883 0.0.0.0` binds all interfaces — use `127.0.0.1` for local-only

## Debugging
- Subscribe to `#` sees all traffic — never in production, leaks everything
- `$SYS/#` exposes broker metrics — client count, bytes, subscriptions
- Retained messages persist after fixing issues — explicitly clear them
- `mosquitto_sub -v` shows topic with message — essential for debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
