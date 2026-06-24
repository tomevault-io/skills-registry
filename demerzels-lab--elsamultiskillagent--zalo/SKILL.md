---
name: zalo
description: Guidance for integrating Zalo (Zalo OA/ZNS): flows, API usage, webhooks, and operational safety. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# Zalo Integration Guide

## Goal
Provide a practical baseline for integrating Zalo (OA/ZNS): auth, messaging, webhooks, and operational considerations.

## Use when
- You need to design a notification flow via Zalo.
- You need to describe webhook handling from Zalo.
- You want a security/rate-limit checklist.

## Do not use when
- The request involves bypassing policies or collecting data unlawfully.

## Core topics
- Auth: tokens, refresh, scopes.
- Messaging: payload formats by message type.
- Webhooks: signature validation, retries, idempotency.
- Ops: rate limits, logging, error tracking.

## Required inputs
- Account type (OA/ZNS).
- Use case (alerts, CS, transactional).
- Environment (dev/prod) and sandbox needs.

## Expected output
- A clear integration plan with a technical checklist.

## Notes
- Never put secrets in logs.
- Always validate webhook signatures and protect against replay.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
