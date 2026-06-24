---
name: jest-snapshot-testing
description: Escreve snapshot tests com Jest para outputs estáveis e reviewáveis. Use apenas para saídas determinísticas e legíveis. Não use para outputs grandes, não-determinísticos ou lógica de negócio. Use when this capability is needed.
metadata:
  author: wendeus0
---

# Objetivo

Escrever testes de snapshot em Jest para capturar outputs estáveis, pequenos e reviewáveis como código, preferindo snapshots inline para facilitar review no diff.

# Quando usar

- Feature produz output canônico e determinístico: render estável, serialização, AST, config gerada, CLI output estruturado.
- Invocada por `test-design` quando o output tem forma estável.
- Output cabe em até 100 linhas.
- Conteúdo pode ser sanitizado para eliminar não-determinismo (timestamps, IDs aleatórios).

# Quando NÃO usar

- Outputs grandes (>100 linhas) — refatore o teste ou use assertions estruturais.
- Lógica de negócio — use unit test com expectativas explícitas.
- UI interativa — use `storybook-interaction-testing`.
- Dados dinâmicos inerentes (timestamp live, UUID real, randomness) sem como sanitizar.

# Leia antes de agir

1. Código-alvo que produz o output.
2. `features/<feature>/SPEC.md` para entender o que o output representa.
3. Snapshots existentes no módulo, se houver, para manter consistência.

# Processo

1. Identificar o output exato a capturar (retorno de função, render, serialização).
2. Sanitizar não-determinismo: mock de `Date`, `crypto.randomUUID`, `Math.random` quando relevante.
3. Escrever o teste com `expect(output).toMatchInlineSnapshot()`.
4. Rodar uma vez para gerar o snapshot.
5. Inspecionar o snapshot gerado — ele vai para o diff do commit.
6. Confirmar que o snapshot tem no máximo 100 linhas e é legível.

# Anti-padrões

| Anti-padrão | Por quê | O que fazer |
|-------------|---------|-------------|
| Snapshot >100 linhas | Review vira impossível, mudanças passam despercebidas | Quebrar em asserções específicas ou testar estrutura |
| Timestamps ou IDs aleatórios no snapshot | Flakiness, commit ruidoso | Sanitize via mock antes de capturar |
| Snapshot como único teste do componente | Cobre forma, não comportamento | Complementar com testes de interação/lógica |
| Commit de snapshot sem ler o diff | Snapshot deixa de ser gate | Sempre inspecionar o snapshot (inline no teste, ou arquivo `.snap` quando externo) antes de commitar |

# Regras obrigatórias

- Preferir `toMatchInlineSnapshot` sobre `toMatchSnapshot` externo.
- Sempre revisar o snapshot antes de commitar — snapshot não revisado é dívida disfarçada.
- Máximo 100 linhas por snapshot; acima disso, refatore o teste.
- Sanitizar fontes de não-determinismo antes de capturar.
- Nomeie o teste por comportamento: `should render <output> when <estado>`.

# Stack recomendada

| Ferramenta | Uso |
|------------|-----|
| Jest | Runner e matchers |
| `@testing-library/react` | Render de componentes |
| `jest-serializer-*` | Formatação legível de snapshots |

Versões pinadas pelo `package.json` do projeto. API de snapshot é estável há vários majors — sem floor obrigatório aqui.

# Saída final esperada

- Testes com `toMatchInlineSnapshot` preenchido após execução inicial.
- Mocks de fontes não-determinísticas onde necessário.
- **status final**:
  - `JEST_SNAPSHOT_READY` — snapshots gerados, revisados, dentro do limite.
  - `JEST_SNAPSHOT_BLOCKED` — output instável ou grande demais; abordagem precisa mudar.

# Restrições

- Não commitar snapshot sem review humano.
- Não usar snapshot para testar lógica de negócio.
- Não capturar dados sensíveis no snapshot.
- Não invocar outras skills — esta é atômica.

---
> Source: [wendeus0/AI-dotfiles](https://github.com/wendeus0/AI-dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
