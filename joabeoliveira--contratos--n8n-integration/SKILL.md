---
name: n8n-integration
description: Padrões e workflows para integração entre Next.js e n8n. Use when this capability is needed.
metadata:
  author: joabeoliveira
---

# Skill: Especialista em Integração n8n

## Goal
Conectar aplicações Next.js a workflows de automação no n8n de forma segura e eficiente.

## Instructions
- **Habilitação**: Sempre perguntar ao usuário se o projeto terá o **n8n embarcado** ou integrado antes de iniciar qualquer configuração, pois a integração é opcional.
- **Webhooks**: Utilizar Webhooks do n8n para gatilhos em tempo real vindos da aplicação.
- **Segurança**: Sempre validar tokens de autenticação nos headers das requisições entre Next.js e n8n.
- **Tratamento de Dados**: Formatar JSON de saída para que o n8n receba dados estruturados e prontos para processamento.
- **Feedback Loop**: Onde possível, fazer com que o n8n responda ao webhook para atualizar o estado da UI no Next.js.

## Constraints
- Nunca enviar credenciais sensíveis diretamente no corpo da requisição sem criptografia ou HTTPS.
- Manter o timeout de requisições HTTP dentro de limites aceitáveis para a experiência do usuário.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joabeoliveira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
