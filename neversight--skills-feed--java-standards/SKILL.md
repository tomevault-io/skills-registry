---
name: java-standards
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# Java Standards

Java service standards for Bitso projects.

## When to use this skill

- Creating new Java services or modules
- Understanding project organization patterns
- Reviewing Java code for standards compliance
- Configuring MapStruct for object mapping
- Verifying builds after code changes

## Skill Contents

### Sections

- [When to use this skill](#when-to-use-this-skill) (L24-L31)
- [Tech Stack](#tech-stack) (L53-L66)
- [Project Organization](#project-organization) (L67-L82)
- [Build Verification](#build-verification) (L83-L96)
- [References](#references) (L97-L104)
- [Related Rules](#related-rules) (L105-L110)
- [Related Skills](#related-skills) (L111-L117)

### Available Resources

**📚 references/** - Detailed documentation
- [build verification](references/build-verification.md)
- [code review](references/code-review.md)
- [services](references/services.md)

---

## Tech Stack

| Component | Version | Notes |
|-----------|---------|-------|
| **Java** | 21 (LTS) | Current LTS version |
| **Gradle** | **9.2.1** | Recommended for all projects |
| **Spring Boot** | **3.5.9** | Latest (min 3.5.9) - preparing for Spring Boot 4 |
| **Database Access** | jOOQ | For accessing PostgreSQL |
| **Databases** | PostgreSQL, Redis | Primary data stores |
| **Inter-service Communication** | gRPC | Standard protocol |
| **Object Mapping** | MapStruct | For DTO/domain mapping |

For Spring Boot upgrades, use `/upgrade-to-recommended-versions`.

## Project Organization

Projects should be organized with domain-based modules:

```text
root-project/
├── build.gradle
├── settings.gradle
├── docs/                    # Documentation
├── bitso-libs/              # Library modules
│   ├── <subdomain>/         # Domain logic
│   └── <subdomain-proto>/   # Protobuf definitions
└── bitso-services/          # Service modules
    └── <domain>/            # Spring Boot application
```

## Build Verification

After updating Java or Groovy code, verify changes:

```bash
# Run tests to verify changes
./gradlew test 2>&1 | grep -E "FAILED|Error" || echo "All tests passed"

# Or run full build with tests
./gradlew build 2>&1 | grep -E "FAILED|Error" || echo "Build successful"
```

If problems are found, fix them before committing.

## References

| Reference | Description |
|-----------|-------------|
| [references/services.md](references/services.md) | Tech stack, project structure, dependency management, MapStruct |
| [references/code-review.md](references/code-review.md) | Java 21 standards, coding style, var keyword usage |
| [references/build-verification.md](references/build-verification.md) | Build commands and verification practices |

## Related Rules

- `.cursor/rules/java-services-standards.mdc` - Full service standards
- `.cursor/rules/java-code-review-standards.mdc` - Code review guidelines
- `.cursor/rules/java-run-build-after-changes.mdc` - Build verification

## Related Skills

| Skill | Purpose |
|-------|---------|
| [gradle-standards](../gradle-standards/SKILL.md) | Gradle configuration |
| [grpc-standards](../grpc-standards/SKILL.md) | gRPC service implementation |
| [database-integration](../database-integration/SKILL.md) | jOOQ and Flyway |
<!-- AUTO-GENERATED FILE - DO NOT EDIT DIRECTLY -->
<!-- Source: bitsoex/ai-code-instructions → java/skills/java-standards/SKILL.md -->
<!-- To modify, edit the source file and run the distribution workflow -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
