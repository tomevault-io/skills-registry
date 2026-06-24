---
name: modify-graphql
description: GraphQL and Absinthe patterns for the CheddarFlow project. TRIGGER when: writing or modifying GraphQL schemas, types, queries, mutations, subscriptions, resolvers, or Absinthe middleware in the cfx_web app. Also trigger when working with Dataloader, Absinthe.Subscription, or any file under schema/, resolvers/, or types/ directories. DO NOT TRIGGER when: working with non-GraphQL code. Use when this capability is needed.
metadata:
  author: MikaAK
---

# GraphQL / Absinthe Architecture

The API layer uses Absinthe for GraphQL with WebSocket subscriptions via `absinthe_graphql_ws`.

## Schema Organization

The main schema is `CFXWeb.Schema` (`apps/cfx_web/lib/cfx_web/schema.ex`):

```
apps/cfx_web/lib/cfx_web/
├── schema.ex                     # Root schema — imports all types/queries/mutations/subscriptions
├── schema/
│   ├── middleware/                # Absinthe middleware (auth, errors, localization)
│   ├── mutations/                # Mutation field definitions
│   ├── queries/                  # Query field definitions
│   ├── subscriptions/            # Subscription field definitions
│   └── notation.ex               # Shared notation helpers
├── resolvers/                    # Resolver modules (business logic)
├── types/                        # Absinthe type definitions (objects, enums, inputs, scalars)
```

## Naming Conventions

- **Types**: `CFXWeb.Types.<DomainName>` (e.g., `Types.Options`, `Types.Feed`, `Types.User`)
- **Queries**: `CFXWeb.Schema.Queries.<DomainName>` — import via `import_fields :domain_queries`
- **Mutations**: `CFXWeb.Schema.Mutations.<DomainName>` — import via `import_fields :domain_mutations`
- **Subscriptions**: `CFXWeb.Schema.Subscriptions.<DomainName>`
- **Resolvers**: `CFXWeb.Resolvers.<DomainName>`

## Middleware Pipeline

All queries and mutations run through:
1. `Middleware.SessionAdminAuthorization` — checks session/admin permissions
2. (field resolvers)
3. `Middleware.ErrorHandler` — normalizes errors

Subscriptions get `SessionAdminAuthorization` only. Localization fields use `Middleware.Localization`.

## Dataloader

```elixir
def context(ctx) do
  source = Dataloader.Ecto.new(Schemas.Repo)
  dataloader = Dataloader.add_source(Dataloader.new(), Schemas.Accounts, source)
  Map.put(ctx, :loader, dataloader)
end
```

## Subscriptions

Feed data subscriptions:
1. Client subscribes with filter params
2. Resolver routes to feed node via `CFXRpc`
3. Feed server (or ETS state) returns current data
4. PubSub events trigger subscription updates

## Absinthe Context

`%{context: ctx}` contains:
- `:current_user` — authenticated user (from `SessionContextPlug`)
- `:loader` — Dataloader instance
- `:ip_address` — client IP
- Session/admin role info from auth middleware

## Adding New GraphQL Fields

1. Define types in `CFXWeb.Types.<Domain>` using `use Absinthe.Schema.Notation`
2. Define query/mutation fields in `CFXWeb.Schema.Queries.<Domain>` or `Schema.Mutations.<Domain>`
3. Import types and fields in `CFXWeb.Schema`
4. Implement resolver logic in `CFXWeb.Resolvers.<Domain>`
5. For subscriptions, define in `CFXWeb.Schema.Subscriptions.<Domain>` with `config` and `trigger` callbacks

---
> Source: [MikaAK/elixir_rpc_load_balancer](https://github.com/MikaAK/elixir_rpc_load_balancer) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
