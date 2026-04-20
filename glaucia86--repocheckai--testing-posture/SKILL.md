---
name: testing-posture
description: Faz análise de postura de testes (presença, estrutura, camadas). Use when this capability is needed.
metadata:
  author: glaucia86
---

# Skill: Testing Posture

## Quando usar
Quando a análise precisa determinar:
- Se existem testes
- Estrutura de teste
- Falta de cobertura por tipo

## Escopo da skill
Inclui:
- Presença de diretórios de teste
- Framework detectado
- Gaps (unit vs integration)

Não inclui:
- Execução de testes reais

## Entradas esperadas
- Pastas de teste
- Scripts

## Como analisar
1) Verificar presença de arquivos de teste.
2) Detectar frameworks (Jest/Vitest/etc.)
3) Determinar gaps por heurística

## Saída esperada
Achados com:
- Severidade
- Recomendações

## Regras obrigatórias
- Evidência bestand

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/glaucia86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
