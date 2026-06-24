---
name: rust-impl-clap-cli
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# rust-impl-clap-cli

The **clap 4.x CLI parser** skill: how to scaffold a command-line interface with the derive macro API, when to drop to the builder API for dynamic CLIs, how to model subcommands and reusable argument groups, how to wire custom value parsers and environment-variable fallbacks, and how to ship shell completions through `clap_complete`.

Cross-references: [[rust-impl-cargo-project]] (declaring the `derive` feature in `Cargo.toml`), [[rust-syntax-traits]] (derive macros, `FromStr` for custom parsers), [[rust-impl-error-handling]] (returning `anyhow::Result` from `main`), [[rust-impl-testing]] (`try_parse_from` for unit-testable CLI logic).

---

## When to use this skill

- User runs `cargo add clap` and asks which features to enable.
- User writes `#[derive(Parser)]` on a struct and gets a "trait `Parser` is not implemented" error.
- User wants nested subcommands (e.g. `git remote add ...`) and is unsure whether to use `Subcommand`, `Args`, or both.
- User has a non-`String` argument type and the parser refuses to compile.
- User wants `--config` to fall back to an environment variable, or to a typed default value.
- User wants to generate Bash, Zsh, Fish, PowerShell, or Elvish completions.
- User builds a CLI whose argument set is decided at runtime and asks how to skip the derive API.
- User asks how to unit-test argument parsing without invoking the binary.

For the `Cargo.toml` mechanics of activating optional crate features see [[rust-impl-cargo-project]]. For mapping argument values into your domain types via `FromStr` see [[rust-syntax-traits]].

---

## Quick reference: Cargo.toml entry

```toml
[dependencies]
clap = { version = "4.6", features = ["derive"] }
clap_complete = "4.6"   # only if shipping shell completions
```

ALWAYS include `features = ["derive"]` when using `#[derive(Parser)]`. Without it the macros are absent and the compiler emits "cannot find derive macro `Parser` in this scope". NEVER write `clap = "4.6"` alone and expect derive to work.

---

## Decision tree: derive vs builder

```
Is your CLI structure known at compile time?
   YES -> use the derive API (#[derive(Parser)]).
   NO  -> use the builder API (Command::new(...).arg(...)).

Do you need dynamic arguments based on a config file or plugin discovery?
   YES -> builder API.
   NO  -> derive API.

Do you need maximum compile-time validation and IDE autocomplete on the parsed struct?
   YES -> derive API.

Are you generating bash/zsh completions for a plugin host?
   The Command tree is the same in both APIs;
   prefer derive and call `Cli::command()` to get the underlying Command.
```

ALWAYS start with the derive API. Drop to the builder API only when the argument set is genuinely unknown until runtime. NEVER mix the two for one command: choose one per `Command` tree.

---

## Minimal derive CLI

```rust
use clap::Parser;

/// A short description that becomes --help text.
#[derive(Parser, Debug)]
#[command(name = "myapp", version, about = "Does the thing", long_about = None)]
struct Cli {
    /// File to operate on
    #[arg(short, long)]
    file: std::path::PathBuf,

    /// Verbose output
    #[arg(short, long)]
    verbose: bool,
}

fn main() {
    let cli = Cli::parse();
    println!("file={:?} verbose={}", cli.file, cli.verbose);
}
```

`Cli::parse()` reads from `std::env::args_os()` and exits the process on parse error or on `--help` / `--version`. For test-friendly variants see the testing section below.

ALWAYS derive `Debug` together with `Parser` during development so the parsed struct can be printed with `{:#?}`. NEVER call `Cli::parse()` inside library code: it terminates the process; parse once in `main` and pass the struct downward.

---

## Subcommands

```rust
use clap::{Parser, Subcommand};

#[derive(Parser, Debug)]
#[command(name = "git-lite", version)]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand, Debug)]
enum Commands {
    /// Stage files for the next commit
    Add {
        /// Paths to stage
        paths: Vec<std::path::PathBuf>,
    },
    /// Record staged changes
    Commit {
        #[arg(short, long)]
        message: String,
    },
    /// Manage remotes
    Remote {
        #[command(subcommand)]
        action: RemoteAction,
    },
}

#[derive(Subcommand, Debug)]
enum RemoteAction {
    Add { name: String, url: String },
    Remove { name: String },
    List,
}
```

ALWAYS attach `#[command(subcommand)]` to the field carrying the `Subcommand`-deriving enum on the parent struct or variant. NEVER decorate the enum itself with `#[command(subcommand)]`: the attribute belongs on the field that holds the enum.

Use nested `#[command(subcommand)]` (as in `RemoteAction` above) for `git remote add` style hierarchies. The depth is unlimited but more than two levels usually warrants a different top-level structure.

---

## Reusable argument groups with `Args`

```rust
use clap::{Args, Parser, Subcommand};

#[derive(Args, Debug)]
struct GlobalOpts {
    /// Path to the config file
    #[arg(long, env = "MYAPP_CONFIG", default_value = "~/.myapp.toml")]
    config: std::path::PathBuf,

    /// Suppress non-essential output
    #[arg(short, long, global = true)]
    quiet: bool,
}

#[derive(Parser, Debug)]
struct Cli {
    #[command(flatten)]
    globals: GlobalOpts,

    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand, Debug)]
enum Commands {
    Build,
    Test,
}
```

ALWAYS use `Args` plus `#[command(flatten)]` for argument sets that belong to several commands (e.g. `--config`, `--verbose`, `--quiet`). NEVER use `Subcommand` to share arguments: `Subcommand` models *alternative* commands, `Args` models *shared* argument lists.

`#[arg(global = true)]` makes an argument visible to the parent command **and** every subcommand. Without it, `myapp --quiet build` parses but `myapp build --quiet` does not.

---

## Choice values with `ValueEnum`

```rust
use clap::{Parser, ValueEnum};

#[derive(Copy, Clone, Debug, ValueEnum)]
enum Format {
    Json,
    Yaml,
    Toml,
}

#[derive(Parser, Debug)]
struct Cli {
    #[arg(short, long, value_enum, default_value_t = Format::Json)]
    format: Format,
}
```

ALWAYS prefer `ValueEnum` over `value_parser` plus `FromStr` for closed sets of accepted values: it auto-generates `possible_values` in `--help` and gives the shell completion generator the value list for free. NEVER hand-roll a custom parser for an enum when `ValueEnum` would compile.

---

## Custom `value_parser`

```rust
use clap::{Parser, value_parser};

fn port_in_range(s: &str) -> Result<u16, String> {
    let port: u16 = s.parse().map_err(|_| format!("`{s}` is not a port"))?;
    if (1024..=65535).contains(&port) {
        Ok(port)
    } else {
        Err(format!("port `{port}` not in 1024-65535"))
    }
}

#[derive(Parser, Debug)]
struct Cli {
    /// Bind port (1024-65535)
    #[arg(short, long, value_parser = port_in_range, default_value_t = 8080)]
    port: u16,

    /// Worker count (1..=64)
    #[arg(long, value_parser = value_parser!(u32).range(1..=64))]
    workers: u32,

    /// Source file (must exist; resolved as PathBuf)
    #[arg(long, value_parser = value_parser!(std::path::PathBuf))]
    input: std::path::PathBuf,
}
```

ALWAYS provide `value_parser` for any non-`String` type unless the type implements `clap::builder::ValueParserFactory` (most primitives do, `PathBuf` does, `OsString` does). NEVER assume `value_parser!(MyType)` works for a custom struct: implement `FromStr` and pass the function directly, or call `value_parser!(MyType)` only if `MyType: ValueParserFactory`.

Numeric ranges use the inline form `value_parser!(u32).range(1..=64)`. The full list of supported types and factory methods is in `references/methods.md`.

---

## Environment-variable fallback

```rust
#[derive(Parser, Debug)]
struct Cli {
    /// API token (also reads $MYAPP_TOKEN)
    #[arg(long, env = "MYAPP_TOKEN", required = true)]
    token: String,

    /// Log level (also reads $RUST_LOG)
    #[arg(long, env = "RUST_LOG", default_value = "info")]
    log_level: String,
}
```

Precedence is **CLI argument > environment variable > `default_value`**. ALWAYS document the env var in the doc comment that becomes `--help` text. NEVER store secrets in `default_value` (it appears in `--help`); the env var pattern keeps secrets out of help output and shell history.

---

## Defaults: `default_value` vs `default_value_t`

| Attribute | Operand type | Use when |
|-----------|--------------|----------|
| `default_value = "8080"` | String literal | The field type implements `FromStr` and you want clap to parse the string at runtime. |
| `default_value_t = 8080u16` | Typed expression | The field type implements `Default` or `Display`, and you want the default to appear typed in `--help`. |
| `default_value_t` (no `=`) | none | The field type implements `Default`; equivalent to `default_value_t = Default::default()`. |

ALWAYS use `default_value_t` for non-string types so that the help output prints the typed representation. NEVER mix `default_value = "..."` with `value_parser` returning a non-`String` type unless the string is guaranteed to parse: the parse failure surfaces at runtime, not compile time.

---

## Required, optional, and multiple

```rust
#[derive(Parser, Debug)]
struct Cli {
    /// Required positional argument
    name: String,

    /// Required flag (no default, no Option)
    #[arg(short, long, required = true)]
    output: std::path::PathBuf,

    /// Optional flag
    #[arg(short, long)]
    label: Option<String>,

    /// Zero or more inputs (positional)
    inputs: Vec<std::path::PathBuf>,

    /// One or more includes (flag, repeated)
    #[arg(short = 'I', long = "include")]
    includes: Vec<String>,

    /// Verbosity counter (-v, -vv, -vvv ...)
    #[arg(short, long, action = clap::ArgAction::Count)]
    verbose: u8,
}
```

Field-type semantics: `T` is required, `Option<T>` is optional, `Vec<T>` is repeated zero-or-more, `bool` is a flag with `ArgAction::SetTrue`. ALWAYS choose the field type that already encodes the cardinality; NEVER set `required = true` on an `Option<T>` field (it contradicts the type).

For incrementing counters use `action = ArgAction::Count` on a `u8` / `u16` / `u32` field.

---

## Builder API (dynamic CLIs only)

```rust
use clap::{Arg, ArgAction, Command, value_parser};

fn build_cli(plugins: &[String]) -> Command {
    let mut cmd = Command::new("myapp")
        .version("1.0")
        .arg(
            Arg::new("verbose")
                .short('v')
                .long("verbose")
                .action(ArgAction::SetTrue)
                .help("Verbose output"),
        )
        .arg(
            Arg::new("port")
                .long("port")
                .value_parser(value_parser!(u16).range(1024..=65535))
                .default_value("8080"),
        );

    for plugin in plugins {
        cmd = cmd.subcommand(Command::new(plugin.clone()).about("Dynamic plugin"));
    }
    cmd
}

fn main() {
    let plugins = discover_plugins();
    let matches = build_cli(&plugins).get_matches();
    let verbose = matches.get_flag("verbose");
    let port: u16 = *matches.get_one("port").expect("default");
    let _ = (verbose, port);
}

fn discover_plugins() -> Vec<String> { vec![] }
```

ALWAYS use the builder API when the set of subcommands or arguments is computed from runtime state (plugin discovery, configuration file, etc.). NEVER bend the derive API with `#[command(skip)]` plus runtime hacks: the builder API exists for exactly this case.

Access parsed values via `ArgMatches::get_one::<T>("id")`, `get_many::<T>("id")`, and `get_flag("id")`. The `id` is the string passed to `Arg::new("...")`.

---

## Shell completions

```rust
use clap::{CommandFactory, Parser};
use clap_complete::{generate, Shell};
use std::io;

#[derive(Parser, Debug)]
#[command(name = "myapp", version)]
struct Cli {
    /// Print completions for the given shell to stdout
    #[arg(long, value_enum)]
    completions: Option<Shell>,
}

fn main() {
    let cli = Cli::parse();
    if let Some(shell) = cli.completions {
        let mut cmd = Cli::command();
        let name = cmd.get_name().to_string();
        generate(shell, &mut cmd, name, &mut io::stdout());
        return;
    }
}
```

ALWAYS expose completions through a dedicated flag or subcommand that writes to stdout (the convention is `myapp completions <shell>` or `myapp --completions <shell>`). NEVER hand-write `_myapp` Zsh files or Bash `complete -F` snippets: `clap_complete::generate` produces correct, version-aware output.

Install instructions for users: `myapp --completions bash > /etc/bash_completion.d/myapp`, or write to `$ZDOTDIR/_myapp` for Zsh. Shell-specific install paths are in `references/examples.md`.

---

## Testing argument parsing

```rust
use clap::Parser;

#[derive(Parser, Debug, PartialEq)]
struct Cli {
    #[arg(short, long)]
    name: String,
}

#[test]
fn parses_flag() {
    let cli = Cli::try_parse_from(["myapp", "--name", "alice"]).unwrap();
    assert_eq!(cli.name, "alice");
}

#[test]
fn missing_required_is_error() {
    let err = Cli::try_parse_from(["myapp"]).unwrap_err();
    assert_eq!(err.kind(), clap::error::ErrorKind::MissingRequiredArgument);
}

#[test]
fn cli_definition_is_consistent() {
    use clap::CommandFactory;
    Cli::command().debug_assert();
}
```

ALWAYS use `try_parse_from(...)` in tests: it returns `Result<Self, clap::Error>` and never exits the process. NEVER call `Cli::parse()` from a `#[test]`: a parse failure will terminate the test process, hiding the real assertion failure.

ALWAYS add one `debug_assert()`-style test per `Parser` struct: it catches misconfigured `Command` trees (duplicate ids, conflicting short flags) before they reach users.

---

## Common cargo entries

```toml
[dependencies]
clap = { version = "4.6", features = ["derive", "env", "wrap_help"] }
clap_complete = "4.6"
```

| Feature | What it enables |
|---------|-----------------|
| `derive` | The `#[derive(Parser, Args, Subcommand, ValueEnum)]` macros. |
| `env` | The `env = "VAR"` attribute on `#[arg]`. Required if you use env-var fallback. |
| `wrap_help` | Wraps `--help` output to terminal width via `terminal_size`. |
| `unicode` | Unicode-aware help formatting. |
| `string` | Allow `String` (in addition to `&'static str`) for help and about text. |

NEVER omit `env` from the feature list if any `#[arg(env = "...")]` exists in the code: omitting it produces a confusing "unknown attribute" error rather than a "feature gate" error.

---

## Anti-patterns

See `references/anti-patterns.md` for the full enumeration with fixes. Highlights:

1. Forgetting `features = ["derive"]` in the clap Cargo entry.
2. Picking the builder API when derive would be ten times shorter.
3. Omitting `value_parser` for non-`String` types and getting an `&String` at runtime.
4. Setting `#[command(name = "foo")]` while `Cargo.toml`'s `[[bin]]` name is `bar`.
5. Hand-rolling shell completion files instead of using `clap_complete::generate`.
6. Deeply nesting `Subcommand` enums without setting `about` on each variant.
7. Calling `Cli::parse()` from a test (process exits, test framework fails to report).
8. Using `Subcommand` to model shared arguments (use `Args` plus `flatten`).
9. Storing secrets in `default_value` (they print in `--help`).

---

## Reference files

- `references/methods.md`: full attribute and method catalogue for `#[command(...)]`, `#[arg(...)]`, `#[group(...)]`, `#[value(...)]`, `value_parser!`, `ArgAction`, `Command`, `Arg`, `ArgMatches`, `clap_complete::Shell`.
- `references/examples.md`: end-to-end CLIs (single binary, subcommand tree, dynamic plugin host, shell-completion install snippets, env-var-driven configuration).
- `references/anti-patterns.md`: numbered anti-patterns with the failure mode and the deterministic fix.

---

## Official sources

- clap top-level reference: https://docs.rs/clap/latest/clap/
- clap derive reference: https://docs.rs/clap/latest/clap/_derive/index.html
- clap_complete: https://docs.rs/clap_complete/latest/clap_complete/
- Command type (debug_assert, try_get_matches_from): https://docs.rs/clap/latest/clap/struct.Command.html

Verified 2026-05-19.

---
> Source: [Impertio-Studio/Rust-Claude-Skill-Package](https://github.com/Impertio-Studio/Rust-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
