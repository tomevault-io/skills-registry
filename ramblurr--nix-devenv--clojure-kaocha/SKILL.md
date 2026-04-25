---
name: clojure-kaocha
description: Kaocha test runner for Clojure. Use when working with test configuration (tests.edn), test suites, filtering tests by metadata or ID, skipping/focusing tests, running specific test subsets, or structuring test execution. Triggers on kaocha imports, tests.edn configuration, `bb test` usage, `--focus`, `--skip-meta`, `--focus-meta`, `:kaocha/skip`, `:kaocha/pending`, or questions about test runner setup in Clojure projects. Use when this capability is needed.
metadata:
  author: ramblurr
---

# Kaocha

Kaocha is a Clojure test runner with suite separation, metadata filtering, and
plugin architecture.

## Setup

deps.edn alias:
```clojure
{:aliases
 {:kaocha {:extra-deps {lambdaisland/kaocha {:mvn/version "1.91.1392"}}
           :main-opts  ["-m" "kaocha.runner"]}}}
```

See https://clojars.org/lambdaisland/kaocha for the latest version.

## tests.edn

Place `tests.edn` at project root. Wrap config in `#kaocha/v1 {}` for defaults.

Preferred baseline configuration:
```clojure
#kaocha/v1
{:bindings  {kaocha.stacktrace/*stacktrace-stop-list* ["kaocha.runner"
                                                        "kaocha.plugin.capture_output"
                                                        "kaocha.testable"]}
 :plugins   [:kaocha.plugin.alpha/info
              :kaocha.plugin/randomize
              :kaocha.plugin/filter
              :kaocha.plugin/capture-output
              :kaocha.plugin/profiling]

 :kaocha/reporter [kaocha.report/dots]

 :tests     [{:id          :unit
              :test-paths  ["test"]
              :ns-patterns ["^(?!my\\.app\\.live\\.).*-test$"]}
             {:id          :live
              :test-paths  ["test"]
              :ns-patterns ["^my\\.app\\.live\\..*-test$"]}]}
```

Key points:
- `*stacktrace-stop-list*` trims stacktraces at kaocha internals for readable output.
- `:plugins` includes `filter`, `profiling`, `info`, `randomize`, and `capture-output`. Do not include `kaocha-retry` unless explicitly needed.
- `:kaocha/reporter` uses `dots` for concise output; switch to `kaocha.report/documentation` for verbose.
- Suites split by `:ns-patterns` regex. The `:unit` suite excludes live namespaces; the `:live` suite includes only integration test namespaces.

## Running with bb

Convention: `bb test` delegates to Kaocha, forwarding args:
```clojure
;; bb.edn
test
{:doc  "Run tests"
 :task (apply clojure "-M:dev:kaocha"
              (if (seq *command-line-args*) *command-line-args* [":unit"]))}
```

This defaults to `:unit` when called without arguments.

Usage:
```bash
bb test              # runs :unit suite
bb test :live        # runs :live suite
bb test :unit :live  # runs both suites
```

## Suites

Define multiple suites in the `:tests` vector. Each suite has:

| Key | Purpose |
|-----|---------|
| `:id` | Suite name (keyword), used on CLI |
| `:test-paths` | Directories containing test sources |
| `:ns-patterns` | Regex patterns matching namespace names |
| `:kaocha.filter/skip-meta` | Metadata keys to skip in this suite |
| `:kaocha.filter/focus-meta` | Metadata keys to focus on in this suite |

Suites share the same test directory but select different namespaces via `:ns-patterns`.

## Filtering by ID

Focus on a single test or namespace:
```bash
bb test --focus my.app.core-test/specific-test
bb test --focus my.app.core-test
```

Skip a specific test:
```bash
bb test --skip my.app.core-test/slow-test
```

In tests.edn:
```clojure
{:tests [{:id :unit
          :kaocha.filter/skip [my.app.core-test/slow-test]}]}
```

## Metadata Filtering

The filter plugin (included by default) supports filtering tests by metadata on `deftest` or on namespaces.

### Built-in metadata

- `^:kaocha/skip` - silently skipped (configured by default via `:kaocha.filter/skip-meta [:kaocha/skip]`)
- `^:kaocha/pending` - skipped and reported as PENDING in output with count

```clojure
(deftest ^:kaocha/skip not-ready-yet
  (is (= 1 2)))

(deftest ^:kaocha/pending todo-test
  (is (= 1 2)))
```

### Custom metadata for conditional tests

Tag tests with custom metadata and filter via CLI or config:

```clojure
(deftest ^:live-postgres pg-connection-test
  (test-pg-connection ...))

(deftest ^:live-redis redis-cache-test
  (test-redis-ops ...))

(deftest ^:slow slow-integration-test
  (run-heavy-test ...))
```

Skip by metadata (CLI):
```bash
bb test :live --skip-meta :live-postgres
```

Focus by metadata (only run matching tests):
```bash
bb test :live --focus-meta :live-postgres
```

In tests.edn (skip tagged tests by default):
```clojure
{:tests [{:id :live
          :kaocha.filter/skip-meta [:live-postgres :live-redis]}]}
```

Use `^:replace` to override default skip-meta instead of appending:
```clojure
{:tests [{:kaocha.filter/skip-meta ^:replace [:my-custom-skip]}]}
```

### Namespace-level metadata

Metadata on a namespace affects all tests in that namespace:
```clojure
(ns ^:slow my.app.integration-test
  (:require [clojure.test :refer :all]))
```
```bash
bb test --skip-meta :slow
```

### Combining filters

Focus and skip can be used together:
```bash
bb test --focus-meta :integration --skip-meta :flaky
```

### focus-meta fallback behavior

If no tests match `--focus-meta`, the filter is ignored and all tests in scope run. This prevents accidentally running zero tests.

## Pattern: Environment-Driven Conditional Tests

For tests requiring external services (databases, third-party APIs, etc.), combine metadata tagging with a `bb` task that reads env vars. This keeps test bodies clean with no conditional wrapping.

1. Tag each test with service metadata:
```clojure
(deftest ^:live-postgres pg-connection-test ...)
(deftest ^:live-redis redis-cache-test ...)
(deftest ^:live-s3 s3-upload-test ...)
```

2. Skip all service metadata by default in tests.edn:
```clojure
{:tests [{:id :live
          :kaocha.filter/skip-meta [:live-postgres :live-redis :live-s3]}]}
```

3. Create a bb task that builds `--focus-meta` flags from env:
```clojure
test:live
{:doc  "Run live tests for enabled services"
 :task (let [services {"LIVE_ENABLE_POSTGRES" :live-postgres
                       "LIVE_ENABLE_REDIS"    :live-redis
                       "LIVE_ENABLE_S3"       :live-s3}
             enabled  (->> services
                           (filter (fn [[env-var _]] (= "1" (System/getenv env-var))))
                           (mapcat (fn [[_ kw]] ["--focus-meta" (str kw)])))]
         (if (seq enabled)
           (apply clojure "-M:dev:kaocha" ":live" enabled)
           (println "No live services enabled. Set LIVE_ENABLE_POSTGRES=1 etc.")))}
```

Run with:
```bash
LIVE_ENABLE_POSTGRES=1 bb test:live          # only postgres tests
LIVE_ENABLE_POSTGRES=1 LIVE_ENABLE_REDIS=1 bb test:live  # postgres + redis
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramblurr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
