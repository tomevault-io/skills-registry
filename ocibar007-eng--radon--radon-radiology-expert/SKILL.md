---
name: radon-radiology-expert
description: Especialista no projeto Radon. Domina a arquitetura, termos médicos radiológicos e integração com a Gemini API. Use when this capability is needed.
metadata:
  author: ocibar007-eng
---

# Radon Radiology Expert ☢️ 🩺

Use esta skill ao implementar novas funcionalidades médicas, refatorar o motor de OCR ou ajustar prompts da Gemini no projeto Radon.

## Arquitetura Chave do Radon
- **OCR Batch:** Pipeline em `src/features/ocr-batch/` com hooks reativos (`useOcrProcessing`).
- **Patient Logic:** Gerenciamento centralizado em `src/services/patient-service.ts`.
- **Configuração:** Definições globais em `src/core/config.ts`.

## Domínio Médico (Radiologia)
- **Mnemonics:** Siga as convenções de terminologia radiológica interna (veja `docs/` para guias).
- **Consistência:** Garanta que "Laudos Prévios" e "Resumos" sigam o vocabulário médico padrão.
- **SLA:** Respeite as prioridades de processamento (Urgente, Normal, etc).

## Integração Gemini API
- **Retentativas:** Sempre use mecanismos de retry com backoff exponencial para evitar 429.
- **Schema Validation:** Use Zod para validar as respostas JSON da IA.
- **Contexto:** Forneça contexto médico rico nos prompts para evitar alucinações em termos técnicos.

## Convenções de Código
- **Tailwind v4:** Use os tokens definidos in `index.css` e `design-tokens.css`.
- **Hooks Reativos:** Prefira separar a lógica de UI em hooks customizados.
- **Testes:** Valide novos fluxos usando as ferramentas em `e2e/`.

## 🛑 Lições Aprendidas (Troubleshooting Recente)
Evite repetir estes erros que já causaram bugs no projeto:

### 1. Agrupamento de PDFs
- **Problema:** A IA às vezes separa páginas de um mesmo exame ou mistura pacientes.
- **Solução:** O prompt deve ser explícito em "agrupar por paciente" e retornar um objeto JSON rigoroso, nunca um array simples se o schema esperar um objeto.

### 2. Configurações e Deployment
- **Vercel:** Sempre verifique se as variáveis de ambiente (especialmente `GEMINI_API_KEY`) estão mapeadas corretamente no `vite.config.ts`.
- **Tailwind v4:** Problemas de "tela branca" geralmente são causados por má configuração do `@tailwindcss/postcss`. Use sempre o plugin correto no `postcss.config.js`.

### 3. Pipeline de Processamento
- **Job Types:** Cuidado ao adicionar novos tipos de processamento (ex: resumo, ocr). Se o `type` estiver `undefined`, o pipeline trava com erro "Unknown job type".
- **Job Types:** Cuidado ao adicionar novos tipos de processamento (ex: resumo, ocr). Se o `type` estiver `undefined`, o pipeline trava com erro "Unknown job type".
- **Paralelismo:** Não deixe o processamento cair para "serial" (um por um) se houver múltiplos arquivos; isso mata a performance da worklist.
- **Armadilha do Código Morto:** No pipeline do OCR, algumas funções parecem não usadas (ex: watchers, eventos de analytics), mas são CRÍTICAS. **Nunca remova código do pipeline sem testar o fluxo end-to-end.** "Se parece inútil, é porque você não entendeu o side-effect."

### 4. O Episódio Pós-Refatoração (Crítico)
- **O que quebrou:** Após uma grande refatoração, as funções automáticas de "Análise de Grupo" (laudo prévio) e "Resumo Clínico" pararam de funcionar. O processamento paralelo também foi perdido, tornando-se serial.
- **Regra de Ouro:** Ao refatorar o `ocr-batch` ou hooks relacionados, **valide explicitamente** se:
    1. O processamento continua paralelo.
    2. A análise automática de `laudo previo` e `resumo previo` é disparada corretamente.
    3. Nenhum `useEffect` essencial foi removido ou teve dependências quebradas.

---

## 📋 POLÍTICA DE STRUCTURED OUTPUT

Ao mexer em resumos/laudos, sempre usar esquema estruturado:

```typescript
// Schema completo para Output Médico
interface StructuredMedicalOutput {
  findings: OrganFinding[];
  impressions: string[];
  recommendations?: string[];
  references?: string[];  // Quando aplicável
  schemaVersion: number;
}
```

---

## 🔒 SAFETY/PHI EM RADIOLOGIA

### Regras HARD
- ❌ Exemplos e logs NUNCA podem conter paciente real
- ❌ Screenshots sem anonimização
- ✅ Usar dados sintéticos: "Test Patient Alpha", "TEST-001"

### Checklist de Prompt Médico
- [ ] Prompt não expõe dados reais?
- [ ] Response é sanitizado antes de mostrar?
- [ ] Logs não contêm PHI?

---

## 📚 CATÁLOGO DE FRAMEWORKS MÉDICOS

| Framework | Uso | Documentação |
|-----------|-----|--------------|
| TNM | Estadiamento tumoral | Ver por órgão |
| MERCURY | Câncer de reto | MRI staging |
| LI-RADS | Lesões hepáticas | ACR guidelines |
| PI-RADS | Próstata | MRI scoring |
| BI-RADS | Mama | Mammography |
| Lung-RADS | Pulmão | Nodule classification |

> ⚠️ **NUNCA invente** scoring. Use apenas os frameworks oficiais.

---

## 📝 CHECKLIST DE TERMINOLOGIA

### Antes de criar/modificar termos médicos:
- [ ] Termo está no `docs/GLOSSARY.md`?
- [ ] Termo segue padrão radiológico brasileiro?
- [ ] Não há ambiguidade com outros termos?
- [ ] Prompt usa vocabulário fechado?

### Termos que causam confusão
| Termo Ambíguo | Termo Correto |
|---------------|---------------|
| "Normal" | "Sem alterações significativas" |
| "Achado" | "Finding" (em código) |
| "Laudo" | "laudo_previo" (classification) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ocibar007-eng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
