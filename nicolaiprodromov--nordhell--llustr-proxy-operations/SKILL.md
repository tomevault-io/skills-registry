---
name: llustr-proxy-operations
description: Use when working in the llustr-proxy repository to start VPN tunnels, inspect Docker Compose state, verify SOCKS5 proxy connectivity, operate the optional FastAPI server, or troubleshoot setup issues such as missing .env values, VPN configs, ports, or the shared Docker network.
metadata:
  author: nicolaiprodromov
---

# LLUSTR Proxy Operations

This skill is the project runbook for the llustr-proxy repository. Use it when a task involves running the stack, validating tunnels, debugging startup failures, or using the local API server.

## When To Use

- Start one tunnel or a tunnel range with `just`.
- Validate local prerequisites before running Docker containers.
- Inspect running passages, logs, ports, health, and exit IPs.
- Verify that the SOCKS5 proxy is actually routing traffic through the VPN.
- Operate the optional FastAPI dashboard and API under `server/`.
- Troubleshoot common startup problems such as missing `.env`, absent VPN configs, missing Docker network, port collisions, or broken VPN connectivity.

## Core Workflow

1. Confirm prerequisites.
   - Docker must be available and running.
   - `just` must be installed.
   - `.env` must define `VPN_USERNAME` and `VPN_PASSWORD`.
   - `vpn-configs/` must contain `.ovpn` files, or the user must be willing to download them.

2. Validate the repo setup.
   - Run `just check` for an overall sanity check.
   - If configs are missing or stale, run `just update-configs`.

3. Start tunnels through the supported entrypoint.
   - Start one tunnel with `just start 0`.
   - Start a range with `just start 0-4` or `just range 0 4`.
   - Force a rebuild with `just rebuild 0`.
   - Refresh configs plus startup with `just fresh 0` or `just fresh-rebuild 0`.
   - The startup script now creates the shared Docker network `nordhell-network` automatically if it is missing.

4. Verify that startup produced a usable proxy.
   - Inspect state with `just status`.
   - Test all active tunnels with `just test`.
   - Test a single published port with `just test-port 1080`.
   - Check a specific tunnel exit IP with `just exit-ip 0`.

5. Inspect or troubleshoot failures.
   - View logs with `just logs 0`, `just tail 0`, or `just follow 0`.
   - Inspect Docker state with `just info 0`, `just ps`, or `just inspect 0`.
   - Check health with `just health 0`.
   - Stop and retry with `just stop 0` then `just start 0`.

6. Use the optional server only after the Docker workflow works.
   - Start it with `just server`.
   - The server wraps the shell scripts and exposes HTTP endpoints for start, stop, status, and replace operations.

## Decision Points

- If the user is new to the repo or the environment looks uninitialized: start with `just check` before attempting any build or run command.
- If `vpn-configs/` is empty: run `just update-configs` or `just fresh 0`.
- If `just start` fails during Compose startup: inspect `docker compose` logs and confirm the container exists, has `/dev/net/tun`, and can establish `tun0`.
- If a tunnel starts but traffic does not route through the VPN: compare host IP vs proxy IP with `just test`.
- If multiple tunnels are needed: prefer the range-aware `just` commands instead of ad hoc docker commands so port assignment stays consistent.
- If the user only needs API access or the dashboard: get at least one tunnel working first, then run the server.

## Quality Checks

A startup task is complete only when all of the following are true:

- The target container `passage-<id>` is running.
- A SOCKS5 port is published.
- `just status` shows the tunnel as up.
- `just test` or an equivalent single-port test returns an exit IP different from the host IP.

A troubleshooting task is complete only when:

- The root cause is identified, not just the visible error.
- The fix uses the supported repo workflow in `justfile` and `scripts/` unless there is a documented reason not to.
- Any permanent workflow change is reflected in docs or automation.

## Preferred Commands

Use the repo entrypoints before raw Docker commands:

- `just start <id-or-range>`
- `just rebuild <id>`
- `just fresh <id>`
- `just stop <id|all>`
- `just status`
- `just test`
- `just logs <id>`
- `just info <id>`
- `just exit-ip <id>`
- `just server`

Command details and troubleshooting cues are in [references/commands.md](./references/commands.md).

## Implementation Notes

- Tunnel images and containers are parameterized by `VPN_CONFIG_NUM` and named like `passage-0`.
- Compose projects are named like `nordhell-0`.
- SOCKS5 ports begin at `SOCKS_BASE_PORT` and increment to avoid collisions.
- The container boot sequence is: choose `.ovpn` file, start OpenVPN, wait for `tun0`, verify connectivity, then launch Dante.

## Example Prompts

- `/llustr-proxy-operations start tunnel 0 and verify it works`
- `/llustr-proxy-operations troubleshoot why just start 3 fails`
- `/llustr-proxy-operations show me how to test all active proxies`
- `/llustr-proxy-operations explain how to run the FastAPI dashboard safely`

---
> Source: [nicolaiprodromov/nordhell](https://github.com/nicolaiprodromov/nordhell) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
