---
name: cloudflare-skills
description: Cloudflare development with Workers, KV, R2, D1, Vectorize, Queues, Workflows, and Wrangler CLI. Use when building on Cloudflare's edge platform. Use when this capability is needed.
metadata:
  author: allanninal
---

# Cloudflare Development Skills

## When to Use This Skill

- Building Cloudflare Workers
- Using Wrangler CLI for deployments
- Working with KV, R2, D1, Vectorize, Queues
- Implementing edge-first architectures
- Building AI agents on Cloudflare
- Setting up Durable Objects for stateful coordination

## Wrangler CLI Essentials

### Project Setup

```bash
# Create new project
npx wrangler init my-worker

# Development
npx wrangler dev

# Deploy
npx wrangler deploy

# Tail logs
npx wrangler tail

# Secrets management
npx wrangler secret put API_KEY
npx wrangler secret list
```

### wrangler.toml Configuration

```toml
name = "my-worker"
main = "src/index.ts"
compatibility_date = "2024-01-01"

[vars]
ENVIRONMENT = "production"

[[kv_namespaces]]
binding = "MY_KV"
id = "abc123"

[[r2_buckets]]
binding = "MY_BUCKET"
bucket_name = "my-bucket"

[[d1_databases]]
binding = "DB"
database_name = "my-database"
database_id = "xyz789"

[[queues.producers]]
binding = "MY_QUEUE"
queue = "my-queue"

[ai]
binding = "AI"
```

## Workers Patterns

### Basic Worker

```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);

    if (url.pathname === '/api/data') {
      return handleApi(request, env);
    }

    return new Response('Not Found', { status: 404 });
  },
};

async function handleApi(request: Request, env: Env): Promise<Response> {
  if (request.method !== 'POST') {
    return new Response('Method not allowed', { status: 405 });
  }

  const data = await request.json();

  // Process and return
  return Response.json({ success: true, data });
}
```

### KV Storage

```typescript
// Write
await env.MY_KV.put('key', 'value', {
  expirationTtl: 3600, // 1 hour
  metadata: { version: 1 },
});

// Read
const value = await env.MY_KV.get('key');
const { value, metadata } = await env.MY_KV.getWithMetadata('key');

// List
const list = await env.MY_KV.list({ prefix: 'user:' });

// Delete
await env.MY_KV.delete('key');
```

### R2 Object Storage

```typescript
// Upload
await env.MY_BUCKET.put('file.pdf', fileData, {
  httpMetadata: {
    contentType: 'application/pdf',
  },
  customMetadata: {
    uploadedBy: 'user123',
  },
});

// Download
const object = await env.MY_BUCKET.get('file.pdf');
if (object) {
  return new Response(object.body, {
    headers: {
      'Content-Type': object.httpMetadata?.contentType || 'application/octet-stream',
    },
  });
}

// List objects
const objects = await env.MY_BUCKET.list({ prefix: 'uploads/' });
```

### D1 Database (SQLite)

```typescript
// Query
const { results } = await env.DB.prepare(
  'SELECT * FROM users WHERE id = ?'
).bind(userId).all();

// Insert
const result = await env.DB.prepare(
  'INSERT INTO users (name, email) VALUES (?, ?)'
).bind(name, email).run();

// Batch operations
const batch = await env.DB.batch([
  env.DB.prepare('INSERT INTO logs (msg) VALUES (?)').bind('log1'),
  env.DB.prepare('INSERT INTO logs (msg) VALUES (?)').bind('log2'),
]);
```

### Durable Objects (Stateful)

```typescript
// durable-object.ts
export class Counter {
  private state: DurableObjectState;
  private count: number = 0;

  constructor(state: DurableObjectState) {
    this.state = state;
    this.state.blockConcurrencyWhile(async () => {
      this.count = (await this.state.storage.get('count')) || 0;
    });
  }

  async fetch(request: Request): Promise<Response> {
    this.count++;
    await this.state.storage.put('count', this.count);
    return Response.json({ count: this.count });
  }
}

// Worker using Durable Object
const id = env.COUNTER.idFromName('global');
const stub = env.COUNTER.get(id);
return stub.fetch(request);
```

### Queues

```typescript
// Producer
await env.MY_QUEUE.send({
  type: 'process',
  data: { userId: '123' },
});

// Batch send
await env.MY_QUEUE.sendBatch([
  { body: { id: 1 } },
  { body: { id: 2 } },
]);

// Consumer
export default {
  async queue(batch: MessageBatch, env: Env): Promise<void> {
    for (const message of batch.messages) {
      try {
        await processMessage(message.body);
        message.ack();
      } catch (error) {
        message.retry();
      }
    }
  },
};
```

### Workers AI

```typescript
// Text generation
const response = await env.AI.run('@cf/meta/llama-3-8b-instruct', {
  messages: [
    { role: 'system', content: 'You are a helpful assistant.' },
    { role: 'user', content: 'Hello!' },
  ],
});

// Embeddings
const embeddings = await env.AI.run('@cf/baai/bge-base-en-v1.5', {
  text: ['Hello world', 'How are you?'],
});

// Image generation
const image = await env.AI.run('@cf/stabilityai/stable-diffusion-xl-base-1.0', {
  prompt: 'A beautiful sunset',
});
```

### Vectorize

```typescript
// Insert vectors
await env.VECTORIZE.insert([
  { id: 'doc1', values: embedding1, metadata: { title: 'Doc 1' } },
  { id: 'doc2', values: embedding2, metadata: { title: 'Doc 2' } },
]);

// Query similar vectors
const results = await env.VECTORIZE.query(queryEmbedding, {
  topK: 5,
  filter: { category: 'tech' },
  returnMetadata: true,
});
```

## Web Performance (Core Web Vitals)

```typescript
// Cache API
const cache = caches.default;
const cacheKey = new Request(url, request);

let response = await cache.match(cacheKey);
if (!response) {
  response = await fetch(request);
  response = new Response(response.body, response);
  response.headers.set('Cache-Control', 'public, max-age=3600');
  ctx.waitUntil(cache.put(cacheKey, response.clone()));
}
return response;
```

## Security Best Practices

- [ ] Use secrets for sensitive config
- [ ] Validate all inputs
- [ ] Implement rate limiting
- [ ] Use Turnstile for bot protection
- [ ] Enable Access for authentication
- [ ] Set appropriate CORS headers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
