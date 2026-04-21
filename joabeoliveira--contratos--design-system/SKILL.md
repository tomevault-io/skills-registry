---
name: design-system-gov-tech
description: Guia de estilos, cores e componentes premium para interfaces governamentais e empresariais. Use when this capability is needed.
metadata:
  author: joabeoliveira
---

# Skill: Mestre de Design Gov-Tech

## Goal
Garantir consistência visual, acessibilidade e credibilidade profissional em todos os módulos do sistema.

## Instructions
- **Identificação de Padrão**: Determinar se a UI necessária é um Dashboard, Formulário de Entrada ou Tabela de Dados.
- **Paleta de Cores**: Aplicar a cor primária #1E40AF (Azul Profundo) para ações e a escala `Slate` para a estrutura.
- **Uso de Componentes**: Utilizar componentes base do **Shadcn/UI**. Se um componente não existir, construa-o usando primitivos do Tailwind.
- **Tipografia**: Forçar o uso de **Geist Sans** ou **Inter** para garantir legibilidade em interfaces densas em dados.
- **Responsive Design**: Todos os layouts devem ser mobile-first e totalmente responsivos.

## Constraints
- Não criar componentes de UI customizados se houver um equivalente **Shadcn/UI** no projeto.
- Manter espaçamentos padrão do Tailwind (ex: `p-4`, `gap-6`) para evitar débito visual.
- **CRITICAL**: Todos os componentes devem suportar Dark/Light modes usando a classe utilitária `dark:`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joabeoliveira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
