---
name: cli-docker
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# docker CLI Reference

Compact command reference for **docker** v29.2.0.

- **271** total commands
- **1408** command flags + **11** global flags
- **7** extracted usage examples
- Max nesting depth: 3

## When to Use

- Constructing or validating `docker` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command Groups:
`builder`, `buildx`, `compose`, `container`, `context`, `image`, `manifest`, `mcp`, `network`, `plugin`, `swarm`, `system`, `volume`
Standalone Commands:
`attach`, `bake`, `build`, `commit`, `cp`, `create`, `diff`, `events`, `exec`, `export`, `history`, `images`, `import`, `info`, `inspect`, `kill`, `load`, `login`, `logout`, `logs`, `pause`, `port`, `ps`, `pull`, `push`
... +16 more in `references/commands.md`
Command format examples: `docker swarm`, `docker attach`, `docker bake`

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--config` | `` | string | Location of client config files (default |
| `--context` | `-c` | string | Name of the context to use to connect to the |
| `--debug` | `-D` | bool | Enable debug mode |
| `--host` | `-H` | string | Daemon socket to connect to |
| `--log-level` | `-l` | string | Set the logging level ("debug", "info", |
| `--tls` | `` | bool | Use TLS; implied by --tlsverify |
| `--tlscacert` | `` | string | Trust certs signed only by this CA (default |
| `--tlscert` | `` | string | Path to TLS certificate file (default |
| `--tlskey` | `` | string | Path to TLS key file (default |
| `--tlsverify` | `` | bool | Use TLS and verify the remote |
| `--version` | `-v` | bool | Print version information and quit |

## Common Usage Patterns (Compact)

```bash
docker mcp catalog add my-catalog github-server ./github-catalog.yaml
```
docker mcp catalog add my-catalog slack-bot ./team-catalog.yaml --force

```bash
docker mcp catalog create dev-servers
```
docker mcp catalog create prod-monitoring

```bash
docker mcp catalog fork docker-mcp my-custom-docker
```
docker mcp catalog fork team-servers my-servers

```bash
docker mcp catalog import https://example.com/my-catalog.yaml
```
docker mcp catalog import ./shared-catalog.yaml

```bash
docker mcp catalog ls
```
docker mcp catalog ls --format=json

## Detailed References

- Full command tree: `references/commands.md`
- Full examples catalog: `references/examples.md`

## Re-Scanning

After a CLI update, run `/scan-cli` or execute crawler + generator again.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
