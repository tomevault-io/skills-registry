---
name: clojure-malli
description: | Use when this capability is needed.
metadata:
  author: ramblurr
---

# Malli Data Validation

Malli validates data against schemas. Schemas are Clojure data structures.

## Setup

deps.edn dependency:
```clojure
{:deps {metosin/malli {:mvn/version "0.20.0"}}}
```

See https://clojars.org/metosin/malli for the latest version.

## Quick Validation

```clojure
(require '[malli.core :as m])
(require '[malli.error :as me])

;; Validate
(m/validate [:map [:name :string] [:age :int]]
            {:name "Alice" :age 30})
;; => true

;; Get errors
(-> [:map [:name :string] [:age :int]]
    (m/explain {:name "Alice" :age "thirty"})
    (me/humanize))
;; => {:age ["should be an integer"]}
```

## Quick Coercion

```clojure
(require '[malli.transform :as mt])

;; Decode string input to proper types
(m/decode [:map [:port :int] [:active :boolean]]
          {:port "8080" :active "true"}
          (mt/string-transformer))
;; => {:port 8080, :active true}

;; Coerce = decode + validate (throws on error)
(m/coerce [:map [:id :int]] {:id "42"} (mt/string-transformer))
;; => {:id 42}
```

## Common Schema Patterns

### Maps with Required/Optional Keys

```clojure
[:map
 [:id :uuid]                              ;; required
 [:name :string]                          ;; required
 [:email {:optional true} :string]        ;; optional
 [:role {:default "user"} :string]]       ;; optional with default
```

### Constrained Values

```clojure
[:string {:min 1 :max 100}]    ;; string length 1-100
[:int {:min 0 :max 150}]       ;; integer range
[:enum "draft" "published"]    ;; one of these values
[:re #".+@.+\..+"]             ;; regex match
```

### Collections

```clojure
[:vector :int]                 ;; vector of ints
[:set :keyword]                ;; set of keywords
[:map-of :keyword :string]     ;; map with keyword keys, string values
[:tuple :double :double]       ;; fixed [x, y] pair
```

### Unions and Conditionals

```clojure
;; Simple union
[:or :string :int]

;; Nilable
[:maybe :string]               ;; string or nil

;; Tagged union with dispatch
[:multi {:dispatch :type}
 [:user [:map [:type [:= :user]] [:name :string]]]
 [:admin [:map [:type [:= :admin]] [:role :string]]]]
```

### Nested Structures

```clojure
[:map
 [:user [:map
         [:name :string]
         [:address [:map
                    [:city :string]
                    [:zip :string]]]]]]
```

## Performance: Cache Validators

```clojure
;; BAD - creates validator every call
(defn process [data]
  (when (m/validate schema data) ...))

;; GOOD - cached validator
(def valid? (m/validator schema))
(defn process [data]
  (when (valid? data) ...))

;; Same for decoders
(def decode-request (m/decoder schema (mt/string-transformer)))
(def coerce-request (m/coercer schema (mt/string-transformer)))
```

## Lite Syntax (malli.experimental.lite)

Simplified syntax sugar for creating schemas, useful for quick map definitions.

```clojure
(require '[malli.experimental.lite :as l])

(l/schema
 {:map1 {:x :int
         :y [:maybe :string]
         :z (l/maybe :keyword)}
  :map2 {:min-max [:int {:min 0 :max 10}]
         :tuples (l/vector (l/tuple :int :string))
         :optional (l/optional (l/maybe :boolean))
         :set-of-maps (l/set {:e :int
                              :f :string})
         :map-of-int (l/map-of :int {:s :string})}})
```

Produces:

```clojure
[:map
 [:map1
  [:map
   [:x :int]
   [:y [:maybe :string]]
   [:z [:maybe :keyword]]]]
 [:map2
  [:map
   [:min-max [:int {:min 0, :max 10}]]
   [:tuples [:vector [:tuple :int :string]]]
   [:optional {:optional true} [:maybe :boolean]]
   [:set-of-maps [:set [:map [:e :int] [:f :string]]]]
   [:map-of-int [:map-of :int [:map [:s :string]]]]]]]
```

Lite functions: `l/schema`, `l/maybe`, `l/optional`, `l/vector`, `l/tuple`, `l/set`, `l/map-of`.

Custom registries via dynamic binding:

```clojure
(binding [l/*options* {:registry (merge (m/default-schemas) {:user/id :int})}]
  (l/schema {:id (l/maybe :user/id)
             :child {:id :user/id}}))
```

## Anti-Patterns

### Do NOT use :closed true

Maps are open by default in malli. Do not add `{:closed true}` to map schemas
unless the human has explicitly confirmed it is needed for the specific use case.
Closing maps breaks extensibility and causes brittle validation failures when
upstream data adds new keys. Almost never the right default.

```clojure
;; BAD - do not do this without explicit human confirmation
[:map {:closed true}
 [:name :string]
 [:age :int]]

;; GOOD - open maps (the default)
[:map
 [:name :string]
 [:age :int]]
```

### Prefer optional keys over :maybe for absent values

When a key may not be present, use `{:optional true}` rather than `[:maybe ...]`.
We prefer non-existence of a key over presence of a key with a nil value.

```clojure
;; BAD - allows {:email nil} which is a meaningless sentinel value
[:map
 [:name :string]
 [:email [:maybe :string]]]

;; GOOD - key is simply absent when not provided
[:map
 [:name :string]
 [:email {:optional true} :string]]
```

Use `[:maybe ...]` only when nil is a meaningful, distinct value in your domain
(e.g., "user explicitly cleared this field").

## Key Gotchas

1. decode doesn't validate - returns invalid data as-is. Use `coerce` for safety.
2. Maps are open by default - extra keys are allowed. See Anti-Patterns above before reaching for `:closed true`.
3. Keys are required by default - use `{:optional true}` for optional keys.

## Detailed References

- [Schema Syntax](references/schema-syntax.md) - All builtin types, properties, operators
- [API Reference](references/api-reference.md) - Core functions with signatures
- [Registries](references/registries.md) - Reusable schema patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramblurr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
