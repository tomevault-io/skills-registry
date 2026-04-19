---
name: ash-architect
description: Expert guidance for designing innovative Ash Framework resources, UI components, and platform architectures. Use when working with Elixir Ash Framework for (1) Designing new Ash resources from scratch, (2) Converting REST APIs or OpenAPI specs to Ash resources, (3) Optimizing existing Ash resources, (4) Creating custom data layers or extensions, (5) Designing UIs and visualizations for resources, (6) Building LiveView/LiveSvelte components, (7) Implementing real-time features, (8) Architecting multi-tenant or RBAC systems, (9) Maximizing Ash usage within platform development Use when this capability is needed.
metadata:
  author: oxmus
---

# Ash Architect

Expert system for designing innovative Ash Framework resources and architectures for the Oxmus platform.

## Core Workflows

### 1. Design New Ash Resource

When creating a new resource from scratch:

1. **Define the domain context** - Determine which domain this resource belongs to
2. **Model the attributes** - Use appropriate types and constraints
3. **Design the actions** - CRUD plus domain-specific operations
4. **Add calculations and aggregates** - Derive data efficiently
5. **Implement authorization** - Policies or checks based on complexity
6. **Create the UI representation** - LiveView or LiveSvelte components

See `references/resource-patterns.md` for detailed patterns and examples.

### 2. Convert API to Ash Resources

For OpenAPI/REST API conversion:

```elixir
# 1. Analyze the OpenAPI spec structure
# 2. Map endpoints to Ash actions
# 3. Convert schemas to attributes
# 4. Generate the resource module

defmodule Platform.ApiResource do
  use Ash.Resource,
    domain: Platform.External,
    data_layer: AshOpenAPI.DataLayer
    
  openapi do
    specification "path/to/openapi.json"
    base_url "https://api.example.com"
  end
  
  actions do
    read :list do
      # Map to GET /items
    end
    
    create :create do
      # Map to POST /items
    end
  end
end
```

Run `scripts/generate_from_openapi.exs` for automated generation.

### 3. Create UI Components

For resource visualization, implement the Smalltalk-inspired browser layout:

```elixir
# Asset Viewer Component Structure
# [Categories | Items List | Details Panel | Actions Panel]

defmodule OxmusWeb.ResourceBrowser do
  use Phoenix.LiveComponent
  
  def render(assigns) do
    ~H"""
    <div class="browser-container grid grid-cols-4">
      <.category_pane resource={@resource} />
      <.list_pane items={@items} />
      <.detail_pane selected={@selected} />
      <.action_pane actions={@actions} />
    </div>
    """
  end
end
```

See `references/ui-patterns.md` for complete UI component patterns.

### 4. Optimize Existing Resources

Performance optimization checklist:

1. **Analyze query patterns** - Use `Ash.Query.load/2` efficiently
2. **Implement read actions with filters** - Push filtering to data layer
3. **Add appropriate indexes** - Based on common query patterns
4. **Use calculations vs aggregates** - Aggregates for counts/sums, calculations for derived fields
5. **Implement caching** - For expensive calculations
6. **Batch load relationships** - Avoid N+1 queries

### 5. Custom Data Layers

When implementing custom data layers:

```elixir
defmodule Platform.CustomDataLayer do
  @behaviour Ash.DataLayer
  
  # Implement required callbacks
  def can?(_, :read), do: true
  def can?(_, :create), do: true
  
  def run_query(query, resource, _parent, opts) do
    # Custom query logic
  end
end
```

See `references/data-layer-patterns.md` for implementation details.

## Resource Design Principles

### Naming Conventions

- **Resources**: Singular, PascalCase (e.g., `User`, `WorkOrder`)
- **Domains**: Contextual grouping (e.g., `Accounts`, `Inventory`)
- **Actions**: Descriptive verbs (e.g., `:complete`, `:approve`, `:archive`)
- **Attributes**: Snake_case (e.g., `:first_name`, `:created_at`)

### Domain Organization

```
lib/platform/
├── accounts/           # User management domain
│   ├── user.ex
│   └── organization.ex
├── inventory/          # Inventory domain
│   ├── item.ex
│   └── warehouse.ex
└── workflows/          # Business process domain
    ├── work_order.ex
    └── approval.ex
```

### Authorization Strategies

**Simple checks** for straightforward rules:
```elixir
authorize_if expr(owner_id == ^actor(:id))
```

**Policies** for complex, reusable logic:
```elixir
policies do
  policy :admin_or_owner do
    authorize_if actor_attribute_equals(:role, :admin)
    authorize_if relates_to_actor_via(:owner)
  end
end
```

## UI Generation Patterns

### LiveView Integration

```elixir
defmodule OxmusWeb.ResourceLive do
  use OxmusWeb, :live_view
  use AshPhoenix.LiveView
  
  @impl true
  def mount(_params, _session, socket) do
    {:ok, 
     socket
     |> assign(:resources, list_resources!())
     |> assign_form()}
  end
end
```

### LiveSvelte Components

```elixir
# In LiveView
def render(assigns) do
  ~H"""
  <.svelte 
    name="ResourceGrid" 
    props={%{resources: @resources}}
    socket={@socket}
  />
  """
end
```

## Real-time Features

Implement Phoenix Channels for resource updates:

```elixir
defmodule Platform.ResourceNotifier do
  use Ash.Notifier
  
  def notify(notification) do
    Phoenix.PubSub.broadcast(
      Oxmus.PubSub,
      "resource:#{notification.resource.id}",
      {:resource_updated, notification}
    )
  end
end
```

## Testing Strategies

```elixir
defmodule Platform.ResourceTest do
  use Platform.DataCase
  
  test "resource lifecycle" do
    # Use Ash.Test helpers
    resource = 
      Resource
      |> Ash.Changeset.for_create(:create, %{name: "Test"})
      |> Ash.create!()
      
    assert resource.name == "Test"
  end
end
```

## Quick Commands

- Generate resource: `mix ash.gen.resource Domain.Resource`
- Add migration: `mix ash.codegen migration_name`
- Install extension: `mix ash.install ash_phoenix`

## References

Load these references as needed for detailed guidance:

- `references/resource-patterns.md` - Common resource design patterns
- `references/ui-patterns.md` - UI component templates and visualization strategies  
- `references/data-layer-patterns.md` - Custom data layer implementations
- `references/optimization-guide.md` - Performance tuning strategies
- `references/llm-integration.md` - Working with LLMs in Ash (from official docs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxmus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
