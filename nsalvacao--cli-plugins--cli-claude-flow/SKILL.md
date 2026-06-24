---
name: cli-claude-flow
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# claude-flow CLI Reference

Expert command reference for **claude-flow** v3.1.0-alpha.3.

- **51** commands (3 with subcommands)
- **7** command flags + **8** global flags
- **17** usage examples
- Max nesting depth: 1

## When to Use

This skill applies when:
- Constructing or validating `claude-flow` commands
- Looking up flags, options, or subcommands
- Troubleshooting `claude-flow` invocations or errors
- Needing correct syntax for `claude-flow` operations

## Prerequisites

Install claude-flow:
```bash
npm install -g claude-flow@latest
```

## Quick Reference

| Command | Description |
| --- | --- |
| `claude-flow agent` | Agent Management Commands |
| `claude-flow analyze` | Analyze Commands |
| `claude-flow claims` | Claude Flow Claims System |
| `claude-flow completions` | Shell Completions |
| `claude-flow config` | Configuration Management |
| `claude-flow daemon` | Worker Daemon - Background Task Management |
| `claude-flow deployment` | Claude Flow Deployment |
| `claude-flow doctor` | claude-flow doctor |
| `claude-flow embeddings` | Claude Flow Embeddings |
| `claude-flow guidance` | Guidance Control Plane |
| `claude-flow hive-mind` | Hive Mind - Consensus-Based Multi-Agent Coordination |
| `claude-flow hooks` | Self-Learning Hooks System |
| `claude-flow init` | Claude Flow appears to be already initialized |
| `claude-flow issues` | Issue Claims (ADR-016) |
| `claude-flow mcp` | MCP Server Management |
| `claude-flow memory` | Memory Management Commands |
| `claude-flow migrate` | V2 to V3 Migration Tools |
| `claude-flow neural` | Claude Flow Neural System |
| `claude-flow performance` | Claude Flow Performance Suite |
| `claude-flow plugins` | Claude Flow Plugin System |
| `claude-flow process` | claude-flow process |
| `claude-flow progress` |  |
| `claude-flow providers` | claude-flow providers |
| `claude-flow route` | claude-flow route |
| `claude-flow ruvector` | RuVector PostgreSQL Bridge |
| `claude-flow security` | Claude Flow Security Suite |
| `claude-flow session` | Session Management Commands |
| `claude-flow start` | Starting Claude Flow V3 |
| `claude-flow status` |  |
| `claude-flow swarm` | Swarm Coordination Commands |
| `claude-flow task` | Task Management Commands |
| `claude-flow update` | Update Command |
| `claude-flow workflow` | Workflow Commands |

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--config` | `-c` | string | Path to configuration file |
| `--format` | `-f` | bool | Output format (text, json, table) |
| `--help` | `-h` | bool | Show help information |
| `--interactive` | `-i` | bool | Enable interactive mode |
| `--no-color` | `` | bool | Disable colored output |
| `--quiet` | `-Q` | bool | Suppress non-essential output |
| `--verbose` | `-v` | bool | Enable verbose output |
| `--version` | `-V` | bool | Show version number |

## Command Overview


### Commands

`agent`, `analyze`, `claims`, `completions`, `config`, `daemon`, `deployment`, `doctor`, `embeddings`, `guidance`, `hive-mind`, `hooks`, `init`, `issues`, `mcp`, `memory`, `migrate`, `neural`, `performance`, `plugins`, `progress`, `ruvector`, `security`, `session`, `start`, `status`, `swarm`, `task`, `update`, `workflow`

### Command Groups

`process`, `providers`, `route`

## Common Usage Patterns

```bash
claude-flow doctor
```
Run full health check

```bash
claude-flow doctor --fix
```
Show fixes for issues

```bash
claude-flow doctor --install
```
Auto-install missing dependencies

```bash
claude-flow doctor -c version
```
Check for stale npx cache

```bash
claude-flow doctor -c claude
```
Check Claude Code CLI only

```bash
claude-flow process daemon --action start
```
Start daemon

```bash
claude-flow process monitor --watch
```
Watch processes

```bash
claude-flow process workers --action list
```
List workers

## Detailed References

For complete command documentation including all flags and subcommands:
- **Full command tree:** see `references/commands.md`
- **All usage examples:** see `references/examples.md`

## Troubleshooting

- Run `claude-flow doctor` to diagnose issues
- Use `claude-flow --help` or `claude-flow <command> --help` for inline help
- Add `--verbose` for detailed output during debugging

## Re-scanning

To update this plugin after a CLI version change, run the `/scan-cli` command
or manually execute the crawler and generator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
