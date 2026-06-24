---
name: react-node-ui-clean-architecture
description: Use this skill when building or refactoring a React application served by Node.js that acts as the human interface layer and calls backend APIs. It is for UI plus BFF style systems where React handles presentation, Node serves assets and optionally hosts HTTP endpoints, and business workflows are isolated from React components, fetch calls, Express request objects, and backend contracts.
metadata:
  author: ecardinalddc
---

# React plus Node.js UI Clean Architecture

Use this skill when you are creating or refactoring a **React application served by Node.js** that is the **human interface layer** for backend APIs.

This skill assumes:

- The browser UI is built with **React**.
- The application is **served by Node.js**.
- The UI layer may include a **Node BFF** that calls backend APIs.
- The system should follow **Clean Architecture**.
- React is the presentation layer, not the place where core workflows and integration policy go.

## What this skill should optimize for

1. Keep **React components** focused on rendering and user interaction.
2. Keep **application workflows** out of components and custom hooks when they start to involve rules, orchestration, or multiple calls.
3. Treat **Node** as an outer delivery layer that serves assets and optionally exposes BFF endpoints.
4. Treat **fetch**, API SDKs, session stores, routers, and React libraries as framework details.
5. Put stable business language in **domain models** and **use cases**.
6. Keep the UI layer resilient to backend contract churn with explicit gateway interfaces and DTO mapping.
7. Make the important logic testable without a browser and without live API calls.

## Architecture mapping

### React UI

React components are **presenters and views**.

Examples:

- `DashboardPage`
- `TicketList`
- `CreateTicketForm`
- `UserMenu`
- `NotificationBanner`

React components should:

- render view models
- collect user input
- invoke use cases or view-model actions
- stay small enough to be understandable
- remain replaceable without rewriting backend workflow rules

React components should not:

- embed fetch logic everywhere
- know backend endpoint shapes in multiple places
- contain cross-step workflow orchestration
- hold authentication or API token policy
- mix transport mapping and rendering logic

### Client application layer

The browser can have an application layer when the UI has meaningful client-side workflows.

Examples:

- `LoadDashboard`
- `SubmitTicket`
- `SearchCustomers`
- `SelectWorkspace`
- `SaveDraft`

Use the client application layer for:

- coordinating UI intent
- shaping calls through gateway interfaces
- transforming raw transport responses into view-friendly results
- keeping workflow logic out of components

Keep it thin if the Node BFF owns most orchestration.

### Node.js server or BFF

Node.js is an **outer delivery mechanism** and often an **inbound adapter** for the browser.

Examples:

- Express routes
- Fastify handlers
- asset serving
- session and cookie handling
- BFF endpoints like `/bff/dashboard` or `/bff/tickets`

Node should:

- terminate browser HTTP concerns
- read cookies, headers, and session state
- call use cases
- map use case results to HTTP responses
- isolate backend contract changes from browser code

Node should not:

- become a dump for random business logic
- leak Express request or response objects into use cases
- let every route talk to backend services directly without ports

### Domain and use cases

For this layer, the domain is often **interaction and workflow domain**, not database domain.

Examples:

- `SupportTicket`
- `TicketSummary`
- `Dashboard`
- `NavigationPolicy`
- `PermissionDecision`
- `PendingAction`

Use cases own the workflow, not React components and not Express routes.

Examples:

- `LoadSupportDashboard`
- `CreateSupportTicket`
- `RefreshSession`
- `GetCaseDetails`

### Outbound adapters

Everything that talks to outside systems belongs at the edge.

Examples:

- `BackendApiClient`
- `SupportGatewayHttpAdapter`
- `SessionStoreAdapter`
- `FeatureFlagAdapter`
- `AnalyticsAdapter`

These adapters implement ports defined by the application layer.

## Strong opinions for React plus Node

### 1. Do not scatter `fetch` through components

Put API calls behind gateways or ports.

Bad:

- `useEffect` in three different components calling the same endpoint
- forms building backend JSON payloads directly
- components parsing backend errors themselves

Better:

- components call a use case or UI action
- the use case depends on a gateway interface
- the infrastructure adapter performs the fetch and transport mapping

### 2. Do not let backend DTOs become your React component model

Map transport DTOs into domain objects or view models.

Your UI should not have to care that an API field is named `cust_id`, `open_ticket_count`, or `state_code`.

### 3. Prefer a Node BFF when auth, aggregation, or contract shaping matters

If Node is already present, it is often the right place for:

- session and cookie handling
- hiding service-to-service tokens from the browser
- combining multiple backend responses into one UI-oriented payload
- stabilizing browser contracts across backend changes

This is an architectural recommendation, not a hard law. For very simple systems, direct browser-to-API calls can be enough.

### 4. Keep routing and framework state at the edge

React Router, TanStack Router, Redux, Zustand, React Query, and similar tools are useful, but they are not the domain model.

Use them to host state and delivery concerns, not to replace use cases and boundaries.

### 5. Keep secrets and privileged calls out of the browser

If a call needs trusted credentials, token exchange, internal topology awareness, or fan-out to multiple services, move it into Node.

## Recommended structure

A good structure for this type of system is:

```text
src/
  client/
    domain/
    application/
      ports/
      usecases/
    infrastructure/
      http/
      router/
    ui/
      pages/
      components/
      hooks/
      view-models/
  server/
    domain/
    application/
      dto/
      ports/
      usecases/
    infrastructure/
      web/
      http/
      session/
      config/
```

You may also use a feature-first structure if boundaries remain obvious inside each feature.

## Dependency rules

When generating or refactoring code, enforce these rules:

1. React components depend on view models, use cases, and UI helpers.
2. Client use cases depend on client ports and domain models.
3. Server routes depend on server use cases and transport mappers.
4. Server use cases depend on domain models and output ports.
5. Output ports live in the application layer.
6. Fetch, Express, cookies, sessions, API SDKs, and browser APIs stay in infrastructure or UI edges.
7. Neither client nor server use cases should depend on Express `Request`, `Response`, or raw React component state.
8. Mapping between backend DTOs, domain objects, and view models should be explicit.

## Input and validation rules

Separate validation into two kinds:

### Transport validation

Belongs at the edge.

Examples:

- malformed JSON
- missing query parameter
- bad form field shape
- invalid route parameter
- unsupported content type

### Business validation

Belongs in domain or use cases.

Examples:

- user cannot create a ticket without selecting a category
- user cannot submit a duplicate request within a cooldown period
- workspace must be active before allowing a specific UI action

## Code generation workflow

When asked to build this architecture:

1. Identify the user-facing workflows.
2. Define the domain concepts the UI works with.
3. Decide which workflows belong in the browser and which belong in the Node BFF.
4. Define input and output ports for backend calls.
5. Create use cases around workflows, not endpoints.
6. Create explicit DTO mappers at the browser and server boundaries.
7. Keep components presentational and small.
8. Keep routes thin and focused on translation.
9. Add unit tests for use cases with fake ports.
10. Add a few boundary tests for the BFF routes and UI rendering.

## What good code looks like

- A component reads like UI, not like a transport client.
- A route reads like an adapter, not like the whole application.
- A backend API change usually impacts one adapter and one mapper, not twenty components.
- Client and server use cases are understandable without React or Express knowledge.
- Important workflows can be tested with fakes and without network access.

## Anti-patterns to avoid

- large page components that fetch, validate, map, orchestrate, and render
- `useEffect` spaghetti coordinating multiple backend calls
- Express routes that call three downstream services directly and manually shape UI data inline
- backend DTOs used as component props across the app
- browser code holding secrets or internal service URLs
- putting every rule into global state management instead of explicit use cases

## Response style for this skill

When helping with this architecture:

- favor clear boundaries over framework cleverness
- generate plain and explicit code
- prefer small adapters and small mappers
- explain which layer a file belongs to
- call out when logic should move from React into a use case or from browser into Node
- optimize for maintainability and testability

## References

- [Architecture mapping](./references/architecture-mapping.md)
- [React boundary rules](./references/react-boundary-rules.md)
- [Node BFF boundary rules](./references/node-bff-boundary-rules.md)
- [Testing strategy](./references/testing-strategy.md)

## Examples

- [Examples overview](./examples/README.md)
- [React plus Node support portal](./examples/react-node-support-portal/README.md)

---
> Source: [ecardinalddc/agent-clean-architecture-agents-and-skills](https://github.com/ecardinalddc/agent-clean-architecture-agents-and-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
