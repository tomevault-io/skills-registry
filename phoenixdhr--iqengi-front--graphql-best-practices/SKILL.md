---
name: graphql-best-practices
description: This skill outlines the mandatory patterns for implementing GraphQL features in this project to avoid "Invalid hook call" errors and import issues. Use when this capability is needed.
metadata:
  author: phoenixdhr
---
---
name: graphql-best-practices
description: Best practices for using GraphQL and Apollo Client in the project to avoid common errors.
---

# GraphQL & Apollo Client Best Practices

This skill outlines the mandatory patterns for implementing GraphQL features in this project to avoid "Invalid hook call" errors and import issues.

## 1. Wrapping Components with ApolloProvider

**Context:**
In Astro's island architecture, **React components** (`.tsx`) running on the client (hydrated islands) do not share a specific global context unless explicitly provided. This rule **does not apply** to `.astro` files where data fetching happens server-side using the client instance directly.

**Rule:**
When a **React component** uses Apollo hooks (e.g., `useQuery`, `useMutation`), you **MUST** serve it wrapped in an `ApolloProvider` specifically for that island.

**Implementation Pattern:**

Separation of specific logic (Content) and the Provider wrapper.

```tsx
import React, { useState } from 'react';
import { ApolloProvider } from '@apollo/client';
import { clientGql } from '@graphql-astro/apolloClient';
import { useSomeQuery } from '@graphql-astro/generated/graphql';

// 1. Inner component with the logic/hooks
function MyComponentContent() {
    const { data, loading } = useSomeQuery();
    
    if (loading) return <div>Loading...</div>;
    return <div>{data?.someField}</div>;
}

// 2. Exported component wrapped with provider
function MyComponent() {
    return (
        <ApolloProvider client={clientGql}>
            <MyComponentContent />
        </ApolloProvider>
    );
}

export default MyComponent;
```

## 2. Imports in `graphql.ts`

**Rule:**
If you need to use `gql` tag manually or in `graphql.ts` (or similar utility files), you **MUST** import it from `@apollo/client/core`.

**Correct:**
```typescript
import { gql } from '@apollo/client/core';
```

**Incorrect:**
```typescript
import { gql } from '@apollo/client';
import { gql } from 'graphql-tag';
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phoenixdhr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
