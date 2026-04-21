---
name: radon-ops
description: Especialista em Vercel, CI/CD e Troubleshooting de infraestrutura. Use para deploys e correções de build. Use when this capability is needed.
metadata:
  author: ocibar007-eng
---

# Radon Ops 🚀 ⚙️

Use esta skill quando o build quebrar, o deploy falhar ou o Vercel reclamar.

---

## 🔄 ROLLBACK PLAYBOOK (Produção Quebrou)

### Quando Reverter?
- Build passou mas app quebra em runtime
- Bug crítico descoberto em produção
- Usuários reportando erro generalizado

### Como Reverter?
```bash
# Opção 1: Revert do commit
git revert <commit-hash>
git push

# Opção 2: Rollback no Vercel (instantâneo)
# Vercel Dashboard → Deployments → Deployment anterior → "Promote to Production"

# Opção 3: Feature Flag (se implementado)
# Desligar a flag da feature problemática
```

### Depois do Rollback
1. Comunicar equipe
2. Investigar causa (usar skill `radon-debugger`)
3. Criar fix em branch separada
4. Testar E2E antes de re-deploy

---

## 📋 RELEASE CHECKLIST COMPLETO

Antes de avisar que "está pronto":

### Build & Lint
- [ ] `npm run build` passa localmente
- [ ] `npm run lint` passa (zero erros)
- [ ] `npx tsc --noEmit` passa (typecheck)

### Testes
- [ ] `npm run test` passa
- [ ] E2E críticos passam (`npx playwright test`)
- [ ] Smoke test manual funciona

### Ambiente
- [ ] Variáveis novas adicionadas ao `.env.example`
- [ ] Variáveis configuradas no Vercel (se produção)
- [ ] Mapeamento em `vite.config.ts` correto

### Validação Final
- [ ] Preview do Vercel funciona como esperado
- [ ] Console sem erros em produção
- [ ] Funcionalidade principal testada manualmente

---

## 🔍 BUILD BROKE vs RUNTIME BROKE

| Sintoma | Tipo | Onde Olhar |
|---------|------|------------|
| Vercel build falha | Build | Vercel Logs → Build Output |
| "Cannot find module X" | Build | `package.json`, imports |
| TypeError em produção | Runtime | Console, Error tracking |
| "API_KEY undefined" | Runtime | Env vars no Vercel |
| Tela branca | Runtime | `postcss.config.js`, Console |
| 404 em assets | Build | `vite.config.ts`, base path |

### Checklist: Build Broke
```bash
# 1. Replicar localmente
npm ci  # Clean install
npm run build

# 2. Se passou local mas falha no Vercel
# Verificar Node version (package.json engines)
# Verificar env vars (não existem em build)
```

### Checklist: Runtime Broke
```bash
# 1. Verificar Console do browser
F12 → Console

# 2. Verificar Network (API calls)
F12 → Network → Filtrar XHR

# 3. Verificar env vars em runtime
console.log(process.env.API_KEY) // Deve estar definido
```

---

## 🔐 POLÍTICA DE ENV (Environment Variables)

### Regra de Ouro
> **NUNCA mudar strategy de env** (`process.env` vs `import.meta.env`) **sem plano e prova.**

### Onde Configurar
| Ambiente | Onde |
|----------|------|
| Local | `.env` (nunca commitar) |
| Vercel Preview | Vercel → Settings → Environment Variables |
| Vercel Production | Vercel → Settings → Environment Variables (Production) |
| CI | GitHub Secrets / Vercel CLI |

### Mapeamento no Vite
```typescript
// vite.config.ts
define: {
  'process.env.API_KEY': JSON.stringify(process.env.GEMINI_API_KEY),
  // Adicionar TODAS as vars usadas no client
}
```

### Checklist de Env
- [ ] `.env.example` atualizado com novas vars
- [ ] Vite `define` mapeando corretamente
- [ ] Vercel tem as vars configuradas
- [ ] Var não está hardcoded no código

---

## 📈 OBSERVABILIDADE EM PRODUÇÃO

### Logs Estruturados
```typescript
console.info('[Scope] event_name', { 
  traceId,
  ...data,
  timestamp: Date.now()
});
```

### Scopes Padrão
- `[Pipeline]`, `[Grouping]`, `[Gemini]`, `[Storage]`, `[Error]`

### Onde Ver Logs
- **Vercel**: Functions → Logs
- **Browser**: F12 → Console

---

## 🚨 INCIDENTE EM PRODUÇÃO

### Passo 1: Estancar Sangramento
```bash
# Se souber o commit: revert
git revert <hash> && git push

# Se não souber: rollback Vercel
# Dashboard → Promote deployment anterior
```

### Passo 2: Comunicar
- Avisar equipe no chat
- Anotar horário e sintoma

### Passo 3: Investigar (DEPOIS de estabilizar)
- Usar skill `radon-debugger`
- Coletar evidências
- Criar fix em branch

### Passo 4: Fix Seguro
- Testar localmente
- Testar em Preview
- Deploy com monitoramento

---

## 🚩 FEATURE FLAGS (Em Produção)

Para features arriscadas, usar flag:

```typescript
// src/config/feature-flags.ts
export const FEATURE_FLAGS = {
  newPipeline: process.env.NODE_ENV === 'development',
} as const;

// Uso
if (FEATURE_FLAGS.newPipeline) {
  // Código novo
}
```

**Quando desligar:** Se produção quebrar, desligar flag é mais rápido que rollback.

---

## Vercel Troubleshooting (Original)

- **Variáveis de Ambiente:** Se a API falhar em produção, verifique primeiro se `GEMINI_API_KEY` está definida no Vercel Project Settings.
- **Build Logs:** Não adivinhe o erro. Leia o log do Vercel (`Run Command`).
- **Cold Starts:** Se a Serverless Function der timeout, considere aumentar o limite ou otimizar imports.

## CI/CD Breakfix
- **Lockfile:** Se o CI quebrar na instalação, verifique diferenças entre `package-lock.json` local e remoto.
- **Tipagem:** Nunca dê bypass no `tsc` (`ts-ignore`). Corrija a tipagem ou use `unknown` + cast seguro.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ocibar007-eng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
