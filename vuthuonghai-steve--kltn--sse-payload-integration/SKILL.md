---
name: sse-payload-integration
description: Integrate SSE with Payload CMS for realtime updates. Covers afterChange hooks to trigger events, authenticated routes, and real-time social app features. Use when this capability is needed.
metadata:
  author: vuthuonghai-steve
---

# SSE with Payload CMS

Trigger SSE notifications from CMS hooks for dynamic social feeds.

## Architecture Overview

```
Payload CMS Hook → Event Emission → SSE Stream → Client Browser
   (afterChange)   (EventEmitter/Redis)    (HTTP)    (EventSource)
```

When user creates post, likes, or comments:
1. Payload hook detects change
2. Hook emits notification event
3. SSE route broadcasts to connected clients
4. Browser receives realtime update

## Payload afterChange Hook

Add hook to collection to trigger notification:

```typescript
// collections/Posts.ts
import { CollectionConfig } from 'payload/types';
import { triggerNotification } from '@/app/api/notifications/route';

const Posts: CollectionConfig = {
  slug: 'posts',
  hooks: {
    afterChange: [
      async ({ doc, operation, previousDoc }) => {
        if (operation === 'update') {
          // Check if likes increased
          if (doc.likesCount > (previousDoc?.likesCount || 0)) {
            await triggerNotification(doc.author.id, {
              type: 'like',
              message: `Your post got a like!`,
              postId: doc.id,
            });
          }

          // Check if comments increased
          if (doc.commentsCount > (previousDoc?.commentsCount || 0)) {
            await triggerNotification(doc.author.id, {
              type: 'comment',
              message: `New comment on your post!`,
              postId: doc.id,
            });
          }
        }

        return doc;
      },
    ],
  },
  fields: [
    // Your fields...
  ],
};

export default Posts;
```

## Payload Hook Patterns

### Pattern 1: Notify Document Author

```typescript
hooks: {
  afterChange: [
    async ({ doc }) => {
      await triggerNotification(doc.author.id, {
        type: 'document-update',
        data: { docId: doc.id, title: doc.title },
      });
    },
  ],
}
```

### Pattern 2: Notify All Followers

```typescript
hooks: {
  afterChange: [
    async ({ doc, collection }) => {
      // Get all followers of the author
      const followers = await collection.db.find({
        where: { following: { equals: doc.author.id } },
      });

      // Notify each follower
      for (const follower of followers) {
        await triggerNotification(follower.id, {
          type: 'user-action',
          data: { action: 'new-post', userId: doc.author.id },
        });
      }
    },
  ],
}
```

### Pattern 3: Aggregate Notifications

```typescript
hooks: {
  afterChange: [
    async ({ doc }) => {
      // Debounce notifications (wait 100ms for batch)
      await aggregateNotification(doc.author.id, {
        type: 'batch',
        events: [{ postId: doc.id, action: 'created' }],
      });
    },
  ],
}
```

## SSE Route with Auth

Authenticate users before streaming notifications:

```typescript
// app/api/notifications/route.ts
import { verifyJwt } from '@/lib/auth'; // Your JWT verify function
import { getPayload } from 'payload';

export async function GET(request: NextRequest) {
  // Extract JWT from Authorization header
  const token = request.headers.get('authorization')?.split(' ')[1];
  if (!token) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  // Verify JWT and get user ID
  const decoded = verifyJwt(token);
  if (!decoded) {
    return NextResponse.json({ error: 'Invalid token' }, { status: 401 });
  }

  const userId = decoded.userId;

  // Continue with SSE stream for authenticated user...
}
```

## Full Flow Example

### 1. User Creates Post

```typescript
// Client creates post via Payload API
const response = await fetch('/api/collections/posts', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ title: 'My new post', content: '...' }),
});
```

### 2. Payload Hook Triggers

```typescript
// Payload afterChange hook automatically runs
hooks: {
  afterChange: [
    async ({ doc }) => {
      // Notify followers
      await redis.publish(
        `notifications:${follower.id}`,
        JSON.stringify({
          type: 'new-post',
          post: { id: doc.id, title: doc.title, author: doc.author.name },
        })
      );
    },
  ],
}
```

### 3. SSE Broadcasts to Clients

Connected EventSource clients receive notification in realtime.

### 4. React Component Updates

```typescript
// Client side
es.onmessage = (event) => {
  const notification = JSON.parse(event.data);
  // Update UI
};
```

## Triggers Available in Payload Hooks

| Hook | When | Use For |
|------|------|---------|
| `beforeChange` | Before save (validate, transform) | Validate notification settings |
| `afterChange` | After save (DB persisted) | Trigger notifications, trigger indexing |
| `beforeRead` | Before fetch | Filter private notifications |
| `afterRead` | After fetch | Enrich notification data |

## Error Handling

Handle hook failures gracefully:

```typescript
hooks: {
  afterChange: [
    async ({ doc }) => {
      try {
        await triggerNotification(doc.author.id, { type: 'update' });
      } catch (error) {
        console.error('Failed to trigger notification:', error);
        // Don't throw - let document save succeed
        // Log to monitoring service (Sentry, DataDog)
      }
    },
  ],
}
```

## Performance Considerations

### Async Non-Blocking

Keep hooks fast by offloading work:

```typescript
// ❌ Bad: Waits for notification delivery
await triggerNotification(...);

// ✅ Good: Fire and forget
triggerNotification(...).catch(err => console.error(err));
```

### Batch Notifications

Avoid notifying 1000 followers individually:

```typescript
// ❌ Slow: 1000 publish calls
for (const follower of followers) {
  await redis.publish(`notifications:${follower.id}`, ...);
}

// ✅ Fast: Use Redis pipeline
const pipeline = redis.pipeline();
for (const follower of followers) {
  pipeline.publish(`notifications:${follower.id}`, ...);
}
await pipeline.exec();
```

## AI Prompts

**Prompt**: "Generate a Payload afterChange hook that sends SSE notifications when a post gets liked."

**Prompt**: "Show me how to integrate Payload collection hooks with Redis pub/sub for realtime feeds."

**Prompt**: "Create an authenticated SSE endpoint that works with Payload JWT tokens."

## Related Hooks

- **beforeValidate**: Validate incoming data
- **beforeChange**: Modify document before save
- **afterChange**: Post-save operations (notifications, indexing)
- **afterDelete**: Cleanup when document deleted

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuthuonghai-steve) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
