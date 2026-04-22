---
name: next-js-caching-strategies
description: Comprehensive guide to Next.js 16 caching mechanisms (Request Memoization, Data Cache, Full Route Cache, Router Cache) and APIs. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Caching in Next.js

@doc-version: 16.1.6
@last-updated: 2025-10-22

Next.js improves your application's performance and reduces costs by caching rendering work and data requests. This page provides an in-depth look at Next.js caching mechanisms, the APIs you can use to configure them, and how they interact with each other.

> **Good to know**: This page helps you understand how Next.js works under the hood but is **not** essential knowledge to be productive with Next.js. Most of Next.js' caching heuristics are determined by your API usage and have defaults for the best performance with zero or minimal configuration.

## Overview

Here's a high-level overview of the different caching mechanisms and their purpose:

| Mechanism                                   | What                       | Where  | Purpose                                         | Duration                        |
| ------------------------------------------- | -------------------------- | ------ | ----------------------------------------------- | ------------------------------- |
| [Request Memoization](#request-memoization) | Return values of functions | Server | Re-use data in a React Component tree           | Per-request lifecycle           |
| [Data Cache](#data-cache)                   | Data                       | Server | Store data across user requests and deployments | Persistent (can be revalidated) |
| [Full Route Cache](#full-route-cache)       | HTML and RSC payload       | Server | Reduce rendering cost and improve performance   | Persistent (can be revalidated) |
| [Router Cache](#client-side-router-cache)   | RSC Payload                | Client | Reduce server requests on navigation            | User session or time-based      |

By default, Next.js will cache as much as possible to improve performance and reduce cost. This means routes are **statically rendered** and data requests are **cached** unless you opt out.

Fetch caching is **not** supported in `proxy`. Any fetches done inside of your `proxy` will be uncached.

## Rendering Strategies

To understand how caching works in Next.js, it's helpful to understand the rendering strategies available. The rendering strategy determines when your route's HTML is generated, which directly impacts what can be cached.

### Static Rendering

With Static Rendering, routes are rendered at **build time** or in the background after [data revalidation](/docs/app/guides/incremental-static-regeneration). The result is cached and can be reused across requests. Static routes are fully cached in the [Full Route Cache](#full-route-cache).

### Dynamic Rendering

With Dynamic Rendering, routes are rendered at **request time**. This happens when your route uses request-specific information like cookies, headers, or search params.

A route becomes dynamic when it uses any of these APIs:

* [`cookies`](/docs/app/api-reference/functions/cookies)
* [`headers`](/docs/app/api-reference/functions/headers)
* [`connection`](/docs/app/api-reference/functions/connection)
* [`draftMode`](/docs/app/api-reference/functions/draft-mode)
* [`searchParams` prop](/docs/app/api-reference/file-conventions/page#searchparams-optional)
* [`unstable_noStore`](/docs/app/api-reference/functions/unstable_noStore)
* [`fetch`](/docs/app/api-reference/functions/fetch) with `{ cache: 'no-store' }`

Dynamic routes are not cached in the Full Route Cache, but can still use the [Data Cache](#data-cache) for data requests.

## Request Memoization

Next.js extends the [`fetch` API](#fetch) to automatically **memoize** requests that have the same URL and options. This means you can call a fetch function for the same data in multiple places in a React component tree while only executing it once.

For example, if you need to use the same data across a route (e.g. in a Layout, Page, and multiple components), you do not have to fetch data at the top of the tree, and forward props between components. Instead, you can fetch data in the components that need it without worrying about the performance implications of making multiple requests across the network for the same data.

```tsx filename="app/example.tsx" switcher
async function getItem() {
  // The `fetch` function is automatically memoized and the result
  // is cached
  const res = await fetch('https://.../item/1')
  return res.json()
}

// This function is called twice, but only executed the first time
const item = await getItem() // cache MISS

// The second call could be anywhere in your route
const item = await getItem() // cache HIT
```

### Duration

The cache lasts the lifetime of a server request until the React component tree has finished rendering.

### Revalidating

Since the memoization is not shared across server requests and only applies during rendering, there is no need to revalidate it.

### Opting out

Memoization only applies to the `GET` method in `fetch` requests, other methods, such as `POST` and `DELETE`, are not memoized. This default behavior is a React optimization and we do not recommend opting out of it.

## Data Cache

Next.js has a built-in Data Cache that **persists** the result of data fetches across incoming **server requests** and **deployments**. This is possible because Next.js extends the native `fetch` API to allow each request on the server to set its own persistent caching semantics.

> **Good to know**: In the browser, the `cache` option of `fetch` indicates how a request will interact with the browser's HTTP cache, in Next.js, the `cache` option indicates how a server-side request will interact with the server's Data Cache.

### Duration

The Data Cache is persistent across incoming requests and deployments unless you revalidate or opt-out.

### Revalidating

Cached data can be revalidated in two ways, with:

* **Time-based Revalidation**: Revalidate data after a certain amount of time has passed and a new request is made. This is useful for data that changes infrequently and freshness is not as critical.
* **On-demand Revalidation:** Revalidate data based on an event (e.g. form submission). On-demand revalidation can use a tag-based or path-based approach to revalidate groups of data at once.

#### Time-based Revalidation

To revalidate data at a timed interval, you can use the `next.revalidate` option of `fetch` to set the cache lifetime of a resource (in seconds).

```js
// Revalidate at most every hour
fetch('https://...', { next: { revalidate: 3600 } })
```

#### On-demand Revalidation

Data can be revalidated on-demand by path ([`revalidatePath`](#revalidatepath)) or by cache tag ([`revalidateTag`](#fetch-optionsnexttags-and-revalidatetag)).

### Opting out

If you do *not* want to cache the response from `fetch`, you can do the following:

```js
let data = await fetch('https://api.vercel.app/blog', { cache: 'no-store' })
```

## Full Route Cache

Next.js automatically renders and caches routes at build time. This is an optimization that allows you to serve the cached route instead of rendering on the server for every request, resulting in faster page loads.

### Duration

By default, the Full Route Cache is persistent.

### Invalidation

There are two ways you can invalidate the Full Route Cache:

* **[Revalidating Data](/docs/app/guides/caching#revalidating)**: Revalidating the [Data Cache](#data-cache), will in turn invalidate the Router Cache by re-rendering components on the server and caching the new render output.
* **Redeploying**: Unlike the Data Cache, which persists across deployments, the Full Route Cache is cleared on new deployments.

### Opting out

You can opt out of the Full Route Cache, or in other words, dynamically render components for every incoming request, by:

* **Using a [Dynamic API](#dynamic-apis)**: This will opt the route out from the Full Route Cache and dynamically render it at request time.
* **Using the `dynamic = 'force-dynamic'` or `revalidate = 0` route segment config options**: This will skip the Full Route Cache and the Data Cache.
* **Opting out of the [Data Cache](#data-cache)**: If a route has a `fetch` request that is not cached, this will opt the route out of the Full Route Cache.

## Client-side Router Cache

Next.js has an in-memory client-side router cache that stores the RSC payload of route segments, split by layouts, loading states, and pages.

When a user navigates between routes, Next.js caches the visited route segments and [prefetches](/docs/app/getting-started/linking-and-navigating#prefetching) the routes the user is likely to navigate to. This results in instant back/forward navigation, no full-page reload between navigations, and preservation of browser state and React state in shared layouts.

With the Router Cache:

* **Layouts** are cached and reused on navigation.
* **Loading states** are cached and reused on navigation.
* **Pages** are not cached by default, but are reused during browser backward and forward navigation. You can enable caching for page segments by using the experimental [`staleTimes`](/docs/app/api-reference/config/next-config-js/staleTimes) config option.

### Duration

The cache is stored in the browser's temporary memory. Two factors determine how long the router cache lasts:

* **Session**: The cache persists across navigation. However, it's cleared on page refresh.
* **Automatic Invalidation Period**: The cache of layouts and loading states is automatically invalidated after a specific time.

### Invalidation

There are two ways you can invalidate the Router Cache:

* In a **Server Action**:
  * Revalidating data on-demand by path with ([`revalidatePath`](/docs/app/api-reference/functions/revalidatePath)) or by cache tag with ([`revalidateTag`](/docs/app/api-reference/functions/revalidateTag))
  * Using [`cookies.set`](/docs/app/api-reference/functions/cookies#setting-a-cookie) or [`cookies.delete`](/docs/app/api-reference/functions/cookies#deleting-cookies) invalidates the Router Cache to prevent routes that use cookies from becoming stale.
* Calling [`router.refresh`](/docs/app/api-reference/functions/use-router) will invalidate the Router Cache and make a new request to the server for the current route.

### Opting out

As of Next.js 15, page segments are opted out by default.

## APIs

| API                                                                     | Router Cache               | Full Route Cache      | Data Cache            | React Cache          |
| ----------------------------------------------------------------------- | -------------------------- | --------------------- | --------------------- | -------------------- |
| [`<Link prefetch>`](#link)                                              | Cache                      |                       |                       |                      |
| [`router.prefetch`](#routerprefetch)                                    | Cache                      |                       |                       |                      |
| [`router.refresh`](#routerrefresh)                                      | Revalidate                 |                       |                       |                      |
| [`fetch`](#fetch)                                                       |                            |                       | Cache                 | Cache (GET and HEAD) |
| [`fetch` `options.cache`](#fetch-optionscache)                          |                            |                       | Cache or Opt out      |                      |
| [`fetch` `options.next.revalidate`](#fetch-optionsnextrevalidate)       |                            | Revalidate            | Revalidate            |                      |
| [`fetch` `options.next.tags`](#fetch-optionsnexttags-and-revalidatetag) |                            | Cache                 | Cache                 |                      |
| [`revalidateTag`](#fetch-optionsnexttags-and-revalidatetag)             | Revalidate (Server Action) | Revalidate            | Revalidate            |                      |
| [`revalidatePath`](#revalidatepath)                                     | Revalidate (Server Action) | Revalidate            | Revalidate            |                      |
| [`const revalidate`](#segment-config-options)                           |                            | Revalidate or Opt out | Revalidate or Opt out |                      |
| [`const dynamic`](#segment-config-options)                              |                            | Cache or Opt out      | Cache or Opt out      |                      |
| [`cookies`](#cookies)                                                   | Revalidate (Server Action) | Opt out               |                       |                      |
| [`headers`, `searchParams`](#dynamic-apis)                              |                            | Opt out               |                       |                      |
| [`generateStaticParams`](#generatestaticparams)                         |                            | Cache                 |                       |                      |
| [`React.cache`](#react-cache-function)                                  |                            |                       |                       | Cache                |
| [`unstable_cache`](/docs/app/api-reference/functions/unstable_cache)    |                            |                       | Cache                 |                      |

(See full documentation for detailed API references for `revalidatePath`, `revalidateTag`, `cookies`, `generateStaticParams`, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
