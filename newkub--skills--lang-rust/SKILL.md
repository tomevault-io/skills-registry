---
name: lang-rust
description: description: Best practices for Rust development including project structure, error handling, memory safety, and performance optimization Use when this capability is needed.
metadata:
  author: newkub
---
---
name: rust
description: Best practices for Rust development including project structure, error handling, memory safety, and performance optimization
goal: Develop Rust applications following best practices
outcome: Safe, performant, and maintainable Rust projects
---

# Rust

## When to Execute

Use this skill when developing applications with Rust. Execute when you need proper memory safety, error handling, and performance optimization.

### Folder Structure Summary

| Folder | Purpose | Required Files | When to Use |
|--------|---------|----------------|-------------|
| `get-started/` | Introduction and setup | introduction.md, goal.md, configuration.md, usage.md, key-concept.md, core-principles.md, should.md | Learning Rust basics |
| `patterns/` | Best practices patterns | must/safety.md, must/error-handling.md, must/api-contract.md, should/code-style.md, should/naming.md, should/performance-tips.md, should/documentation.md | Writing quality code |
| `anti-patterns/` | Common mistakes to avoid | must/safety.md, must/error-handling.md, must/api-contract.md, should/code-style.md, should/naming.md, should/performance-tips.md, should/documentation.md | Avoiding pitfalls |
| `workflows/` | Development workflows | workflow files | Streamlining development |
| `tools/` | Development tools | tool documentation | Using Rust ecosystem |
| `optimization/` | Performance optimization | perf/, mem/, cpu/, io/, build/, runtime/, dx/ | Optimizing applications |
| `architecture/` | System architecture | architecture patterns | Designing systems |
| `ecosystem/` | Rust ecosystem | ecosystem information | Understanding ecosystem |
| `production/` | Production deployment | deployment guides | Deploying to production |
| `security/` | Security practices | security guidelines | Writing secure code |
| `scalability/` | Scaling applications | scaling strategies | Handling growth |
| `glossary/` | Terminology | term definitions | Understanding vocabulary |
| `learning-path/` | Learning roadmap | learning guides | Structured learning |
| `practices/` | Development practices | practice guidelines | Daily development |
| `guide/` | Advanced guides | advanced topics | Deep learning |
| `examples/` | Code examples | example files | Learning by examples |
| `apis/` | API documentation | API references | API usage |

## Quick Start

1. Create new Rust project with `cargo new my-project`
2. Set up project structure following patterns/must/safety.md
3. Manage dependencies in Cargo.toml following best practices
4. Write code following ownership and borrowing rules
5. Run `cargo test` for testing and `cargo build --release` for production

## Rules

| Rule | Priority | Impact | File | Description | Trigger |
|------|----------|--------|------|-------------|---------|
| Safety Patterns | 1 | `CRITICAL` | patterns/must/safety.md | Follow safety rules strictly | Writing code |
| Error Handling | 1 | `CRITICAL` | patterns/must/error-handling.md | Handle errors properly | Error scenarios |
| API Contracts | 1 | `CRITICAL` | patterns/must/api-contract.md | Maintain API consistency | API design |
| Code Style | 2 | `HIGH` | patterns/should/code-style.md | Write readable code | Development |
| Naming | 2 | `HIGH` | patterns/should/naming.md | Use clear naming | Development |
| Performance | 2 | `HIGH` | patterns/should/performance-tips.md | Optimize performance | Optimization |
| Documentation | 2 | `HIGH` | patterns/should/documentation.md | Document code properly | Development |

## Knowledge

| Resource | Type | Purpose | Content |
|----------|------|---------|---------|
| get-started/introduction.md | Documentation | Core understanding | Introduction to Rust |
| get-started/goal.md | Documentation | Objectives | Learning goals |
| get-started/configuration.md | Documentation | Setup | Environment setup |
| get-started/usage.md | Documentation | Basic usage | Simple examples |
| get-started/key-concept.md | Documentation | Key concepts | Core Rust concepts |
| get-started/core-principles.md | Documentation | Principles | Rust principles |
| get-started/should.md | Documentation | Guidelines | Best practices |

## Templates

### Programming Language Template

| Template | File Structure | Purpose |
|----------|----------------|---------|
| Rust Language Skill | SKILL.md, get-started/, patterns/, anti-patterns/, workflows/, tools/, optimization/, architecture/, ecosystem/, production/, security/, scalability/, glossary/, learning-path/, practices/, guide/, examples/, apis/ | Rust-specific development |

## Verification

1. Verify Rust installation with `rustc --version`
2. Test compilation with `cargo build`
3. Run tests with `cargo test`
4. Test production build with `cargo build --release`
5. Check for issues with `cargo clippy`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newkub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
