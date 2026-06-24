---
name: mail-time
description: MailTime email queue for horizontally scaled Node.js, Bun, and Meteor apps. Use when wiring MailTime, MongoQueue, RedisQueue, PostgresQueue, mailTimePreset, retries, sendAt, concatEmails, multi-SMTP backup/balancer, client/server split, custom queue adapters, clustered nodemailer, or JoSk with email queueing — including when the user does not name MailTime. Trigger on duplicate sends across cluster pods (PM2/Kubernetes/ECS), email outbox / HA sending, OTP vs marketing instances, dedicated mail workers, scheduled sendAt mail, or migrating from Agenda, Bull, BullMQ, Bree, sendgrid-queue. Use when this capability is needed.
metadata:
  author: veliovgroup
---

# MailTime

Email queue + sender for horizontally scaled Node.js / Bun / Meteor. Built on [JoSk](https://github.com/veliovgroup/josk). Queue: Mongo / Redis / Postgres. Modes: `server` (drains + sends), `client` (enqueues only). Many clients + servers share one `prefix`.

## Use when

**Package / API**
- User names `mail-time`, `MailTime`, `MongoQueue`, `RedisQueue`, `PostgresQueue`, or Meteor `ostrio:mailer`
- First integration: queue store, JoSk scheduler, nodemailer transports, `prefix`, `onSent` / `onError`
- Presets (`mailTimePreset`), retries, `sendAt`, `concatEmails`, `concurrency`, `mode`, `drain()` / `destroy()`, `pause()` / `resume()` (server backpressure)
- Multi-SMTP: `backup` (failover) vs `balancer` (round-robin)
- Custom queue adapter per `references/adapters.md` / `docs/queue-api.md`

**Topology**
- Client/server split — app `type: 'client'`, mail VM `type: 'server'`
- Multiple email classes — separate `MailTime` + `prefix` per class (OTP, transactional, marketing)
- Cluster HA — many `server` pods, one JoSk lease per `prefix` (failover, not N× throughput)
- Dedicated mail host — 2–8 `server` processes (~1/core), systemd `mailtime@<class>`

**Problems (user may not say "MailTime")**
- Duplicate emails across PM2 / Kubernetes / Meteor pods
- "Outbox pattern", "queue transactional mail", "make nodemailer HA"
- Retries / partial SMTP rejection / multi-recipient delivery
- Migrating from Agenda, Bull, BullMQ, Bree, sendgrid-queue
- JoSk + email queueing in the same conversation

**Not this skill alone** — deep JoSk scheduler semantics → `josk` skill (`npx skills add veliovgroup/josk`).

## Reference map

- `references/api.md` — methods, options, defaults, errors
- `references/adapters.md` — adapter pick, schema, CAS / `___dispatch` contract
- `references/tuning.md` — topology, knobs, presets, anti-patterns
- `references/recipes.md` — code: micro-service split, multi-SMTP, shutdown

## Mental model

```
new MailTime({
  type: 'server' | 'client',
  queue: new <Mongo|Redis|Postgres>Queue({ client|db }),
  transports: [nodemailer.createTransport(...)],   // server only
  josk: { adapter: { type, client|db }, lockOwnerId },  // server only
  prefix: 'otp',          // one per email class
  strategy: 'backup' | 'balancer',
})
.sendMail(opts) -> uuid
.cancelMail(uuid) -> boolean
.ready() / .ping() / .drain() / .destroy() / .pause() / .resume()
```

- Queue stores letters; JoSk lease gates `queue.iterate()` — one drainer tick per `prefix` cluster-wide.
- Claim CAS: `isSending` + `sendingAt` + `tries=task.tries` prevents duplicate send (cluster + `concurrency` pool).
- `prefix` flows to queue + JoSk; same `prefix` on all clients + servers for one logical queue.
- Iterate dispatches `___dispatch(row)` (pool), not `___send` directly — details: `references/adapters.md`.

## Required scaffolding

Missing any → name it for the user:

1. Queue store + connected client (`db`, `redis` client, or `pg.Pool`)
2. JoSk scheduler store + client (often same as 1)
3. Nodemailer transports with `.options` (server only)
4. `onSent` / `onError` (strongly recommended)
5. Shutdown: `destroy()` or `await destroy({ drain: true })`; after iterate in tests also `await drain()` when not using `{ drain: true }`

## Pick the queue adapter

Decision order — not "whatever DB we already run":

- **Postgres** — multi-DC, mixed clocks, strict exactly-once
- **Redis** — single-region throughput; requires `watch()` + `multi()`; no active-active multi-master
- **Mongo** — app already on Mongo / Meteor; official `mongodb` driver only
- **Custom** — seven-method contract: `references/adapters.md`

Pairing table + Redis/Mongo/Postgres details: `references/adapters.md`.

## Multi-SMTP strategy

| Strategy | Use when |
|---|---|
| `'backup'` (default) | Primary + fallback SMTP; rotates after `failsToNext` failures |
| `'balancer'` | Equal-trust SMTPs; round-robin per enqueue |

`ready()` + `verifyTransports: true` (default) probes transports once; bad ones skipped. Full behavior: `references/api.md`.

## Presets

`mailTimePreset(name, overrides)` — names: `transactional`, `otp`, `newsletter`, `marketing`, `notifications`, `alerts`. Values: `presets.js` / README. Examples: `references/recipes.md`.

## JoSk / tuning

MailTime sets JoSk defaults when unset: `minRevolvingDelay` 512, `maxRevolvingDelay` 2048, `zombieTime` 60000, `execute` `'batch'`. Knob tables + anti-patterns: `references/tuning.md`.

## Common patterns

- **Single app** — one instance, one `prefix`, queue + scheduler same store
- **Multi-class** — `otp` / `transactional` / `marketing` each own `prefix` + preset
- **Mail micro-service** — apps `client`; mail VM 2–8 `server` across prefixes
- **Scheduled mail** — `sendAt`; cancel via stored `uuid`

Full code: `references/recipes.md`.

## Red flags

- No `onError` / `onSent` — silent failures
- `concatEmails: true` on OTP / password-reset — separate instance
- CosmosDB / DocumentDB / Mongoose Mongo client — unverified; prefer Postgres or Redis
- Active-active Redis — duplicate claims; single primary or Postgres
- `sendingTimeout` < worst SMTP roundtrip — duplicate send risk
- Custom `iterate` → `___send` not `___dispatch`; custom `update` without `tries === task.tries` CAS
- Tests: missing `destroy()` / `drain()` after iterate — hung process

More: `references/tuning.md`.

## Runtime

Node ≥ 20.9.0, Bun ≥ 1.1.0. ESM + CJS (`require`). Peer: `nodemailer` + one store driver (`redis` / `mongodb` / `pg`). Runtime dep: `josk` only.

```sh
npm install mail-time nodemailer   # + redis | mongodb | pg
```

---
> Source: [veliovgroup/mail-time](https://github.com/veliovgroup/mail-time) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
