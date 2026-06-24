---
name: agent-configuration
description: Configure agent specs, inheritance, skills, and tools in .parac/ workspace. Use when setting up or customizing agents. Use when this capability is needed.
metadata:
  author: ibiface-tech
---

# Agent Configuration Skill

## When to use this skill

Use when:

- Creating new agent specifications
- Setting up agent inheritance
- Configuring agent skills and tools
- Customizing agent behavior
- Troubleshooting agent configuration

## Agent Spec Structure

```yaml
# .parac/agents/specs/my-agent.yaml
name: my-agent
model: gpt-4
temperature: 0.7
max_tokens: 2000

system_prompt: |
  You are an expert software developer.
  You write clean, maintainable code.

skills:
  - code-generation
  - debugging
  - testing

tools:
  - file-read
  - file-write
  - shell-execute

metadata:
  author: team
  version: "1.0.0"
```

## Inheritance Pattern

```yaml
# Base agent
# .parac/agents/specs/base-coder.yaml
name: base-coder
model: gpt-4
temperature: 0.7
system_prompt: "You are a software developer."
skills:
  - code-generation

# Specialized agent (inherits from base)
# .parac/agents/specs/python-coder.yaml
name: python-coder
inherits: base-coder  # Inherits all properties from base-coder
temperature: 0.5  # Override: lower temperature for Python
skills:
  - code-generation  # Inherited
  - python-specific  # Added
system_prompt: |
  You are a Python expert.
  Follow PEP 8 standards.
```

## Configuration Best Practices

1. **Use inheritance for common configurations**
2. **Keep system prompts focused**
3. **Test configurations before deployment**
4. **Version control all agent specs**
5. **Document custom configurations**

## Resources

- Agent Specs: `.parac/agents/specs/`
- Template: `content/templates/.parac-template/agents/specs/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ibiface-tech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
