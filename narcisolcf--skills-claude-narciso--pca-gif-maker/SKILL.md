---
name: pca-gif-maker
description: Gera GIFs animados programaticamente usando Python (Pillow) para o projeto PCA Camocim. Cria animacoes de texto e badges para documentacao. Use when this capability is needed.
metadata:
  author: narcisolcf
---

# Criador de GIFs PCA

## Visão Geral
Esta habilidade permite criar imagens animadas (.gif) através de código. É útil para criar assets visuais leves, banners de notificação ou elementos de UI animados para a documentação do projeto PCA, sem precisar de software de edição externo.

## Funcionalidades
Utiliza scripts Python localizados na pasta `core/`:
- **Texto Animado:** Cria textos que deslizam, piscam ou mudam de cor.
- **Formas Dinâmicas:** Gera fundos e elementos geométricos em movimento.
- **Otimização:** Saída otimizada para web e READMEs.

## Como Usar

### 1. Gerar um GIF Simples
O usuário descreve a animação desejada. O Claude escreve um script que importa os módulos de `core`.

**Prompt Exemplo:**
"Crie um GIF de 500x100px com fundo azul e o texto 'PCA APROVADO' piscando em branco."

**Raciocínio do Modelo:**
1. Importar `GifBuilder` de `core.gif_builder`.
2. Configurar frames e duração.
3. Usar `FrameComposer` para desenhar texto em cada frame.
4. Salvar como `aprovado.gif`.

### 2. Execução
O código Python gerado deve ser executado no ambiente de análise do Claude.

## Requisitos
- Dependências Python: `Pillow` (definido em requirements.txt).
- A pasta `core/` deve estar acessível no path de execução.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/narcisolcf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
