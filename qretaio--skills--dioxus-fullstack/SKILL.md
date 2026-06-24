---
name: dioxus-fullstack
description: Comprehensive Dioxus 0.7 framework expertise for building modern fullstack applications with Rust. Use when working with Dioxus for web, desktop, or mobile app development, setting up projects, implementing state management, routing, data fetching, CSS-in-Rust styling, or integrating with backend APIs and databases. Use when this capability is needed.
metadata:
  author: qretaio
---

# Dioxus 0.7 Fullstack Development

Comprehensive Dioxus 0.7 framework expertise for building modern fullstack applications with Rust. Covers desktop, web, and mobile app development with seamless Rust integration.

## Core Dioxus 0.7 Concepts

### Essential References

- **Official Docs**: https://dioxuslabs.com/docs/0.7/dioxus/
- **Fullstack Guide**: https://dioxuslabs.com/docs/0.7/fullstack/
- **CLI Reference**: https://dioxuslabs.com/docs/0.7/cli/
- **Awesome Dioxus**: https://dioxuslabs.com/awesome/

### Project Structure

```
src/
├── main.rs           # Main application entry
├── app.rs            # Root app component
├── components/       # Reusable UI components
├── pages/            # Route/page components
├── hooks/            # Custom hooks
├── server/           # Server-only code (fullstack)
├── assets/           # Static assets
└── routes/           # Route definitions (new in 0.7)
```

### Key Dependencies (Dioxus 0.7)

```bash
# Use dx add to install dependencies:
dx add dioxus --features fullstack
dx add dioxus-router
dx add dioxus-desktop
dx add dioxus-mobile
dx add serde --features derive
dx add reqwest
dx add tokio --features full
dx add sqlx --features "runtime-tokio-rustls,postgres"
dx add dioxus-logger  # New logging system
```

## Development Patterns

### Component Architecture

- Use functional components with hooks (`use_signal`, `use_effect`)
- Implement proper prop typing with Rust structs
- Leverage `children` for composition
- Use `cx.render` for conditional rendering
- New: `fn Component(cx: Scope) -> Element` syntax

### State Management (0.7 Updates)

- Local state: `use_signal` hook (preferred over `use_state`)
- Global state: Copy/Clone signals or context providers
- Server state: Use server functions with new `#[server]` macro
- Signals are now Copy and Clone by default

### Routing (0.7 Rewrite)

```rust
use dioxus_router::prelude::*;

#[derive(Clone, Routable, Debug, PartialEq)]
enum Route {
    #[route("/")]
    Home {},
    #[route("/blog/:id")]
    BlogPost { id: u32 },
    #[route("/about")]
    About {},
    #[nest("/admin")]
        #[route("/dashboard")]
        AdminDashboard {},
    #[end_nest]
    #[route("/:..route")]
    NotFound { route: Vec<String> },
}

fn App(cx: Scope) -> Element {
    render! {
        Router::<Route> {}
    }
}
```

### Data Fetching (0.7 Server Functions)

```rust
use dioxus::prelude::*;

#[server]
async fn get_user(id: u32) -> Result<User, ServerFnError> {
    // Database logic here
    let user = sqlx::query_as::<_, User>("SELECT * FROM users WHERE id = $1")
        .bind(id)
        .fetch_one(&db)
        .await?;
    Ok(user)
}

// Client usage
fn UserComponent(cx: Scope, id: u32) -> Element {
    let user = use_resource(cx, || async move { get_user(id).await });

    render! {
        div {
            match &*user.read() {
                Some(Ok(user)) => rsx! { "Hello, {user.name}" },
                Some(Err(e)) => rsx! { "Error: {e}" },
                None => rsx! { "Loading..." },
            }
        }
    }
}
```

### Styling (0.7 Enhancements)

- CSS-in-Rust with `dioxus-css` or `dioxus-free-components`
- Tailwind CSS integration with `dioxus-tailwind`
- Scoped CSS with CSS modules support
- New CSS hot-reloading in development
- Inline styles for dynamic styling

## Best Practices

### Performance (0.7 Features)

- Use `use_memo` for expensive computations
- Implement proper key props for lists
- Lazy load routes and components with new suspense boundaries
- Optimize re-renders with signal dependency tracking
- Use `use_callback` for stable event handlers

### Error Handling

- Use `Result` types throughout
- Implement proper error boundaries with `ErrorBoundary`
- Server function error propagation
- Graceful fallbacks for network failures
- New: Global error handling with `use_error_boundary`

### Testing

- Component unit tests with `dioxus-testing`
- Integration tests for fullstack
- E2E testing with Playwright or similar
- Server function testing
- New: Headless rendering for tests

### Security

- Input validation and sanitization
- CSRF protection for server functions
- Proper authentication/authorization
- Environment variable management
- New: Built-in XSS protection

## Tooling & Workflow

### Development Commands (Dioxus CLI 0.7)

```bash
# New project with templates
dx create my-app --template fullstack
dx create my-app --template desktop
dx create my-app --template mobile

# Development server with hot reload
dx serve

# Add dependencies (preferred over manual Cargo.toml editing)
dx add dioxus-logger
dx add sqlx --features postgres
dx add reqwest --features json

# Build for production
dx build --platform web
dx build --platform desktop
dx build --platform mobile

# Generate deployment bundle
dx build --release

# Run tests
dx test

# Check project status
dx check
```

### IDE Setup

- Use rust-analyzer with Dioxus support
- Configure tailwind CSS class completion
- Set up proper TOML language server
- Use Dioxus VS Code extension
- New: Integrated debugging support

### Common Integrations (0.7 Ready)

- **Database**: SQLx for async DB operations with connection pooling
- **HTTP**: reqwest for client requests
- **Authentication**: Auth0 integration via server functions
- **File Uploads**: Multipart form handling with streaming
- **WebSockets**: Real-time communication with built-in support
- **PWA**: Progressive Web App capabilities

## Common Patterns

### Form Handling (0.7 Signals)

```rust
#[component]
fn LoginForm(cx: Scope) -> Element {
    let mut email = use_signal(cx, || "".to_string());
    let mut password = use_signal(cx, || "".to_string());
    let mut errors = use_signal(cx, Vec::<String>::new);

    let on_submit = move |_| {
        // Validation and submission logic
        if email.read().is_empty() {
            errors.write().push("Email is required".to_string());
        }
    };

    render! {
        form { onsubmit: on_submit,
            input {
                type: "email",
                value: "{email}",
                oninput: move |e| email.set(e.value())
            }
            input {
                type: "password",
                value: "{password}",
                oninput: move |e| password.set(e.value())
            }
            for error in errors.read() {
                div { class: "error", "{error}" }
            }
            button { type: "submit", "Login" }
        }
    }
}
```

### API Integration (0.7 Server Functions)

```rust
#[server]
async fn create_post(title: String, content: String) -> Result<Post, ServerFnError> {
    // Validate input
    if title.is_empty() {
        return Err(ServerFnError::ServerError("Title cannot be empty".to_string()));
    }

    // Database insertion
    let post = sqlx::query_as::<_, Post>(
        "INSERT INTO posts (title, content) VALUES ($1, $2) RETURNING *"
    )
    .bind(&title)
    .bind(&content)
    .fetch_one(&db)
    .await?;

    Ok(post)
}

// Usage in component
#[component]
fn PostForm(cx: Scope) -> Element {
    let mut title = use_signal(cx, || "".to_string());
    let mut content = use_signal(cx, || "".to_string());

    let create_post = use_server_future(cx, (title(), content()), |(title, content)| async move {
        create_post(title, content).await
    });

    render! {
        form {
            input {
                value: "{title}",
                oninput: move |e| title.set(e.value())
            }
            textarea {
                value: "{content}",
                oninput: move |e| content.set(e.value())
            }
            button { onclick: move |_| create_post.restart(), "Create Post" }

            match create_post.read() {
                Some(Ok(post)) => rsx! { div { "Created: {post.title}" } },
                Some(Err(e)) => rsx! { div { "Error: {e}" } },
                None => rsx! { div { "Creating..." } },
            }
        }
    }
}
```

### Custom Hooks (0.7 Patterns)

```rust
#[hook]
pub fn use_api<T: Clone + 'static>(
    cx: Scope,
    endpoint: String
) -> UseResource<Result<T, reqwest::Error>> {
    use_resource(cx, || async move {
        reqwest::get(&endpoint).await?.json::<T>().await
    })
}

#[hook]
pub fn use_debounce(cx: Scope, value: String, delay_ms: u64) -> Signal<String> {
    let debounced = use_signal(cx, || value.clone());

    use_effect(cx, (value,), |(value,)| {
        spawn(async move {
            tokio::time::sleep(Duration::from_millis(delay_ms)).await;
            debounced.set(value.clone());
        });
        || ()
    });

    debounced
}
```

## Architecture Tips

1. **Separate Concerns**: Keep server and client logic distinct with clear boundaries
2. **Type Safety**: Leverage Rust's type system across the full stack
3. **Error Boundaries**: Implement graceful error handling at component level
4. **Code Splitting**: Use lazy loading and suspense boundaries for optimal bundle sizes
5. **Environment Management**: Proper dev/staging/prod configuration
6. **Signal Best Practices**: Use signals for derived state and computed values
7. **Server Function Design**: Keep server functions focused and testable
8. **Performance Monitoring**: Use built-in performance tracking and profiling tools

This skill provides the essential knowledge and patterns for building robust, performant fullstack applications with Dioxus 0.7 while maintaining Rust's safety and ergonomics throughout the entire development lifecycle.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qretaio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
