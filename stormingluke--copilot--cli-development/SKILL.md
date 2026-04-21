---
name: cli-development
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## Go CLI Patterns

Every Go project in this setup uses cobra-cli and viper. Whether the
binary starts an API server or runs as a CLI tool, the entry point is
always a cobra command with viper configuration.

### Entry Point Structure (always present)

```
cmd/
  main.go              # Minimal: calls root command Execute()
  root.go              # Root cobra command, viper init, global flags
  serve.go             # `serve` subcommand (starts API/gRPC server)
  version.go           # `version` subcommand
  <verb>.go            # Additional subcommands
```

```go
// cmd/main.go — always this simple
package main

func main() {
    cmd.Execute()
}
```

```go
// cmd/root.go — cobra + viper wiring
package cmd

var (
    cfgFile string
    rootCmd = &cobra.Command{
        Use:   "mytool",
        Short: "Brief description",
    }
)

func Execute() {
    if err := rootCmd.Execute(); err != nil {
        os.Exit(1)
    }
}

func init() {
    cobra.OnInitialize(initConfig)
    rootCmd.PersistentFlags().StringVar(&cfgFile, "config", "",
        "config file (default $HOME/.mytool.yaml)")
}

func initConfig() {
    if cfgFile != "" {
        viper.SetConfigFile(cfgFile)
    } else {
        viper.SetConfigName(".mytool")
        viper.SetConfigType("yaml")
        viper.AddConfigPath(".")
        viper.AddConfigPath("$HOME/.config/mytool")
        viper.AddConfigPath("/etc/mytool")
    }
    viper.SetEnvPrefix("MYTOOL")
    viper.AutomaticEnv()
    viper.ReadInConfig() // ignore error — config file is optional
}
```

### API Server Pattern

When the CLI starts a server (API, gRPC, controller):

```go
// cmd/serve.go
var serveCmd = &cobra.Command{
    Use:   "serve",
    Short: "Start the API server",
    RunE: func(cmd *cobra.Command, args []string) error {
        cfg, err := config.Load()  // viper-backed config
        if err != nil {
            return fmt.Errorf("loading config: %w", err)
        }

        app, err := factory.New(cfg)  // hexagonal factory
        if err != nil {
            return fmt.Errorf("building application: %w", err)
        }
        defer app.Close()

        return app.Run(cmd.Context())  // blocks until shutdown
    },
}
```

### Viper Configuration

Configuration loading lives in `internal/infrastructure/config/`:

```go
// internal/infrastructure/config/config.go
type Config struct {
    Server   ServerConfig   `mapstructure:"server"`
    Database DatabaseConfig `mapstructure:"database"`
    Log      LogConfig      `mapstructure:"log"`
}

func Load() (*Config, error) {
    var cfg Config
    if err := viper.Unmarshal(&cfg); err != nil {
        return nil, fmt.Errorf("unmarshaling config: %w", err)
    }
    return &cfg, nil
}
```

Configuration precedence (viper default):
1. Explicit flag values
2. Environment variables (`MYTOOL_SERVER_PORT`)
3. Config file (`.mytool.yaml`)
4. Default values

### CLI Tool Pattern

When the binary is a CLI tool (not a server):

```go
// cmd/create.go
var createCmd = &cobra.Command{
    Use:   "create [name]",
    Short: "Create a new resource",
    Args:  cobra.ExactArgs(1),
    RunE: func(cmd *cobra.Command, args []string) error {
        // ... create logic
    },
}
```

- Use verb-noun pattern: `tool create cluster`, `tool get pods`
- Persistent flags on root for global options (`--kubeconfig`, `--namespace`, `--output`)
- Local flags on subcommands for command-specific options
- Flag binding: `viper.BindPFlag()` to merge flags with config

### Output
- Support `--output` / `-o` flag with: `table` (default), `json`, `yaml`, `wide`
- Use `tabwriter` for aligned table output
- JSON output must be machine-parseable (no decoration, no colour)
- Colour: use `lipgloss` or `termenv`, respect `NO_COLOR` env var
- Errors to stderr, results to stdout
- Use exit codes: 0 = success, 1 = general error, 2 = usage error

### Interactive Mode
- Use `bubbletea` for TUI applications
- Use `huh` or `survey` for interactive prompts
- Always provide non-interactive alternatives via flags
- Detect TTY: `os.Stdout` is a terminal vs piped

### Kubernetes CLI Integration
- Accept `--kubeconfig` flag and `KUBECONFIG` env var
- Use `client-go` rest.Config loading with standard precedence
- Support `--namespace` / `-n` with fallback to current context namespace
- Use `cmdutil.Factory` pattern from kubectl for consistent UX
- Implement `--dry-run` for mutating commands

### Graceful Shutdown

All server commands must handle graceful shutdown:

```go
ctx, cancel := signal.NotifyContext(cmd.Context(), os.Interrupt, syscall.SIGTERM)
defer cancel()
```

### Distribution
- Cross-compile with `goreleaser`
- Provide shell completions: `cobra.GenBashCompletion`, `GenZshCompletion`, `GenFishCompletion`
- Include `man` page generation if applicable
- Use semantic versioning, embed version at build time via `-ldflags`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
