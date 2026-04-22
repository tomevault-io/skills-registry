---
name: realtime
description: Real-time communication patterns with Action Cable and Trailblazer Operations. Use when implementing WebSocket channels, broadcasting updates, or handling real-time features. Covers channel organization, authorization, messaging patterns, and operation broadcasting. Use when this capability is needed.
metadata:
  author: doncan-orozco
---

# Realtime (Action Cable + Trailblazer 2.1)

## Dependencies
- actioncable
- trailblazer-operation
- trailblazer-rails

## Channels Organization (Pattern)
- `app/channels/` hosts channel classes
- One channel per domain concept (game/room)
- Authorization happens in `subscribed`
- Use `stream_for` with domain instances
- Keep channels thin and delegate logic to Operations

## Messages (Pattern)
- JSON payloads with `type` and small deltas
- Include timestamps in broadcasts

## Operations Broadcasting (Pattern)
- Broadcast inside Operations after successful state changes
- Example: `Game::BroadcastChannel.broadcast_to(game, { type: 'player_moved', ... })`

See [Channels](../../VERIFICATION_CHECKLIST.md#channels-action-cable) for requirements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doncan-orozco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
