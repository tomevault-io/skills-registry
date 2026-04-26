---
name: code-quality-checks
description: Enforce code quality with SonarCloud, CheckStyle, SpotBugs, and maintain quality gates Use when this capability is needed.
metadata:
  author: hack23
---

# Code Quality Checks Skill

## Purpose
Maintain high code quality through automated static analysis and quality gates.

## When to Use
- ✅ Before committing code
- ✅ In CI/CD pipeline
- ✅ During code reviews
- ✅ Regular quality audits

## SonarCloud Quality Gates
```yaml
# sonar-project.properties
sonar.projectKey=Hack23_cia
sonar.organization=hack23
sonar.qualitygate.wait=true

# Quality Gate Thresholds
sonar.coverage.minimum=80
sonar.duplications.maximum=3
sonar.maintainability.rating=A
sonar.reliability.rating=A
sonar.security.rating=A
```

## CheckStyle Configuration
```xml
<module name="Checker">
    <module name="LineLength">
        <property name="max" value="120"/>
    </module>
    <module name="TreeWalker">
        <module name="NeedBraces"/>
        <module name="AvoidStarImport"/>
        <module name="UnusedImports"/>
    </module>
</module>
```

## Maven Quality Plugin
```xml
<plugin>
    <groupId>org.sonarsource.scanner.maven</groupId>
    <artifactId>sonar-maven-plugin</artifactId>
    <version>3.10.0.2594</version>
</plugin>
```

## References
- SonarCloud: https://sonarcloud.io/
- CheckStyle: https://checkstyle.org/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
