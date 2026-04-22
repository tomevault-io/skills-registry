---
name: api-mocking-skill
description: Scaffold API mocking infrastructure using Mountebank and Testcontainers for reliable integration testing. Use when this capability is needed.
metadata:
  author: godspeedai
---

# API Mocking Skill

This skill automates the setup of API mocking infrastructure. It leverages `Mountebank` (for over-the-wire mocks) and `Testcontainers` (for ephemeral environment management) to create robust, hermetic integration tests.

## Commands

| Command                 | Description                                                       | Usage                   |
| :---------------------- | :---------------------------------------------------------------- | :---------------------- |
| `/vibepro.mock.init`    | Initialize mocking infrastructure (fixtures, dependencies check). | `/vibepro.mock.init`    |
| `/vibepro.mock.example` | Generate a reference implementation of a mock-backed test.        | `/vibepro.mock.example` |

## Usage Examples

### Initialize Mocking Support

```bash
/vibepro.mock.init
```

### Create Example Test

```bash
# This creates tests/integration/test_mock_example.py
/vibepro.mock.example
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/godspeedai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
