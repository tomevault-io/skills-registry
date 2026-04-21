---
name: agent-os-foundry-dev
description: Skill especializada para desenvolvimento de módulos Foundry VTT, focada em interações com a API, Hooks e sistema PF2e. Use when this capability is needed.
metadata:
  author: questionwho42-jpg
---

# Skill: Desenvolvedor Foundry VTT

Esta skill capacita o agente a escrever código eficiente e correto para o Foundry Virtual Tabletop, especificamente para o módulo "Corvo do Poço".

## Quando usar esta skill:

- Ao criar ou modificar scripts JavaScript (`.js`) no diretório `scripts/`.
- Ao criar classes de Application/FormApplication para interfaces.
- Ao registrar Hooks (`init`, `ready`, etc.).
- Ao manipular dados de Atores ou Itens do PF2e via código.
- Ao atualizar o `module.json`.

## Capacidades Principais

### 1. Registro de Configurações (Settings)

O agente sabe registrar configurações de módulo:

```javascript
game.settings.register("corvo-do-poco", "minhaConfig", {
  name: "Nome da Config",
  hint: "Explicação...",
  scope: "world",
  config: true,
  type: Boolean,
  default: false,
});
```

### 2. Manipulação de Hooks

O agente sabe onde e como registrar hooks:

```javascript
Hooks.once("init", () => {
  console.log("Corvo do Poço | Inicializando...");
  // Registro de classes, settings, etc.
});
```

### 3. Integração com Handlebars

O agente pode criar templates Handlebars `.hbs` e renderizá-los em aplicações.

### 4. Links com Regras

Esta skill deve ser usada em conjunto com:

- `agent-os-foundry-module-structure.md` para saber onde colocar arquivos.
- `agent-os-foundry-api-best-practices.md` para garantir qualidade de código.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/questionwho42-jpg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
