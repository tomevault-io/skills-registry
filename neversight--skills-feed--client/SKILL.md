---
name: client
description: VuerClient for connecting to Vuer servers and sending custom events (plugin:vuer@vuer) Use when this capability is needed.
metadata:
  author: neversight
---

# VuerClient

## Basic Usage

```python
import asyncio
from vuer import VuerClient
from vuer.events import ClientEvent

class MyEvent(ClientEvent):
    etype = "MY_EVENT"

async def main():
    async with VuerClient(URI="ws://localhost:8012") as client:
        client.send @ MyEvent(value={"data": 123})  # Fire-and-forget
        await client.send(MyEvent(value="x"))       # Awaitable

asyncio.run(main())
```

## Custom Events

```python
class SetPositionEvent(ClientEvent):
    etype = "SET_POSITION"
    value = [0, 0, 0]  # Optional default
```

## Receiving

```python
event = await client.recv(timeout=5.0)

async for event in client:
    print(event.etype, event.value)
```

## Server Handler

```python
@app.add_handler("MY_EVENT")
async def on_event(event, session: VuerSession):
    session.upsert @ Box(key="box", position=event.value)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
