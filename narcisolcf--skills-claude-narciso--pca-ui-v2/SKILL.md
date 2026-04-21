---
name: pca-ui-v2
description: Gera prototipos React (SPA) visuais completos num unico ficheiro HTML para o PCA Camocim. Versao 2 com shadcn/ui. Use when this capability is needed.
metadata:
  author: narcisolcf
---

# Construtor de UI PCA Camocim (V2)

## Visão Geral
Esta habilidade permite criar mini-aplicações React visualizáveis diretamente na interface do Claude. É usada para validar ideias visuais (ex: novos formulários de contratação) sem sujar o código principal.

## Como Funciona
A skill executa scripts shell que:
1.  Inicializam um projeto React temporário com `shadcn/ui`.
2.  Permitem ao Claude escrever o código dos componentes.
3.  Empacotam tudo num único ficheiro `bundle.html`.

## Instruções de Uso

### 1. Criar um Protótipo
Quando o utilizador pedir "Crie um protótipo visual de X":
1.  Execute `scripts/init-artifact.sh` para preparar o ambiente.
2.  Escreva o código React (App.tsx, componentes).
3.  Execute `scripts/bundle-artifact.sh` para gerar o HTML final.

## Requisitos Técnicos
- O ambiente deve suportar execução de Bash scripts.
- O ficheiro `shadcn-components.tar.gz` deve estar presente em `scripts/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/narcisolcf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
