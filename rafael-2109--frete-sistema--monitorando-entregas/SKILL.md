---
name: monitorando-entregas
description: >- Use when this capability is needed.
metadata:
  author: rafael-2109
---

# Monitorando Entregas

Skill para consultar status de entregas, canhotos, devoluções e agendamentos pós-faturamento.

---

## Índice

1. [Quando NÃO Usar Esta Skill](#quando-não-usar-esta-skill)
2. [DECISION TREE - Qual Script Usar?](#decision-tree---qual-script-usar)
3. [Regras CRÍTICAS (Anti-Alucinação)](#regras-críticas-anti-alucinação)
4. [Scripts Disponíveis](#scripts-disponíveis)
5. [Tratamento de Resultados](#tratamento-de-resultados)
6. [Referência Cruzada](#referência-cruzada)
7. [References](#references)

---

## Quando NÃO Usar Esta Skill

| Situação | Usar em vez desta |
|----------|-------------------|
| Pedido ainda não faturado | **gerindo-expedicao** |
| Rastrear NF/PO/pagamento no Odoo | **rastreando-odoo** |
| Criar separação de pedido | **gerindo-expedicao** |
| Análise completa da carteira (P1-P7) | **analista-carteira** (subagente) |
| Consultas SQL analíticas complexas | **consultando-sql** |

---

## DECISION TREE - Qual Script Usar?

### Scripts EXISTENTES (apenas estes 3)

⚠️ **IMPORTANTE**: Esta skill possui EXATAMENTE 3 scripts. NÃO tente executar scripts que não estão listados abaixo.

| Script | Arquivo | Função |
|--------|---------|--------|
| Status de entregas | `consultando_status_entrega.py` | Status, datas, canhotos, filtros por NF/cliente/período |
| Devoluções básicas | `consultando_devolucoes.py` | NFDs com ocorrências (abertas, por NF/cliente) |
| Devoluções detalhadas | `consultando_devolucoes_detalhadas.py` | 4 modos: por cliente, produto, ranking, custo |

### Mapeamento Rápido

| Se a pergunta menciona... | Use este script | Com estes parâmetros |
|---------------------------|-----------------|----------------------|
| **Status de NF específica** | `consultando_status_entrega.py` | `--nf 12345` |
| **Status por cliente/CNPJ** | `consultando_status_entrega.py` | `--cliente "Atacadao"` ou `--cnpj 123...` |
| **Data de embarque** ("que dia saiu?", "quando embarcou?") | `consultando_status_entrega.py` | `--nf 12345` → campo `data_embarque` |
| **Data de faturamento** ("quando faturou?", "data da NF") | `consultando_status_entrega.py` | `--nf 12345` → campo `data_faturamento` |
| **Data de entrega** ("quando chegou?", "foi entregue quando?") | `consultando_status_entrega.py` | `--nf 12345` → campo `data_hora_entrega_realizada` |
| **Entregas pendentes** | `consultando_status_entrega.py` | `--pendentes` |
| **Entregas no CD** | `consultando_status_entrega.py` | `--no-cd` |
| **Entregas reagendadas** | `consultando_status_entrega.py` | `--reagendadas` |
| **Entregas entregues** (período) | `consultando_status_entrega.py` | `--entregues --de 2025-01-01 --ate 2025-01-31` |
| **Canhoto de NF** | `consultando_status_entrega.py` | `--nf 12345` → campo `canhoto_arquivo` |
| **Canhotos pendentes** | `consultando_status_entrega.py` | `--entregues` → filtrar onde `canhoto_arquivo` é null |
| **Entregas com problema** | `consultando_status_entrega.py` | `--problemas` |
| **Devoluções abertas** | `consultando_devolucoes.py` | `--abertas` |
| **Devolução de NF específica** | `consultando_devolucoes.py` | `--nf 12345` |
| **Devoluções de cliente** | `consultando_devolucoes_detalhadas.py` | `--cliente "Sendas"` |
| **Produtos mais devolvidos** | `consultando_devolucoes_detalhadas.py` | `--ranking` |
| **Custo de devoluções** | `consultando_devolucoes_detalhadas.py` | `--custo` |
| **Devoluções de produto** | `consultando_devolucoes_detalhadas.py` | `--produto "palmito"` |

### Regras de Decisão (em ordem de prioridade)

1. **Se pergunta sobre STATUS de entrega ou DATAS (embarque/faturamento/entrega):**
   → Use `consultando_status_entrega.py`

2. **Se pergunta sobre CANHOTO:**
   → Use `consultando_status_entrega.py` (campo `canhoto_arquivo` no retorno)
   → Se `canhoto_arquivo` é null → "sem canhoto registrado"
   → Se `canhoto_arquivo` tem valor → "canhoto disponível"

3. **Se pergunta sobre DEVOLUÇÃO (ocorrência/status/NFD):**
   → Use `consultando_devolucoes.py`

4. **Se pergunta sobre ANÁLISE de devoluções (ranking, custo, histórico por cliente/produto):**
   → Use `consultando_devolucoes_detalhadas.py` com o modo adequado

5. **Se pergunta sobre AGENDAMENTO:**
   → Dados de agendamento estão em `consultando_status_entrega.py` (campos `data_agenda`, `reagendar`, `motivo_reagendamento`)
   → Para agendamentos detalhados, usar `consultando-sql` com tabela `agendamentos_entrega`

6. **Se pergunta sobre PROBLEMAS genéricos:**
   → Use `consultando_status_entrega.py --problemas`
   → Inclui: nf_cd=True OU reagendar=True

---

## Regras CRÍTICAS (Anti-Alucinação)

### R1: FIDELIDADE AO OUTPUT DOS SCRIPTS

```
OBRIGATÓRIO:
- Reportar EXATAMENTE os valores retornados pelo script
- Usar EXATAMENTE os nomes de campos do JSON de retorno
- Citar numeros (total, valores) do campo "resumo" quando disponivel
- Formatar datas como DD/MM/YYYY (converter de YYYY-MM-DD do script)
- Formatar valores monetarios como R$ X.XXX,XX

PROIBIDO:
- Inventar dados que NAO estao no output do script
- Inferir status a partir de campos booleanos isolados (usar status_finalizacao)
- Arredondar ou estimar valores — usar exatamente o que o script retorna
- Adicionar contexto ou explicacao que nao tem base nos dados retornados
```

### R2: status_finalizacao — Valores Válidos

| Valor | Significado |
|-------|-------------|
| `NULL` | **Em andamento/pendente** - NF ainda não finalizada |
| `Entregue` | NF entregue com sucesso ao cliente |
| `Cancelada` | Entrega cancelada (NF não será entregue) |
| `Devolvida` | Cliente devolveu a mercadoria |
| `Troca de NF` | NF substituída por outra (ver campo `nova_nf`) |
| `Sinistro` | Perda/extravio da mercadoria |
| `nao_finalizado` | NF saiu do monitoramento sem conclusão |

### R3: Fórmulas CORRETAS

| Consulta | Fórmula | Flag no script |
|----------|---------|----------------|
| Entregas pendentes | `status_finalizacao IS NULL` | `--pendentes` |
| Entregas entregues | `status_finalizacao = 'Entregue'` | `--entregues` |
| Entregas devolvidas | `status_finalizacao = 'Devolvida' OR teve_devolucao = True` | — |
| Entregas com problema | `nf_cd = True OR reagendar = True` | `--problemas` |
| Com canhoto | `canhoto_arquivo IS NOT NULL` | — |

### R4: O Agente PODE Afirmar

- Status atual da entrega (baseado em `status_finalizacao`)
- Se tem canhoto (baseado em `canhoto_arquivo IS NOT NULL`)
- Data de entrega (baseado em `data_hora_entrega_realizada`)
- Se está no CD (baseado em `nf_cd = True`)
- Se precisa reagendar (baseado em `reagendar = True`)
- Ranking de produtos devolvidos (baseado no output de `--ranking`)
- Custo de devoluções (baseado no output de `--custo`)

### R5: O Agente NÃO PODE Inventar

- Lead time ou prazo de entrega se não calculado pelo script
- Motivo de devolução sem consultar `ocorrencia_devolucao` (usar `consultando_devolucoes.py`)
- Previsão de entrega sem dados no campo `data_entrega_prevista`
- Custo de devolução sem executar `consultando_devolucoes_detalhadas.py --custo`
- Status de agendamento detalhado sem consultar tabela `agendamentos_entrega`

### R6: Campos com Semântica Especial

| Campo | Descrição | ⚠️ Cuidado |
|-------|-----------|------------|
| `entregue` | Boolean - True quando `status_finalizacao='Entregue'` | NÃO usar isoladamente para filtrar pendentes |
| `nf_cd` | Boolean - NF fisicamente no CD | Pode ser NF que nunca saiu OU que voltou |
| `reagendar` | Boolean - cliente solicitou reagendamento | — |
| `teve_devolucao` | Boolean - houve devolução (mesmo parcial) | — |
| `nova_nf` | NF substituta | Só presente quando `status_finalizacao='Troca de NF'` |
| `canhoto_arquivo` | Caminho do arquivo do canhoto | null = sem canhoto, valor = tem canhoto |

---

## Scripts Disponíveis

### 1. consultando_status_entrega.py

Consulta status de entregas com vários filtros. Script principal — cobre status, datas, canhotos e agendamentos básicos.

```bash
source .venv/bin/activate && python .claude/skills/monitorando-entregas/scripts/consultando_status_entrega.py [opções]
```

**Parâmetros:**

| Param | Obrig | Descrição |
|-------|-------|-----------|
| `--nf` | Não | Número da NF (busca parcial ILIKE) |
| `--cliente` | Não | Nome do cliente (busca parcial ILIKE) |
| `--cnpj` | Não | CNPJ do cliente |
| `--transportadora` | Não | Transportadora |
| `--pendentes` | Não | Apenas entregas pendentes (status_finalizacao IS NULL) |
| `--entregues` | Não | Apenas entregas entregues |
| `--no-cd` | Não | Apenas NFs no CD (nf_cd=True) |
| `--reagendadas` | Não | Apenas reagendadas (reagendar=True) |
| `--problemas` | Não | Com problema (nf_cd=True OR reagendar=True) |
| `--de` | Não | Data inicial (YYYY-MM-DD) |
| `--ate` | Não | Data final (YYYY-MM-DD) |
| `--limite` | Não | Máximo de registros (default: 50) |
| `--formato` | Não | json ou tabela (default: json) |

**Retorno esperado:**
```json
{
  "sucesso": true,
  "total": 15,
  "exibindo": 50,
  "filtros_aplicados": {"pendentes": true},
  "entregas": [
    {
      "id": 1234,
      "numero_nf": "144533",
      "cliente": "ATACADAO SA",
      "cnpj_cliente": "45.543.915/0039-00",
      "transportadora": "BRASPRESS",
      "municipio": "SAO PAULO",
      "uf": "SP",
      "valor_nf": 12345.67,
      "data_faturamento": "2025-01-15",
      "data_embarque": "2025-01-16",
      "data_entrega_prevista": "2025-01-20",
      "data_hora_entrega_realizada": null,
      "status_finalizacao": null,
      "entregue": false,
      "nf_cd": false,
      "reagendar": false,
      "motivo_reagendamento": null,
      "data_agenda": null,
      "teve_devolucao": false,
      "canhoto_arquivo": null,
      "nova_nf": null
    }
  ]
}
```

### 2. consultando_devolucoes.py

Consulta devoluções (NFDs) com ocorrências relacionadas.

```bash
source .venv/bin/activate && python .claude/skills/monitorando-entregas/scripts/consultando_devolucoes.py [opções]
```

**Parâmetros:**

| Param | Obrig | Descrição |
|-------|-------|-----------|
| `--nf` | Não | Número da NF original |
| `--nfd` | Não | Número da NF de devolução |
| `--abertas` | Não | Apenas ocorrências abertas (ABERTA, EM_ANALISE, AGUARDANDO_RETORNO) |
| `--cliente` | Não | Nome do cliente/emitente |
| `--de` | Não | Data inicial (YYYY-MM-DD) |
| `--ate` | Não | Data final (YYYY-MM-DD) |
| `--limite` | Não | Máximo de registros (default: 50) |

**Retorno esperado:**
```json
{
  "sucesso": true,
  "total": 30,
  "exibindo": 30,
  "devolucoes": [
    {
      "id": 456,
      "numero_nfd": "67890",
      "numero_nf_venda": "12345",
      "motivo": "AVARIA",
      "valor_total": 1234.56,
      "nome_emitente": "ATACADAO SA",
      "status_nfd": "REGISTRADA",
      "status_ocorrencia": "ABERTA",
      "categoria": "QUALIDADE",
      "responsavel": "QUALIDADE"
    }
  ]
}
```

### 3. consultando_devolucoes_detalhadas.py

Consulta devoluções detalhadas com 4 modos mutuamente exclusivos.

```bash
source .venv/bin/activate && python .claude/skills/monitorando-entregas/scripts/consultando_devolucoes_detalhadas.py [opções]
```

**Parâmetros de modo (UM obrigatório, mutuamente exclusivos):**

| Param | Descrição |
|-------|-----------|
| `--cliente "Nome"` | Histórico de devoluções por cliente |
| `--produto "nome"` | Produtos devolvidos (ILIKE) |
| `--ranking` | Top N produtos mais devolvidos |
| `--custo` | Custo total de devoluções (via despesas_extras) |

**Parâmetros gerais:**

| Param | Descrição |
|-------|-----------|
| `--de` | Data início (YYYY-MM-DD) |
| `--ate` | Data fim (YYYY-MM-DD) |
| `--limite` | Max resultados (default: 50) |
| `--incluir-custo` | Incluir custo (apenas com --cliente) |
| `--ordenar-por` | 'ocorrencias' ou 'quantidade' (apenas com --ranking) |

**Retorno por modo:**

| Modo | Campo principal | Conteúdo |
|------|-----------------|----------|
| `--cliente` | `nfds` | Lista de NFDs + resumo com total/valor |
| `--produto` | `linhas` | Linhas de devolução + resumo com qtd/clientes |
| `--ranking` | `ranking` | Top N com total_ocorrencias, qtd_total, total_clientes |
| `--custo` | `breakdown_mensal` | Custo total + breakdown por mês |

---

## Tratamento de Resultados

### Quando o Script Retorna `"sucesso": false`

```
1. Reportar o erro ao usuario: "Nao consegui consultar: {erro}"
2. NAO inventar dados alternativos
3. Sugerir: "Verifique se o numero da NF/nome do cliente esta correto"
```

### Quando o Script Retorna `"total": 0` (sem resultados)

```
1. Dizer claramente: "Nao encontrei entregas com os filtros aplicados"
2. Sugerir filtros alternativos:
   - Se buscou por NF → "Verifique o numero da NF"
   - Se buscou por cliente → "O nome exato pode ser diferente. Tente parte do nome"
   - Se buscou pendentes → "Nao ha entregas pendentes no momento"
3. NAO inventar dados ou resultados aproximados
```

### Quando `total` > `exibindo` (resultados truncados)

```
1. Informar: "Encontrei {total} entregas, mostrando as {exibindo} mais recentes"
2. Sugerir refinar filtros (período, NF específica) se total for muito grande
```

### Formatação de Resposta

```
- Datas: converter YYYY-MM-DD → DD/MM/YYYY (padrão brasileiro)
- Valores: R$ com separador de milhar (.) e decimal (,)
- Status null: dizer "pendente" ou "em andamento" (NÃO dizer "null")
- Canhoto null: dizer "sem canhoto registrado" (NÃO dizer "null")
- Listas: numerar NFs quando houver múltiplas
```

---

## Exemplos de Uso

### Cenário 1: Status de NF específica

```
Pergunta: "NF 144533 foi entregue?"
Script: consultando_status_entrega.py --nf 144533
Resposta: "A NF 144533 está pendente (em andamento). Data de embarque: 16/01/2025."
```

### Cenário 2: Entregas pendentes do Atacadão

```
Pergunta: "tem entrega pendente do Atacadão?"
Script: consultando_status_entrega.py --cliente atacadao --pendentes
Resposta: "Encontrei 5 entregas pendentes do Atacadão: [lista com NFs]"
```

### Cenário 3: Canhoto de NF

```
Pergunta: "tem canhoto da NF 144533?"
Script: consultando_status_entrega.py --nf 144533
Resposta (se canhoto_arquivo != null): "Sim, canhoto registrado para a NF 144533."
Resposta (se canhoto_arquivo == null): "Não, NF 144533 não tem canhoto registrado."
```

### Cenário 4: Devoluções abertas

```
Pergunta: "tem devolução aberta?"
Script: consultando_devolucoes.py --abertas
Resposta: "3 ocorrências de devolução abertas: [detalhes com NFD, cliente, categoria]"
```

### Cenário 5: Ranking de produtos devolvidos

```
Pergunta: "quais produtos são mais devolvidos?"
Script: consultando_devolucoes_detalhadas.py --ranking --limite 10
Resposta: "Top 10 produtos mais devolvidos: 1. Palmito 400g (45 ocorrências)..."
```

### Cenário 6: Custo de devoluções em período

```
Pergunta: "quanto custaram as devoluções em janeiro?"
Script: consultando_devolucoes_detalhadas.py --custo --de 2025-01-01 --ate 2025-01-31
Resposta: "Custo total de devoluções em janeiro: R$ 12.345,67 (15 despesas, 8 NFDs)"
```

### Cenário 7: Data de embarque

```
Pergunta: "que dia saiu a NF 144533?"
Script: consultando_status_entrega.py --nf 144533
Resposta: "A NF 144533 saiu (embarcou) em 16/01/2025."
```

---

## Referência Cruzada

| Skill | Quando usar em vez desta |
|-------|--------------------------|
| **gerindo-expedicao** | Pedidos antes de faturar, estoque, separação |
| **rastreando-odoo** | Rastrear NF/PO/pagamento no Odoo |
| **consultando-sql** | Consultas analíticas complexas (agregações, rankings avançados) |
| **analista-carteira** | Análise completa da carteira com decisões P1-P7 |

---

## References (sob demanda)

| Gatilho na Pergunta | Reference a Ler | Motivo |
|---------------------|-----------------|--------|
| "status de devolução" | `references/devolucoes.md` | Fluxo completo de devolução, status NFD/ocorrência |
| "categorias de ocorrência" | `references/devolucoes.md` | Valores válidos de categoria/subcategoria |
| "tabelas relacionadas" | `references/tables.md` | Relacionamentos entre tabelas do domínio |

---

## Tabelas do Domínio

| Tabela | Descrição | FK principal |
|--------|-----------|--------------|
| `entregas_monitoradas` | Status principal de cada NF | - |
| `agendamentos_entrega` | Histórico de agendamentos | `entrega_id → entregas_monitoradas.id` |
| `nf_devolucao` | NFs de devolução recebidas | `entrega_monitorada_id → entregas_monitoradas.id` |
| `nf_devolucao_linha` | Linhas/produtos da NFD | `nf_devolucao_id → nf_devolucao.id` |
| `ocorrencia_devolucao` | Ocorrências/tratativa | `nf_devolucao_id → nf_devolucao.id` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rafael-2109) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
