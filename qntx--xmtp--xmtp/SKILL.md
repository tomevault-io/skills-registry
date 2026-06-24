---
name: xmtp
description: >- Use when this capability is needed.
metadata:
  author: qntx
---

# XMTP Messaging

Use the `xmtp` CLI tool for decentralized end-to-end encrypted messaging.
All data commands support `--json` for structured output. The binary is
built from this repository (`cargo build -p xmtp-cli`).

## Profile Setup

Profiles store identity keys and message databases. A default profile is
auto-created on first TUI launch, but agents should create one explicitly.

```bash
# Create a new profile (generates a random Ethereum identity)
xmtp new mybot --env dev

# Import an existing private key
xmtp new mybot --import 0xHEX_PRIVATE_KEY

# List profiles
xmtp list --json
# → {"profiles":[{"name":"mybot","address":"0x...","signer":"file","env":"dev","is_default":true}]}

# Show/set default profile
xmtp default --json
xmtp default mybot

# Profile details
xmtp info --json
# → {"profile":"mybot","env":"dev","address":"0x...","inbox_id":"...","signer":"file","installations":[...]}
```

## Listing Conversations

```bash
# All conversations
xmtp conversations --json

# Filter by consent state: allowed, denied, unknown
xmtp conversations --consent allowed --json

# Use a specific profile
xmtp conversations --profile mybot --json
```

Response format:

```json
{
  "conversations": [
    {
      "id": "abc123...",
      "type": "dm",
      "name": null,
      "last_message": "Hello!",
      "last_message_ns": 1710000000000000000
    }
  ]
}
```

## Reading Messages

```bash
xmtp messages <conversation_id> --json
xmtp messages <conversation_id> --limit 20 --json
```

Response format:

```json
{
  "conversation_id": "abc123...",
  "messages": [
    {
      "id": "msg001...",
      "conversation_id": "abc123...",
      "sender_inbox_id": "inbox_xyz...",
      "sent_at_ns": 1710000000000000000,
      "delivery_status": "published",
      "content": { "type": "text", "text": "Hello!" }
    }
  ]
}
```

Content types: `text`, `markdown`, `reaction`, `reply`, `read_receipt`,
`attachment`, `remote_attachment`, `unknown`, `system`.

## Sending Messages

```bash
xmtp send <conversation_id> "Hello from the agent!" --json
# → {"ok":true,"message_id":"msg002...","conversation_id":"abc123..."}
```

## Creating Conversations

```bash
# Create/open a DM (by address, ENS name, or inbox ID)
xmtp dm 0x1234...abcd --json
xmtp dm vitalik.eth --json
# → {"conversation_id":"...","type":"dm","peer":"vitalik.eth"}

# Create a group
xmtp group 0xAddr1 0xAddr2 --name "Team Chat" --json
# → {"conversation_id":"...","type":"group","name":"Team Chat","members":["0xAddr1","0xAddr2"]}
```

## Managing Conversations

```bash
# List group members
xmtp members <conversation_id> --json
# → {"conversation_id":"...","members":[{"inbox_id":"...","addresses":["0x..."],"permission":"member","consent":"allowed"}]}

# Accept/deny conversation requests
xmtp request <conversation_id> accept --json
xmtp request <conversation_id> deny --json

# Check if addresses can receive XMTP messages
xmtp can-message 0xAddr1 0xAddr2 --json
# → {"results":[{"address":"0xAddr1","can_message":true},{"address":"0xAddr2","can_message":false}]}
```

## Real-Time Streaming

The `stream` command outputs NDJSON (one JSON object per line) and runs
until interrupted. Use this to monitor incoming messages in real time.

```bash
# Stream everything (messages + new conversations)
xmtp stream all --profile mybot

# Stream only messages
xmtp stream messages --profile mybot

# Stream only conversation updates
xmtp stream conversations --profile mybot
```

NDJSON event types:

```json
{"type":"ready","stream":"all"}
{"type":"message","message_id":"...","conversation_id":"...","sender_inbox_id":"...","sent_at_ns":...,"delivery_status":"published","content":{"type":"text","text":"Hi"}}
{"type":"conversation","conversation_id":"...","conversation_type":"dm","name":null}
```

## Agent Workflow: Monitor and Respond

1. Start streaming in a background process
2. Parse each NDJSON line for incoming messages
3. Fetch conversation context when needed
4. Send a response

```bash
# 1. Stream messages (long-running, background)
xmtp stream messages --profile mybot

# 2. When a message arrives, get context
xmtp messages <conversation_id> --limit 20 --json --profile mybot

# 3. Send a response
xmtp send <conversation_id> "Got it, processing your request..." --json --profile mybot
```

## Error Handling

- On success with `--json`: structured JSON on stdout, exit code 0
- On failure with `--json`: `{"error":"description"}` on stdout, exit code 1
- On failure without `--json`: error message on stderr, exit code 1

## Profile Selection

All commands accept `--profile <name>`. If omitted, the default profile
is used. Profile commands (`list`, `info`, `default`) use a positional
`name` argument instead.

---
> Source: [qntx/xmtp](https://github.com/qntx/xmtp) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
