---
name: cpp20-skill-creator
description: Use when creating skills for C++20 standard library features, language constructs, remote third-party libraries, or local C++ projects. Keywords: create C++20 skill, create std skill C++, create concept skill, create local C++ skill, 创建 C++20 skill, 创建标准库技能, 创建 concept 技能, 本地库生成 skill, 动态 C++ skill, skill for ranges, skill for coroutines, skill for concepts, generate C++ skill from local path, C++20 技能, 从文档创建 skill, from docs create C++ skill
metadata:
  author: 13eholder
---

# C++20 Skill Creator

> Create dynamic skills for C++20 language features, standard library components, remote third-party libraries, and **local C++ projects** using official documentation or source headers.

## When to Use

This skill handles requests to create skills for:
- C++20 standard library components (`<ranges>`, `<coroutine>`, `<format>`, etc.)
- C++20 language features (concepts, modules, `consteval`, `requires`, etc.)
- Remote third-party C++ libraries with public documentation (e.g., fmt, range-v3, Boost)
- **Local third-party or internal C++ libraries** (via filesystem path to headers or project root)
- Any C++ documentation URL (primarily [cppreference.com](https://en.cppreference.com))

## Workflow

### 1. Identify the Target

| User Request                                              | Target Type               | Input Pattern                                          |
| --------------------------------------------------------- | ------------------------- | ------------------------------------------------------ |
| "create ranges skill"                                     | C++20 std feature         | `https://en.cppreference.com/w/cpp/ranges`             |
| "create skill for std::views::filter"                     | Specific std component    | `https://en.cppreference.com/w/cpp/ranges/filter_view` |
| "create skill for concept std::integral"                  | C++20 concept             | `https://en.cppreference.com/w/cpp/concepts/integral`  |
| "create skill from https://fmt.dev/latest/api.html"       | Remote third-party lib    | User-provided URL                                      |
| **"create skill from local library at ./libs/mynetwork"** | **Local third-party lib** | **Local filesystem path**                              |

### 2. Execute the Command

Use the `/create-llms-for-skills` command with appropriate flags:

#### For online documentation (cppreference or remote docs):
```bash
/create-llms-for-skills  [requirements]
```

#### For local libraries:
```bash
/create-llms-for-skills --local  [requirements]
```

> ✅ Supports multiple URLs (space-separated) for online mode.  
> ❌ Local mode accepts only one path.

**Examples:**

```bash
# C++20 ranges
/create-llms-for-skills https://en.cppreference.com/w/cpp/ranges

# Concept
/create-llms-for-skills https://en.cppreference.com/w/cpp/concepts/same_as

# Remote library
/create-llms-for-skills https://fmt.dev/latest/api.html

# Local library (include directory)
/create-llms-for-skills --local /home/user/projects/crypto/include

# Local project root (auto-detect include/)
/create-llms-for-skills --local ./third_party/myalgo

# With focus requirement
/create-llms-for-skills --local ./libs/async_net "Focus on TCP client API and error handling"
```

> 🔍 For local paths, the system scans `.h`, `.hpp`, `.hxx` files and parses Doxygen-style comments (`///`, `/** */`) to infer public APIs, constraints, and usage notes. If a `Doxyfile` or `docs/xml/` exists, it will be used for higher-fidelity extraction.

### 3. Follow-up with Skill Creation

After `llms.txt` is generated, use:

```bash
/create-skills-via-llms   [version]
```

**Examples:**
```bash
/create-skills-via-llms ranges ~/tmp/20260125-ranges-llms.txt c++20
/create-skills-via-llms fmt ~/tmp/20260125-fmt-llms.txt v10.2.0
/create-skills-via-llms mycrypto ~/tmp/20260125-mycrypto-llms.txt local
```

> Use `local` as version for private/internal libraries unless a specific version is known.

## URL & Path Construction Helper

| Target                | Input Template                                         |
| --------------------- | ------------------------------------------------------ |
| Standard header       | `https://en.cppreference.com/w/cpp/header/{header}`    |
| Ranges algorithm/view | `https://en.cppreference.com/w/cpp/ranges/{name}`      |
| Concept               | `https://en.cppreference.com/w/cpp/concepts/{name}`    |
| Coroutine utilities   | `https://en.cppreference.com/w/cpp/coroutine/{type}`   |
| Language feature      | `https://en.cppreference.com/w/cpp/language/{feature}` |
| **Local library**     | **`/path/to/lib/` or `/path/to/lib/include/`**         |

> Note: cppreference uses flat naming (e.g., `transform_view`, not `views::transform` in URL).

## Common C++20 Features & Paths

| Feature                            | Category   | URL Path                                                   |
| ---------------------------------- | ---------- | ---------------------------------------------------------- |
| `std::ranges::sort`, `views::take` | Ranges     | `/w/cpp/ranges/{name}`                                     |
| `std::integral`, `std::same_as`    | Concepts   | `/w/cpp/concepts/{name}`                                   |
| `std::coroutine_handle`            | Coroutines | `/w/cpp/coroutine/coroutine_handle`                        |
| `std::format`                      | Formatting | `/w/cpp/utility/format/format`                             |
| `consteval`, `requires`            | Language   | `/w/cpp/language/consteval`, `/w/cpp/language/constraints` |
| Modules (TS)                       | Modules    | `/w/cpp/language/modules` *(note: not fully standardized)* |

## Example Interactions

### Example 1: Create Ranges Skill

```
User: "Create a dynamic skill for C++20 ranges"

Assistant:
1. Identify: C++20 standard library feature "ranges"
2. Execute: /create-llms-for-skills https://en.cppreference.com/w/cpp/ranges
3. Wait for llms.txt generation
4. Execute: /create-skills-via-llms ranges ~/tmp/{timestamp}-ranges-llms.txt c++20
```

### Example 2: Create Concept Skill

```
User: "Create a skill for std::regular and std::semiregular concepts"

Assistant:
1. Identify: C++20 concepts
2. Execute: /create-llms-for-skills https://en.cppreference.com/w/cpp/concepts/regular https://en.cppreference.com/w/cpp/concepts/semiregular
3. Wait for llms.txt
4. Execute: /create-skills-via-llms regular_concepts ~/tmp/{timestamp}-regular-llms.txt c++20
```

### Example 3: Remote Third-Party Library

```
User: "Create skill from https://fmt.dev/latest/api.html"

Assistant:
1. Identify: Remote third-party library
2. Execute: /create-llms-for-skills https://fmt.dev/latest/api.html
3. Follow standard workflow → skill name: fmt
```

### Example 4: Local Third-Party Library

```
User: "Create a skill from my local logging library at ./libs/logger"

Assistant:
1. Identify: Local third-party library
2. Execute: /create-llms-for-skills --local ./libs/logger
3. System scans ./libs/logger/include/logger.hpp, etc., extracts APIs and comments
4. Wait for llms.txt generation
5. Execute: /create-skills-via-llms logger ~/tmp/{timestamp}-logger-llms.txt local
```

## DO NOT

- Use generic skill creators for C++20-specific content
- Skip the `/create-llms-for-skills` step
- Assume URL structure — always verify against cppreference.com or official docs
- Confuse C++17 and C++20 features (e.g., structured bindings are C++17; ranges are C++20)
- Treat `.cpp` files as public interface in local mode — only headers are analyzed
- Run network requests on local paths

## Output Location

All generated skills are saved to: `~/.agent/skills/`

## Notes

- **Primary documentation source**: [cppreference.com](https://en.cppreference.com)
- **For third-party libraries**: Prefer official API docs (e.g., fmt.dev, boost.org)
- **For local libraries**: Quality depends on header clarity and Doxygen-style comments. Libraries with rich annotations yield better skills.
- **Skills should emphasize C++20 idioms**:
  - Concepts over SFINAE
  - Lazy, composable ranges pipelines
  - Coroutine-based async patterns
  - Safe formatting with `std::format`
- **Local skill limitation**: Cannot infer complex template constraints without explicit `requires` clauses or comments. Manual review recommended for critical systems.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/13eholder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
