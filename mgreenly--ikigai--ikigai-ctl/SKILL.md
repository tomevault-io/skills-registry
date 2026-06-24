---
name: ikigai-ctl
description: Control socket client for programmatic interaction with running ikigai Use when this capability is needed.
metadata:
  author: mgreenly
---

# ikigai-ctl

Command-line client for sending requests to a running ikigai instance via its Unix domain control socket. Useful for agents, test harnesses, and external tooling that need to inspect ikigai state programmatically.

## Invocation

```bash
ikigai-ctl <message-type>                        # Auto-discover socket
ikigai-ctl <message-type> --socket PATH           # Explicit socket path
```

## Socket Auto-Discovery

When `--socket` is not specified, the script looks in `$IKIGAI_RUNTIME_DIR` for socket files matching `ikigai-*.sock`:

- **One socket found** — uses it automatically
- **No sockets** — error: ikigai is not running
- **Multiple sockets** — error: lists all sockets, asks you to specify one with `--socket`

Socket path convention: `$IKIGAI_RUNTIME_DIR/ikigai-<pid>.sock`

| Context | IKIGAI_RUNTIME_DIR |
|---------|--------------------|
| Development (.envrc) | `$PWD/run` |
| Production | `${XDG_RUNTIME_DIR}/ikigai` |

## Protocol

- Newline-delimited JSON over Unix domain socket
- Stateless: one request in, one response out
- Single client at a time

## Available Requests

### read_framebuffer

Captures exactly what is on screen at the moment of the request.

```bash
ikigai-ctl read_framebuffer
```

Response fields:
- `type` — `"framebuffer"`
- `rows`, `cols` — screen dimensions
- `cursor` — `{row, col, visible}`
- `lines` — array of `{row, spans}` where each span has `text` and `style`

Style attributes (only present when set): `fg` (0-255), `bg` (0-255), `bold`, `dim`, `reverse`.

Every screen row is represented. Empty rows have a single span with empty text.

### send_keys

Injects keystrokes into the running ikigai instance as if typed by the user.

```bash
ikigai-ctl send_keys "hello world\r"
ikigai-ctl send_keys "test\x1b"              # Supports C-style escape sequences
```

The keys argument supports C-style escape sequences:
- `\r` — carriage return (Enter)
- `\n` — newline
- `\x1b` or `\e` — escape
- `\t` — tab

Response fields:
- `type` — `"ok"` on success
- `error` — error message on failure

### Future Messages

Additional request types planned (same request/response pattern):
- `read_scrollback` — scrollback buffer contents
- `read_input_buffer` — current input line
- `read_completion` — active completion state

## Source Files

- `scripts/bin/ikigai-ctl` — client script
- `apps/ikigai/control_socket.c` — server-side socket lifecycle
- `apps/ikigai/serialize_framebuffer.c` — framebuffer JSON serializer
- `project/control-socket.md` — full protocol specification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgreenly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
