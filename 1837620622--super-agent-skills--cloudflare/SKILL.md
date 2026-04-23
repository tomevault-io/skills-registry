---
name: cloudflare
description: 在 Cloudflare 边缘平台上构建和部署应用。适用于创建 Workers、Pages、D1 数据库、R2 存储、AI 推理或 KV 存储。触发关键词：Cloudflare, Workers, Cloudflare Pages, D1, R2, KV, Cloudflare AI, Durable Objects, edge computing, 边缘计算。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# Cloudflare 平台

在 Cloudflare 全球边缘网络上使用 Workers、Pages 和存储解决方案构建应用。

## 快速开始

```bash
# 安装 Wrangler CLI
npm install -g wrangler

# 登录 Cloudflare
wrangler login

# 创建新的 Worker 项目
npm create cloudflare@latest my-worker

# 本地开发
wrangler dev

# 部署
wrangler deploy
```

## Workers

### 基础 Worker
```typescript
// src/index.ts
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url);
    
    if (url.pathname === '/api/hello') {
      return Response.json({ message: 'Hello from the edge!' });
    }
    
    return new Response('Not Found', { status: 404 });
  },
};
```

### wrangler.toml 配置
```toml
name = "my-worker"
main = "src/index.ts"
compatibility_date = "2025-01-01"

[vars]
ENVIRONMENT = "production"

# KV 命名空间
[[kv_namespaces]]
binding = "MY_KV"
id = "abc123"

# D1 Database
[[d1_databases]]
binding = "DB"
database_name = "my-database"
database_id = "def456"

# R2 存储桶
[[r2_buckets]]
binding = "BUCKET"
bucket_name = "my-bucket"

# AI
[ai]
binding = "AI"

# Durable Objects
[[durable_objects.bindings]]
name = "COUNTER"
class_name = "Counter"

[[migrations]]
tag = "v1"
new_classes = ["Counter"]
```

### wrangler.jsonc 配置（替代格式）
```jsonc
{
  "$schema": "./node_modules/wrangler/config-schema.json",
  "name": "my-worker",
  "main": "src/index.ts",
  "compatibility_date": "2025-01-01",
  "vars": { "ENVIRONMENT": "production" },
  "kv_namespaces": [
    { "binding": "MY_KV", "id": "abc123", "preview_id": "preview123" }
  ],
  "d1_databases": [
    { "binding": "DB", "database_name": "my-database", "database_id": "def456" }
  ],
  "r2_buckets": [
    { "binding": "BUCKET", "bucket_name": "my-bucket" }
  ],
  "ai": { "binding": "AI" },
  "vectorize": [
    { "binding": "VECTOR_INDEX", "index_name": "my-index" }
  ],
  "durable_objects": {
    "bindings": [
      { "name": "COUNTER", "class_name": "Counter" }
    ]
  }
}
```

### 请求路由
```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    const { pathname } = url;
    
    // 路由模式
    const routes: Record<string, () => Promise<Response>> = {
      '/api/users': () => handleUsers(request, env),
      '/api/posts': () => handlePosts(request, env),
    };
    
    const handler = routes[pathname];
    if (handler) {
      return handler();
    }
    
    // 通配符匹配
    if (pathname.startsWith('/api/users/')) {
      const userId = pathname.split('/')[3];
      return handleUser(userId, request, env);
    }
    
    return new Response('Not Found', { status: 404 });
  },
};
```

## KV 存储

```typescript
interface Env {
  MY_KV: KVNamespace;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    // 设置值
    await env.MY_KV.put('key', 'value', {
      expirationTtl: 3600, // 1 hour
      metadata: { created: Date.now() },
    });
    
    // 获取值
    const value = await env.MY_KV.get('key');
    
    // 获取值和元数据
    const { value: data, metadata } = await env.MY_KV.getWithMetadata('key');
    
    // 列出键
    const list = await env.MY_KV.list({ prefix: 'user:' });
    
    // 删除
    await env.MY_KV.delete('key');
    
    return Response.json({ value });
  },
};
```

## D1 数据库（SQLite）

```typescript
interface Env {
  DB: D1Database;
}

// 创建表（通过 wrangler d1 execute 运行一次）
// wrangler d1 execute my-database --file=./schema.sql

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // 查询
    const { results } = await env.DB.prepare(
      'SELECT * FROM users WHERE id = ?'
    ).bind(1).all();
    
    // 插入
    const { meta } = await env.DB.prepare(
      'INSERT INTO users (name, email) VALUES (?, ?)'
    ).bind('Alice', 'alice@example.com').run();
    
    // 批量操作
    const batch = await env.DB.batch([
      env.DB.prepare('INSERT INTO logs (action) VALUES (?)').bind('login'),
      env.DB.prepare('UPDATE users SET last_login = ? WHERE id = ?').bind(Date.now(), 1),
    ]);
    
    // 仅获取第一条结果
    const user = await env.DB.prepare(
      'SELECT * FROM users WHERE email = ?'
    ).bind('alice@example.com').first();
    
    return Response.json({ results, insertId: meta.last_row_id });
  },
};
```

### Schema 示例
```sql
-- schema.sql
CREATE TABLE IF NOT EXISTS users (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  email TEXT UNIQUE NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE IF NOT EXISTS posts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id INTEGER NOT NULL,
  title TEXT NOT NULL,
  content TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## R2 对象存储

```typescript
interface Env {
  BUCKET: R2Bucket;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    const key = url.pathname.slice(1);
    
    switch (request.method) {
      case 'PUT': {
        // 上传文件
        const body = await request.arrayBuffer();
        await env.BUCKET.put(key, body, {
          httpMetadata: {
            contentType: request.headers.get('content-type') || 'application/octet-stream',
          },
          customMetadata: {
            uploadedBy: 'api',
          },
        });
        return new Response('Uploaded', { status: 201 });
      }
      
      case 'GET': {
        // 下载文件
        const object = await env.BUCKET.get(key);
        if (!object) {
          return new Response('Not Found', { status: 404 });
        }
        
        const headers = new Headers();
        object.writeHttpMetadata(headers);
        headers.set('etag', object.httpEtag);
        
        return new Response(object.body, { headers });
      }
      
      case 'DELETE': {
        await env.BUCKET.delete(key);
        return new Response('Deleted');
      }
      
      default:
        return new Response('Method Not Allowed', { status: 405 });
    }
  },
};

// 列出对象
async function listObjects(env: Env, prefix?: string) {
  const listed = await env.BUCKET.list({
    prefix,
    limit: 100,
  });
  return listed.objects.map(obj => ({
    key: obj.key,
    size: obj.size,
    uploaded: obj.uploaded,
  }));
}
```

## Cloudflare AI 人工智能

```typescript
interface Env {
  AI: Ai;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const { prompt } = await request.json();
    
    // 文本生成（Llama 3.1, Mistral, Qwen 等）
    const response = await env.AI.run('@cf/meta/llama-3.1-8b-instruct', {
      messages: [
        { role: 'system', content: 'You are a helpful assistant.' },
        { role: 'user', content: prompt },
      ],
      max_tokens: 1024,
    });
    
    return Response.json(response);
  },
};

// 图像生成
async function generateImage(env: Env, prompt: string) {
  const response = await env.AI.run('@cf/stabilityai/stable-diffusion-xl-base-1.0', {
    prompt,
    num_steps: 20,
  });
  
  return new Response(response, {
    headers: { 'content-type': 'image/png' },
  });
}

// 文本嵌入
async function getEmbeddings(env: Env, text: string) {
  const response = await env.AI.run('@cf/baai/bge-base-en-v1.5', {
    text: [text],
  });
  return response.data[0]; // Float32Array
}

// 图像分类
async function classifyImage(env: Env, imageData: ArrayBuffer) {
  const response = await env.AI.run('@cf/microsoft/resnet-50', {
    image: [...new Uint8Array(imageData)],
  });
  return response;
}

// 语音转文字
async function transcribe(env: Env, audioData: ArrayBuffer) {
  const response = await env.AI.run('@cf/openai/whisper', {
    audio: [...new Uint8Array(audioData)],
  });
  return response.text;
}
```

## 持久对象（Durable Objects）

```typescript
// 持久对象类
export class Counter {
  state: DurableObjectState;
  
  constructor(state: DurableObjectState) {
    this.state = state;
  }
  
  async fetch(request: Request): Promise<Response> {
    const url = new URL(request.url);
    
    let value = (await this.state.storage.get<number>('count')) || 0;
    
    switch (url.pathname) {
      case '/increment':
        value++;
        await this.state.storage.put('count', value);
        break;
      case '/decrement':
        value--;
        await this.state.storage.put('count', value);
        break;
    }
    
    return Response.json({ count: value });
  }
}

// 使用持久对象的 Worker
interface Env {
  COUNTER: DurableObjectNamespace;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // 获取计数器的唯一 ID（例如，每个用户）
    const counterId = env.COUNTER.idFromName('global-counter');
    const counter = env.COUNTER.get(counterId);
    
    // 将请求转发到持久对象
    return counter.fetch(request);
  },
};
```

## Cloudflare Pages

### pages.toml（函数配置）
```toml
[build]
command = "npm run build"
output_directory = "dist"

[[redirects]]
from = "/old-page"
to = "/new-page"
status = 301

[[headers]]
for = "/api/*"
[headers.values]
Access-Control-Allow-Origin = "*"
```

### Pages 函数
```typescript
// functions/api/hello.ts
export const onRequestGet: PagesFunction = async (context) => {
  return Response.json({ message: 'Hello!' });
};

export const onRequestPost: PagesFunction<Env> = async (context) => {
  const body = await context.request.json();
  
  // 访问绑定
  await context.env.KV.put('key', JSON.stringify(body));
  
  return Response.json({ success: true });
};

// functions/api/users/[id].ts
export const onRequestGet: PagesFunction = async (context) => {
  const userId = context.params.id;
  return Response.json({ userId });
};

// 中间件: functions/_middleware.ts
export const onRequest: PagesFunction = async (context) => {
  // 认证检查
  const auth = context.request.headers.get('Authorization');
  if (!auth) {
    return new Response('Unauthorized', { status: 401 });
  }
  
  // 继续下一个处理器
  return context.next();
};
```

## 队列

```typescript
// 生产者
interface Env {
  MY_QUEUE: Queue;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // 发送消息到队列
    await env.MY_QUEUE.send({
      type: 'email',
      to: 'user@example.com',
      subject: 'Welcome!',
    });
    
    // 批量发送
    await env.MY_QUEUE.sendBatch([
      { body: { task: 'process', id: 1 } },
      { body: { task: 'process', id: 2 } },
    ]);
    
    return Response.json({ queued: true });
  },
};

// 消费者
export default {
  async queue(batch: MessageBatch<any>, env: Env): Promise<void> {
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

## 定时任务

```toml
# wrangler.toml
[triggers]
crons = ["0 0 * * *", "*/15 * * * *"]  # 每天午夜、每15分钟
```

```typescript
export default {
  async scheduled(event: ScheduledEvent, env: Env, ctx: ExecutionContext): Promise<void> {
    switch (event.cron) {
      case '0 0 * * *':
        await dailyCleanup(env);
        break;
      case '*/15 * * * *':
        await checkHealthStatus(env);
        break;
    }
  },
};
```

## WebSockets

```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const upgradeHeader = request.headers.get('Upgrade');
    
    if (upgradeHeader === 'websocket') {
      const [client, server] = Object.values(new WebSocketPair());
      
      server.accept();
      server.addEventListener('message', (event) => {
        server.send(`Echo: ${event.data}`);
      });
      
      return new Response(null, {
        status: 101,
        webSocket: client,
      });
    }
    
    return new Response('Expected WebSocket', { status: 400 });
  },
};
```

## RAG（检索增强生成）与 Vectorize

```typescript
import { Hono } from "hono";

interface Env {
  AI: Ai;
  DB: D1Database;
  VECTOR_INDEX: VectorizeIndex;
}

const app = new Hono<{ Bindings: Env }>();

// 添加笔记并生成向量嵌入
app.post('/notes', async (c) => {
  const { text } = await c.req.json();
  const id = crypto.randomUUID();
  
  // 生成文本嵌入向量
  const embeddings = await c.env.AI.run('@cf/baai/bge-base-en-v1.5', {
    text: text,
  });
  
  // 存储到 D1 数据库
  await c.env.DB.prepare('INSERT INTO notes (id, text) VALUES (?, ?)')
    .bind(id, text)
    .run();
  
  // 存储向量到 Vectorize
  await c.env.VECTOR_INDEX.upsert([{
    id: id,
    values: embeddings.data[0],
  }]);
  
  return c.json({ id, success: true });
});

// RAG 查询：向量搜索 + AI 生成
app.get('/', async (c) => {
  const question = c.req.query('text') || '默认问题';
  
  // 1. 将问题转换为向量
  const embeddings = await c.env.AI.run('@cf/baai/bge-base-en-v1.5', {
    text: question,
  });
  
  // 2. 在 Vectorize 中搜索最相似的向量
  const vectorQuery = await c.env.VECTOR_INDEX.query(embeddings.data[0], { 
    topK: 3 
  });
  
  // 3. 从 D1 获取相关笔记
  let notes: string[] = [];
  if (vectorQuery.matches?.length > 0) {
    const ids = vectorQuery.matches.map(m => m.id);
    const placeholders = ids.map(() => '?').join(',');
    const { results } = await c.env.DB
      .prepare(`SELECT text FROM notes WHERE id IN (${placeholders})`)
      .bind(...ids)
      .all();
    notes = results?.map((r: any) => r.text) || [];
  }
  
  // 4. 使用上下文生成 AI 回答
  const contextMessage = notes.length
    ? `上下文:\n${notes.map(note => `- ${note}`).join('\n')}`
    : '';
  
  const { response } = await c.env.AI.run('@cf/meta/llama-3-8b-instruct', {
    messages: [
      ...(notes.length ? [{ role: 'system', content: contextMessage }] : []),
      { role: 'system', content: '根据提供的上下文回答问题。' },
      { role: 'user', content: question },
    ],
  });
  
  return c.text(response);
});

export default app;
```

### wrangler.toml 配置（RAG）
```toml
name = "rag-worker"
main = "src/index.ts"
compatibility_date = "2025-01-01"

[[d1_databases]]
binding = "DB"
database_name = "notes-db"
database_id = "xxx"

[[vectorize]]
binding = "VECTOR_INDEX"
index_name = "notes-index"

[ai]
binding = "AI"
```

## Workflows（工作流）

用于持久执行、异步任务和需要人工介入的场景：

```typescript
import { WorkflowEntrypoint, WorkflowStep, WorkflowEvent } from 'cloudflare:workers';

type Env = {
  MY_WORKFLOW: Workflow;
};

type Params = {
  email: string;
  metadata: Record<string, string>;
};

export class MyWorkflow extends WorkflowEntrypoint<Env, Params> {
  async run(event: WorkflowEvent<Params>, step: WorkflowStep) {
    // 步骤 1: 获取数据
    const data = await step.do('fetch-data', async () => {
      const resp = await fetch('https://api.example.com/data');
      return await resp.json();
    });
    
    // 步骤 2: 等待一段时间
    await step.sleep('wait-period', '5 minutes');
    
    // 步骤 3: 带重试的操作
    await step.do('api-call-with-retry', {
      retries: {
        limit: 5,
        delay: '5 second',
        backoff: 'exponential',
      },
      timeout: '15 minutes',
    }, async () => {
      // 可能失败的操作
      await sendNotification(event.payload.email);
    });
    
    return { success: true };
  }
}

// 触发工作流
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const instance = await env.MY_WORKFLOW.create({
      params: { email: 'user@example.com', metadata: {} },
    });
    return Response.json({ workflowId: instance.id });
  },
};
```

## Hyperdrive（数据库加速）

连接现有 PostgreSQL 数据库并加速查询：

```typescript
interface Env {
  HYPERDRIVE: Hyperdrive;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // 获取连接字符串
    const connectionString = env.HYPERDRIVE.connectionString;
    
    // 使用任何 PostgreSQL 客户端
    // 连接会自动池化和加速
    const client = new Client({ connectionString });
    await client.connect();
    
    const result = await client.query('SELECT * FROM users LIMIT 10');
    await client.end();
    
    return Response.json(result.rows);
  },
};
```

```toml
# wrangler.toml
[[hyperdrive]]
binding = "HYPERDRIVE"
id = "your-hyperdrive-id"
```

## Browser Rendering（浏览器渲染）

使用 Puppeteer 进行无头浏览器操作：

```typescript
import puppeteer from "@cloudflare/puppeteer";

interface Env {
  MYBROWSER: Fetcher;
}

export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url).searchParams.get("url");
    if (!url) return new Response("Missing url param", { status: 400 });
    
    const browser = await puppeteer.launch(env.MYBROWSER);
    const page = await browser.newPage();
    await page.goto(url);
    
    // 获取页面内容
    const content = await page.content();
    
    // 提取文本
    const text = await page.$eval("body", (el) => el.textContent);
    
    // 截图
    const screenshot = await page.screenshot({ type: 'png' });
    
    await browser.close();
    
    return new Response(screenshot, {
      headers: { 'content-type': 'image/png' },
    });
  },
};
```

## OpenAI 兼容接口

Workers AI 提供 OpenAI 兼容的端点：

```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: env.CLOUDFLARE_API_TOKEN,
  baseURL: `https://api.cloudflare.com/client/v4/accounts/${env.ACCOUNT_ID}/ai/v1`,
});

const response = await openai.chat.completions.create({
  model: '@cf/meta/llama-3.1-8b-instruct',
  messages: [
    { role: 'system', content: 'You are a helpful assistant' },
    { role: 'user', content: 'Hello!' },
  ],
  stream: true,
});
```

## 相关资源

- **Workers 文档**: https://developers.cloudflare.com/workers/
- **D1 文档**: https://developers.cloudflare.com/d1/
- **R2 文档**: https://developers.cloudflare.com/r2/
- **Pages 文档**: https://developers.cloudflare.com/pages/
- **AI 文档**: https://developers.cloudflare.com/workers-ai/
- **Vectorize 文档**: https://developers.cloudflare.com/vectorize/
- **Wrangler CLI**: https://developers.cloudflare.com/workers/wrangler/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
