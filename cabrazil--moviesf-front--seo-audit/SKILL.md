---
name: seo-audit
description: Analisa projetos web para verificar implementação de boas práticas de SEO e identifica oportunidades de melhoria Use when this capability is needed.
metadata:
  author: cabrazil
---

# SEO Audit Skill

Esta skill realiza uma auditoria completa de SEO em projetos web, analisando técnicas e recursos implementados e identificando oportunidades de melhoria.

## Objetivo

Analisar um projeto web frontend para:
1. Verificar implementação de boas práticas de SEO
2. Identificar recursos já implementados
3. Sugerir melhorias e otimizações
4. Gerar um relatório completo e acionável

## Escopo da Análise

### 1. Estrutura HTML e Semântica

**Verificar:**
- [ ] Uso correto de tags semânticas HTML5 (`<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>`)
- [ ] Hierarquia de headings (H1-H6) - apenas um H1 por página
- [ ] Estrutura lógica e hierárquica dos headings
- [ ] Uso de landmarks ARIA quando apropriado
- [ ] Atributo `lang` na tag `<html>`
- [ ] Estrutura de links internos e navegação

**Analisar:**
- Páginas principais do projeto (index, páginas de conteúdo, etc.)
- Componentes reutilizáveis (Header, Footer, Navigation)
- Templates de páginas dinâmicas

### 2. Meta Tags e Metadados

**Verificar:**
- [ ] Tag `<title>` em todas as páginas (50-60 caracteres ideal)
- [ ] Meta description (150-160 caracteres ideal)
- [ ] Meta viewport para responsividade
- [ ] Meta charset (UTF-8)
- [ ] Canonical URLs
- [ ] Meta robots (index/noindex, follow/nofollow)
- [ ] Open Graph tags (og:title, og:description, og:image, og:url, og:type)
- [ ] Twitter Card tags (twitter:card, twitter:title, twitter:description, twitter:image)
- [ ] Favicon e ícones para diferentes dispositivos

**Analisar:**
- Implementação em páginas estáticas
- Implementação dinâmica (Next.js Metadata API, React Helmet, etc.)
- Consistência entre páginas

### 3. Performance e Core Web Vitals

**Verificar:**
- [ ] Otimização de imagens (formatos modernos: WebP, AVIF)
- [ ] Lazy loading de imagens
- [ ] Atributos `width` e `height` em imagens
- [ ] Atributos `alt` descritivos em imagens
- [ ] Minificação de CSS e JavaScript
- [ ] Code splitting e lazy loading de componentes
- [ ] Preload/Prefetch de recursos críticos
- [ ] Uso de CDN para assets estáticos
- [ ] Compressão (Gzip/Brotli)
- [ ] Caching strategies

**Analisar:**
- Configuração de build (Next.js, Vite, Webpack, etc.)
- Componentes de imagem otimizados
- Estratégias de carregamento

### 4. Conteúdo e Acessibilidade

**Verificar:**
- [ ] Textos alternativos (alt) em imagens
- [ ] Contraste de cores adequado (WCAG AA/AAA)
- [ ] Tamanho de fonte legível
- [ ] Links descritivos (evitar "clique aqui")
- [ ] Formulários com labels apropriados
- [ ] Navegação por teclado funcional
- [ ] ARIA labels quando necessário
- [ ] Skip links para navegação

**Analisar:**
- Componentes interativos
- Formulários
- Navegação

### 5. URLs e Estrutura de Navegação

**Verificar:**
- [ ] URLs amigáveis e descritivas
- [ ] Estrutura de URLs lógica e hierárquica
- [ ] Uso de hífens (não underscores) em URLs
- [ ] URLs em lowercase
- [ ] Breadcrumbs implementados
- [ ] Sitemap.xml gerado
- [ ] Robots.txt configurado
- [ ] Redirecionamentos 301 para URLs antigas

**Analisar:**
- Configuração de rotas
- Arquivos de sitemap e robots.txt
- Estrutura de navegação

### 6. Dados Estruturados (Schema.org)

**Verificar:**
- [ ] JSON-LD implementado
- [ ] Schema apropriado para o tipo de conteúdo:
  - Article/BlogPosting
  - Organization
  - WebSite
  - BreadcrumbList
  - Person/Author
  - Product (se aplicável)
  - Review/Rating (se aplicável)
  - FAQ (se aplicável)
  - HowTo (se aplicável)
- [ ] Validação com Google Rich Results Test

**Analisar:**
- Implementação em páginas relevantes
- Estrutura e completude dos dados

### 7. Mobile-First e Responsividade

**Verificar:**
- [ ] Design responsivo implementado
- [ ] Meta viewport configurado
- [ ] Touch targets adequados (mínimo 48x48px)
- [ ] Texto legível sem zoom
- [ ] Conteúdo adaptado para mobile
- [ ] Velocidade de carregamento mobile

**Analisar:**
- CSS responsivo (media queries)
- Componentes mobile-friendly
- Estratégias de layout

### 8. Segurança e Protocolo

**Verificar:**
- [ ] HTTPS implementado
- [ ] Certificado SSL válido
- [ ] Headers de segurança (CSP, X-Frame-Options, etc.)
- [ ] Links externos com rel="noopener noreferrer"
- [ ] Proteção contra XSS e CSRF

**Analisar:**
- Configuração do servidor/hosting
- Headers HTTP
- Links externos

### 9. Internacionalização (i18n)

**Verificar:**
- [ ] Atributo `lang` correto
- [ ] Tags hreflang para conteúdo multilíngue
- [ ] Estrutura de URLs para diferentes idiomas
- [ ] Conteúdo localizado apropriadamente

**Analisar:**
- Configuração de i18n (se aplicável)
- Estrutura de tradução

### 10. Analytics e Monitoramento

**Verificar:**
- [ ] Google Analytics ou alternativa implementado
- [ ] Google Search Console configurado
- [ ] Event tracking implementado
- [ ] Conversões rastreadas
- [ ] Conformidade com GDPR/LGPD (cookie consent)

**Analisar:**
- Scripts de analytics
- Configuração de tracking
- Políticas de privacidade

## Processo de Execução

### Passo 1: Identificar a Estrutura do Projeto

1. Identificar o framework/biblioteca usado (Next.js, React, Vue, etc.)
2. Localizar arquivos principais:
   - Páginas/rotas principais
   - Componentes de layout (Header, Footer)
   - Configurações (next.config.js, vite.config.ts, etc.)
   - Arquivos públicos (robots.txt, sitemap.xml)
3. Identificar estratégia de renderização (SSR, SSG, CSR)

### Passo 2: Análise Sistemática

Para cada categoria do escopo:

1. **Localizar arquivos relevantes** usando `find_by_name` ou `grep_search`
2. **Examinar implementações** usando `view_file` ou `view_code_item`
3. **Documentar achados**:
   - ✅ Implementado corretamente
   - ⚠️ Implementado parcialmente ou precisa melhorias
   - ❌ Não implementado
4. **Coletar exemplos** de código quando relevante

### Passo 3: Análise de Páginas Específicas

Analisar pelo menos:
- Página inicial (index/home)
- Página de conteúdo/artigo (se aplicável)
- Página de listagem (se aplicável)
- Página de erro 404

### Passo 4: Verificações Técnicas

1. Verificar configurações de build
2. Analisar estrutura de pastas públicas
3. Verificar arquivos de configuração SEO (robots.txt, sitemap)
4. Analisar componentes de metadados

### Passo 5: Gerar Relatório

Criar um relatório markdown completo com:

1. **Resumo Executivo**
   - Score geral de SEO (0-100)
   - Principais pontos fortes
   - Principais oportunidades de melhoria
   - Prioridades de ação

2. **Análise Detalhada por Categoria**
   - Status de cada item verificado
   - Exemplos de código encontrados
   - Problemas identificados
   - Recomendações específicas

3. **Plano de Ação Priorizado**
   - **Prioridade Alta** (impacto crítico em SEO)
   - **Prioridade Média** (melhorias importantes)
   - **Prioridade Baixa** (otimizações incrementais)

4. **Exemplos de Implementação**
   - Código de exemplo para correções sugeridas
   - Links para documentação relevante
   - Best practices específicas do framework usado

5. **Checklist de Implementação**
   - Lista acionável de tarefas
   - Estimativa de esforço (baixo/médio/alto)
   - Impacto esperado

## Formato do Relatório

```markdown
# Relatório de Auditoria SEO
**Projeto:** [Nome do Projeto]
**Data:** [Data da Análise]
**Framework:** [Framework Identificado]

## 📊 Resumo Executivo

### Score Geral de SEO: [X]/100

**Distribuição:**
- ✅ Implementado: X itens (X%)
- ⚠️ Parcial/Melhorias: X itens (X%)
- ❌ Não Implementado: X itens (X%)

### 🎯 Principais Pontos Fortes
1. [Ponto forte 1]
2. [Ponto forte 2]
3. [Ponto forte 3]

### 🚨 Principais Oportunidades
1. [Oportunidade 1]
2. [Oportunidade 2]
3. [Oportunidade 3]

---

## 📋 Análise Detalhada

### 1. Estrutura HTML e Semântica

**Status:** [Score]/10

#### ✅ Implementado
- [Item implementado com exemplo de arquivo]

#### ⚠️ Precisa Melhorias
- [Item com problema e sugestão]

#### ❌ Não Implementado
- [Item faltante e impacto]

**Recomendações:**
1. [Recomendação específica]
2. [Recomendação específica]

---

[Repetir para cada categoria]

---

## 🎯 Plano de Ação Priorizado

### 🔴 Prioridade Alta (Implementar Imediatamente)

#### 1. [Nome da Tarefa]
- **Impacto:** [Descrição do impacto]
- **Esforço:** [Baixo/Médio/Alto]
- **Implementação:**
```[linguagem]
[Código de exemplo]
```
- **Arquivos afetados:** [Lista de arquivos]

---

### 🟡 Prioridade Média (Implementar em Breve)

[Mesmo formato]

---

### 🟢 Prioridade Baixa (Otimizações Futuras)

[Mesmo formato]

---

## 📚 Recursos e Referências

- [Links para documentação relevante]
- [Ferramentas de validação]
- [Best practices do framework]

---

## ✅ Checklist de Implementação

### Estrutura HTML
- [ ] [Tarefa específica] - Esforço: [X] - Impacto: [X]

[Continuar para todas as categorias]
```

## Critérios de Pontuação

Cada categoria vale 10 pontos. Score final é a média ponderada:

- **Estrutura HTML:** Peso 1.0
- **Meta Tags:** Peso 1.5 (crítico)
- **Performance:** Peso 1.5 (crítico)
- **Conteúdo/Acessibilidade:** Peso 1.2
- **URLs/Navegação:** Peso 1.0
- **Dados Estruturados:** Peso 1.3
- **Mobile-First:** Peso 1.5 (crítico)
- **Segurança:** Peso 1.0
- **i18n:** Peso 0.5 (se aplicável)
- **Analytics:** Peso 0.5

**Cálculo por categoria:**
- Implementado corretamente: 10 pontos
- Implementado parcialmente: 5-7 pontos
- Não implementado: 0-3 pontos

## Notas Importantes

1. **Seja específico:** Sempre cite arquivos, linhas de código e exemplos concretos
2. **Seja prático:** Forneça código de exemplo para implementações sugeridas
3. **Considere o contexto:** Adapte recomendações ao framework e tipo de projeto
4. **Priorize impacto:** Foque em mudanças que trarão maior benefício SEO
5. **Seja educativo:** Explique o "porquê" de cada recomendação

## Ferramentas de Validação Sugeridas

Após implementar melhorias, sugerir ao usuário validar com:
- Google Lighthouse
- Google Search Console
- Google Rich Results Test
- PageSpeed Insights
- WebPageTest
- WAVE (Web Accessibility Evaluation Tool)
- Schema.org Validator

## Exemplo de Uso

Quando o usuário solicitar uma auditoria SEO:

1. Confirmar o diretório do projeto
2. Executar análise sistemática seguindo os passos acima
3. Gerar relatório completo em markdown
4. Salvar relatório em `.agent/reports/seo-audit-[data].md`
5. Apresentar resumo executivo ao usuário
6. Oferecer implementar melhorias prioritárias

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cabrazil) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
