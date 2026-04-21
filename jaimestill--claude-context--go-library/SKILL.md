---
name: go-library
description: > Use when this capability is needed.
metadata:
  author: jaimestill
---

# Go Library Development

## When This Skill Applies

- Designing public library APIs
- Implementing extensibility patterns
- Working on pkg/ directories
- Creating reusable packages
- Defining library scope and boundaries

## Principles

### 1. Format Extensibility Pattern

Enable new capabilities without modifying core library code.

```go
// Define format interface in lower-level package
type Format interface {
    Name() string
    Marshal(v any) ([]byte, error)
    Unmarshal(data []byte, v any) error
}

// Registry for formats
var formats = make(map[string]Format)

func RegisterFormat(f Format) {
    formats[f.Name()] = f
}

func GetFormat(name string) (Format, bool) {
    f, ok := formats[name]
    return f, ok
}
```

```go
// Implementations register themselves
func init() {
    RegisterFormat(&JSONFormat{})
    RegisterFormat(&XMLFormat{})
}

// Usage - no core library changes needed for new formats
format, ok := GetFormat("json")
if ok {
    data, _ := format.Marshal(value)
}
```

### 2. Provider-Format Separation

Cleanly separate provider infrastructure from capability formats.

```
Provider Layer: Routes protocols to endpoints
    │
    ▼
Format Layer: Converts requests/responses for specific API
    │
    ▼
Protocol Layer: Defines protocol types and contracts
```

```go
// Provider: handles connection, auth, routing
type Provider struct {
    endpoint   string
    httpClient *http.Client
    auth       Authenticator
}

func (p *Provider) Send(req Request) (Response, error) {
    // Handles HTTP mechanics, auth, retries
}

// Format: handles protocol-specific marshaling
type OpenAIFormat struct{}

func (f *OpenAIFormat) FormatRequest(msg Message) Request {
    // Converts to OpenAI-specific format
}

func (f *OpenAIFormat) ParseResponse(resp Response) Message {
    // Parses OpenAI-specific response
}
```

**Key Insight**: Providers don't know about format details; formats don't know about provider details.

### 3. Capability Composition

Models compose capabilities from registered formats via configuration.

```go
type Model struct {
    provider     Provider
    capabilities map[string]Capability
}

type Capability interface {
    Name() string
    Execute(ctx context.Context, input any) (any, error)
}

// Configuration specifies capabilities
type ModelConfig struct {
    Provider     string
    Capabilities []string  // e.g., ["chat", "embed", "vision"]
}

func NewModel(cfg ModelConfig) (*Model, error) {
    m := &Model{
        provider:     getProvider(cfg.Provider),
        capabilities: make(map[string]Capability),
    }

    for _, name := range cfg.Capabilities {
        cap, ok := getCapability(name)
        if !ok {
            return nil, fmt.Errorf("unknown capability: %s", name)
        }
        m.capabilities[name] = cap
    }

    return m, nil
}
```

### 4. Library Scope Definition

Clearly document what the library provides and intentionally excludes.

```go
// Package agent provides LLM integration primitives.
//
// Scope:
//   - Protocol abstractions for LLM communication
//   - Provider integration (Ollama, OpenAI, Azure)
//   - HTTP transport and streaming
//
// Out of Scope (use supplemental packages):
//   - Tool execution and function calling
//   - Context management and memory
//   - Multi-agent orchestration
//   - Workflow composition
package agent
```

### 5. Supplemental Package Pattern

Guidelines for creating supplemental packages extending core libraries.

```
Core Library (v1.0.0)
├── Provides: Primitives, interfaces, basic implementations
└── Stable API, minimal dependencies

Supplemental Package (v0.x.x → v1.0.0)
├── Extends: Higher-level abstractions
├── Depends on: Core library interfaces
└── Pre-release versioning during validation
```

```go
// Core: go-agents/pkg/agent
type Agent interface {
    Execute(ctx context.Context, input string) (string, error)
}

// Supplemental: go-agents-orchestration/pkg/workflow
type Workflow struct {
    agents []agent.Agent  // Uses core interface
}

func (w *Workflow) Run(ctx context.Context, input string) (string, error) {
    // Orchestrates multiple agents
}
```

**Versioning Strategy**:
- Start at v0.1.0 during development
- Iterate through v0.x.x for API refinement
- Graduate to v1.0.0 after API validation

## Patterns

### Functional Options

```go
type Option func(*Client)

func WithTimeout(d time.Duration) Option {
    return func(c *Client) {
        c.timeout = d
    }
}

func WithRetries(n int) Option {
    return func(c *Client) {
        c.retries = n
    }
}

func NewClient(endpoint string, opts ...Option) *Client {
    c := &Client{
        endpoint: endpoint,
        timeout:  30 * time.Second,  // defaults
        retries:  3,
    }
    for _, opt := range opts {
        opt(c)
    }
    return c
}

// Usage
client := NewClient("https://api.example.com",
    WithTimeout(10*time.Second),
    WithRetries(5),
)
```

### Interface Segregation

```go
// Bad: Large interface
type Repository interface {
    Create(ctx context.Context, e Entity) error
    Read(ctx context.Context, id string) (Entity, error)
    Update(ctx context.Context, e Entity) error
    Delete(ctx context.Context, id string) error
    List(ctx context.Context) ([]Entity, error)
    Search(ctx context.Context, query string) ([]Entity, error)
    Export(ctx context.Context, format string) ([]byte, error)
}

// Good: Segregated interfaces
type Reader interface {
    Read(ctx context.Context, id string) (Entity, error)
}

type Writer interface {
    Create(ctx context.Context, e Entity) error
    Update(ctx context.Context, e Entity) error
    Delete(ctx context.Context, id string) error
}

type Searcher interface {
    Search(ctx context.Context, query string) ([]Entity, error)
}

// Compose as needed
type ReadWriter interface {
    Reader
    Writer
}
```

### Constructor Validation

```go
func NewService(cfg Config) (*Service, error) {
    // Validate required fields
    if cfg.Endpoint == "" {
        return nil, errors.New("endpoint required")
    }

    // Apply defaults
    if cfg.Timeout == 0 {
        cfg.Timeout = 30 * time.Second
    }

    // Validate constraints
    if cfg.MaxRetries < 0 {
        return nil, errors.New("max retries must be non-negative")
    }

    return &Service{
        endpoint: cfg.Endpoint,
        timeout:  cfg.Timeout,
        retries:  cfg.MaxRetries,
    }, nil
}
```

## Anti-Patterns

### Leaking Implementation Types

```go
// Bad: Returns concrete type
func NewParser() *JSONParser {
    return &JSONParser{}
}

// Good: Returns interface
func NewParser() Parser {
    return &jsonParser{}
}
```

### Breaking Semver

```go
// v1.0.0
func Process(input string) string

// v1.1.0 - WRONG: Breaking change in minor version
func Process(input string, opts Options) string

// v1.1.0 - CORRECT: Backward compatible
func Process(input string) string  // Original preserved
func ProcessWithOptions(input string, opts Options) string
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaimestill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
