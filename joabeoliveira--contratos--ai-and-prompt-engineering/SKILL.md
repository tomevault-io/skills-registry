---
name: ai-prompt-engineering
description: Padrões de engenharia de prompts e design de interfaces para integração com LLMs. Use when this capability is needed.
metadata:
  author: joabeoliveira
---

# Skill: Especialista em IA & LLM Engineering

## Goal
Integrar inteligência artificial generativa de forma eficaz, segura e com uma experiência de usuário (UX) excepcional.

## Instructions

### 1. Engenharia de Prompts (System Prompts)
- **Estruturação**: Definir papéis (Persona), Contexto, Tarefa e Formato de Saída esperado.
- **Few-Shot Prompting**: Fornecer exemplos de entrada-saída dentro do prompt para melhorar a precisão.
- **Saída Estruturada**: Solicitar respostas em JSON sempre que for processar o dado programaticamente.

### 2. UI/UX para IA
- **Streaming**: Sempre que possível, usar streaming de texto para reduzir a latência percebida pelo usuário.
- **Estados de "Pensamento"**: Mostrar indicadores visuais (ex: skeletons ou textos de status) enquanto a IA está processando.
- **Feedback**: Oferecer botões para o usuário avaliar a resposta da IA (Like/Dislike).

### 3. Confiabilidade e Segurança
- **Filtros de Saída**: Validar a resposta da IA (ex: com Zod ou Regex) antes de inseri-la no banco de dados.
- **Tratamento de Alucinações**: Incluir instruções no system prompt para a IA dizer "não sei" caso não tenha a informação.
- **Context Window**: Gerenciar o histórico de mensagens para não exceder o limite de tokens do modelo.

## Constraints
- **CRITICAL**: Nunca enviar dados pessoais sensíveis identificáveis (PII) para APIs de LLM externas sem anonimização.
- **Custos**: Monitorar o uso de tokens e evitar chamadas desnecessárias ou redundantes à API.
- **Fallback**: Sempre ter um plano de erro caso a API da IA esteja indisponível.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joabeoliveira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
