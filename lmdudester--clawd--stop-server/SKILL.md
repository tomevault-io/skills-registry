---
name: stop-server
description: Stop the Clawd dev server by killing processes on dev ports Use when this capability is needed.
metadata:
  author: lmdudester
---

Stop the Clawd dev server. Follow these steps exactly:

## 1. Check current port status

Run `netstat -ano | grep -E ":(3050|3051) " | grep LISTEN` to identify processes on the dev ports (3050 = server, 3051 = Vite client).

If no processes are listening, report that the server is already stopped and exit.

## 2. Kill processes

For each listening port, extract the PID and kill it with `taskkill //F //PID <pid>`.

**IMPORTANT:** Never kill processes on ports 3000-3001 — those are Docker containers.

## 3. Verify

Wait 2 seconds, then re-check both ports to confirm they are free. Report the final status of each port.

## 4. Also stop any background bash tasks

Check for any running background bash tasks (from previous `/start-server` invocations) and stop them with the TaskStop tool if found.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lmdudester) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
