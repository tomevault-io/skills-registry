---
name: xteink-display
description: Publishes messages to MQTT broker for XTeInk e-ink display Use when this capability is needed.
metadata:
  author: maddiedreese
---

# XTeInk Display Publisher

Publishes messages to an MQTT broker for display on ESP32 XTeInk.

## Configuration
- `mqtt_broker`: MQTT broker address (e.g., `broker.hivemq.com`)
- `mqtt_topic`: Topic to publish to (e.g., `tamagotchi/your-unique-id/display`)

## Workflow

**Critical Rules:**
- **NEVER** pass user's incoming messages to this script
- **ONLY** pass the AI assistant's outgoing messages
- Run the script **AFTER** sending your response
- Pass the **EXACT** same message you sent

**State mapping:**
- `idle` — default, waiting for activity
- `alert` — new message received from user
- `thinking` — processing/thinking
- `talking` — responding
- `working` — using a tool
- `excited` — task completed successfully
- `error` — something went wrong
- `sleeping` — idle for 5+ minutes

## Usage
```bash
# After responding to a user:
./script.sh "Your response message here" talking
```

The script publishes to MQTT broker (default: broker.hivemq.com) on your configured topic.

---
> Source: [maddiedreese/xteink-tamagotchi](https://github.com/maddiedreese/xteink-tamagotchi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-25 -->
