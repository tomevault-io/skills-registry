---
name: superdoc-core
description: Core SuperDoc usage guidelines for document editing applications. Use when integrating SuperDoc with vanilla JavaScript, Vue, Svelte, or other frameworks. Triggers on tasks involving DOCX editing, document rendering, or SuperDoc configuration. Use when this capability is needed.
metadata:
  author: superdoc-dev
---

# SuperDoc Core

SuperDoc is a document editing and rendering library for the web. It provides full DOCX support with real-time collaboration capabilities.

## When to Apply

Reference these guidelines when:
- Adding document editing to a vanilla JS application
- Integrating SuperDoc with Vue, Svelte, or Angular
- Configuring SuperDoc options
- Understanding the core API
- Setting up collaboration or AI features

## Installation

```bash
npm install superdoc
```

## Quick Start

```javascript
import { SuperDoc } from 'superdoc';
import 'superdoc/style.css';

const superdoc = new SuperDoc({
  selector: '#editor',
  document: file, // File, Blob, URL, or config object
  documentMode: 'editing',
  onReady: ({ superdoc }) => {
    console.log('Editor ready!');
  }
});
```

## Configuration

### Required Options

| Option | Type | Description |
|--------|------|-------------|
| `selector` | `string \| HTMLElement` | Container element |
| `document` | `File \| Blob \| string \| object` | Document to load |

### Mode & Role

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `documentMode` | `'editing' \| 'viewing' \| 'suggesting'` | `'editing'` | Editing mode |
| `role` | `'editor' \| 'viewer' \| 'suggester'` | `'editor'` | User permissions |

### User Options

| Option | Type | Description |
|--------|------|-------------|
| `user` | `{ name, email?, image? }` | Current user info |
| `users` | `Array<{ name, email, image? }>` | All users (for @-mentions) |

### UI Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `toolbar` | `string \| HTMLElement \| false` | auto | Toolbar container |
| `rulers` | `boolean` | `true` | Show rulers |
| `pagination` | `boolean` | `true` | Enable pagination |

### Event Callbacks

| Option | Type | Description |
|--------|------|-------------|
| `onReady` | `({ superdoc }) => void` | Instance initialized |
| `onEditorCreate` | `({ editor }) => void` | ProseMirror editor created |
| `onEditorDestroy` | `() => void` | Editor destroyed |
| `onEditorUpdate` | `({ editor }) => void` | Content changed |
| `onContentError` | `(event) => void` | Document parsing error |
| `onException` | `({ error }) => void` | Runtime error |

## Instance Methods

### Document Mode

```javascript
// Change editing mode
superdoc.setDocumentMode('viewing');
superdoc.setDocumentMode('editing');
superdoc.setDocumentMode('suggesting');
```

### Export

```javascript
// Export as DOCX blob
const blob = await superdoc.export({ triggerDownload: false });

// Export and trigger download
await superdoc.export({ triggerDownload: true });

// Export as HTML
const htmlArray = superdoc.getHTML();
```

### Editor Control

```javascript
// Focus the editor
superdoc.focus();

// Lock/unlock editing
superdoc.setLocked(true);
superdoc.setLocked(false);

// Toggle rulers
superdoc.toggleRuler();

// High contrast mode
superdoc.setHighContrastMode(true);
```

### Search

```javascript
// Search for text
const results = superdoc.search('keyword');
const regexResults = superdoc.search(/pattern/gi);

// Navigate to result
superdoc.goToSearchResult(results[0]);
```

### Track Changes

```javascript
// Set track changes preferences
superdoc.setTrackedChangesPreferences({
  mode: 'review', // 'review' | 'original' | 'final'
  enabled: true
});
```

### Cleanup

```javascript
// Destroy instance
superdoc.destroy();
```

## Document Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| `editing` | Full editing capabilities | Default editing |
| `viewing` | Read-only presentation | Document preview |
| `suggesting` | Track changes mode | Collaborative review |

## User Roles

| Role | Can Edit | Can Suggest | Can View |
|------|----------|-------------|----------|
| `editor` | Yes | Yes | Yes |
| `suggester` | No | Yes | Yes |
| `viewer` | No | No | Yes |

## Framework Integration

### Vue 3

```vue
<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { SuperDoc } from 'superdoc';
import 'superdoc/style.css';

const containerRef = ref(null);
let superdoc = null;

const props = defineProps({
  document: { type: [File, String, Object], required: true }
});

onMounted(async () => {
  superdoc = new SuperDoc({
    selector: containerRef.value,
    document: props.document,
    documentMode: 'editing'
  });
});

onUnmounted(() => {
  superdoc?.destroy();
});
</script>

<template>
  <div ref="containerRef" style="height: 700px" />
</template>
```

### Svelte

```svelte
<script>
  import { onMount, onDestroy } from 'svelte';
  import { SuperDoc } from 'superdoc';
  import 'superdoc/style.css';

  export let document;

  let container;
  let superdoc;

  onMount(() => {
    superdoc = new SuperDoc({
      selector: container,
      document,
      documentMode: 'editing'
    });
  });

  onDestroy(() => {
    superdoc?.destroy();
  });
</script>

<div bind:this={container} style="height: 700px" />
```

### Vanilla JavaScript (Dynamic Import)

```javascript
async function initEditor(container, document) {
  const { SuperDoc } = await import('superdoc');

  return new SuperDoc({
    selector: container,
    document,
    documentMode: 'editing',
    onReady: ({ superdoc }) => {
      console.log('Ready!');
    }
  });
}
```

## Collaboration Setup

```javascript
import * as Y from 'yjs';
import { WebsocketProvider } from 'y-websocket';
import { SuperDoc } from 'superdoc';

const ydoc = new Y.Doc();
const provider = new WebsocketProvider(
  'wss://your-server.com',
  'document-id',
  ydoc
);

const superdoc = new SuperDoc({
  selector: '#editor',
  document: file,
  modules: {
    collaboration: {
      ydoc,
      provider
    }
  }
});
```

## AI Integration

```javascript
const superdoc = new SuperDoc({
  selector: '#editor',
  document: file,
  modules: {
    ai: {
      apiKey: 'your-api-key',
      endpoint: 'https://api.example.com/ai'
    }
  }
});
```

## Comments Module

```javascript
const superdoc = new SuperDoc({
  selector: '#editor',
  document: file,
  user: { name: 'John', email: 'john@example.com' },
  users: [
    { name: 'Jane', email: 'jane@example.com' },
    { name: 'Bob', email: 'bob@example.com' }
  ],
  modules: {
    comments: {
      enabled: true
    }
  }
});
```

## Error Handling

```javascript
const superdoc = new SuperDoc({
  selector: '#editor',
  document: file,
  onContentError: (event) => {
    console.error('Failed to parse document:', event);
  },
  onException: ({ error }) => {
    console.error('Runtime error:', error);
  }
});
```

## Architecture Notes

SuperDoc has two rendering systems:

| Mode | System | Description |
|------|--------|-------------|
| Editing | super-editor | ProseMirror-based rich text editing |
| Viewing | layout-engine | Virtualized DOM rendering for presentation |

Both systems work together to provide seamless editing and viewing experiences.

## Requirements

| Requirement | Version |
|-------------|---------|
| Node.js | 16+ |
| Modern browser | ES2020 support |

## Examples

| Example | Description |
|---------|-------------|
| [React + TypeScript](https://github.com/superdoc-dev/superdoc/tree/main/examples/getting-started/react) | React wrapper usage |
| [Next.js SSR](https://github.com/superdoc-dev/superdoc/tree/main/examples/integrations/nextjs-ssr) | Next.js App Router |

> For React/Next.js projects, prefer `@superdoc-dev/react` wrapper over core package.

## Links

- [Documentation](https://docs.superdoc.dev)
- [Configuration](https://docs.superdoc.dev/core/superdoc/configuration)
- [Methods](https://docs.superdoc.dev/core/superdoc/methods)
- [GitHub](https://github.com/superdoc-dev/superdoc)
- [npm](https://www.npmjs.com/package/superdoc)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/superdoc-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
