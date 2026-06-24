---
name: libchat
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libchat Skill

## When to Use

- Embedding chat interfaces in web applications
- Building dedicated chat page experiences
- Adding conversational AI to existing websites
- Creating custom chat UI with authentication

## Key Concepts

**ChatClient**: Composed client managing auth state, API communication, and
conversation persistence via localStorage.

**ChatDrawerElement**: Collapsible drawer component for unobtrusive chat
integration on any page.

**ChatPageElement**: Full-page chat component for dedicated chat experiences.

## Usage Patterns

### Pattern 1: Embed chat drawer

```javascript
import { ChatClient, ChatDrawerElement } from "@copilot-ld/libchat";

const client = new ChatClient({
  chatUrl: "/web/api/chat",
  auth: { url: "http://localhost:9999", anonKey: "key" },
});

document.querySelector("chat-drawer").client = client;
```

### Pattern 2: Full-page chat

```html
<chat-page data-name="Assistant"></chat-page>
<script type="module">
  import { ChatClient, ChatPageElement } from "@copilot-ld/libchat";
  const client = new ChatClient({ chatUrl: "/api" });
  document.querySelector("chat-page").client = client;
</script>
```

## Integration

Used by the UI extension. Communicates with Web extension chat API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
