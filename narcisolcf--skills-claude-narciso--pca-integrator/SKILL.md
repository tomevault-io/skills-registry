---
name: pca-integrator
description: Constroi servidores MCP (Model Context Protocol) para conectar o Claude ao banco de dados PostgreSQL e APIs do PCA Camocim. Use when this capability is needed.
metadata:
  author: narcisolcf
---

# Arquiteto de Integração PCA

## Visão Geral
Esta habilidade permite criar "Pontes" (MCP Servers) entre o Claude e os dados reais da Prefeitura. Com ela, o Claude deixa de ser apenas um chat e passa a poder consultar o stock em tempo real ou validar CPFs diretamente na base de dados, de forma segura.

## Funcionalidades
Utiliza as referências em `reference/` para gerar código de servidor em Python (FastAPI) ou Node.js.

### 1. Criar Conector de Banco de Dados
Gera um servidor MCP que expõe tabelas específicas (ex: `itens_contratacao`) como ferramentas para o Claude.
- **Entrada:** "Crie um servidor MCP Python que permita consultar o saldo de orçamento de uma secretaria."
- **Saída:** Código Python completo usando `mcp-sdk` e `psycopg2`.

### 2. Validar Conexões
Usa os scripts de avaliação para garantir que o servidor criado responde corretamente.
- **Comando:** `python scripts/evaluation.py`

## Instruções de Desenvolvimento
Ao criar um novo servidor para o PCA:
1.  Use **Python** preferencialmente (temos mais referências em `reference/python_mcp_server.md`).
2.  Nunca exponha senhas (`connection_string`) diretamente no código; instrua o uso de variáveis de ambiente (`.env`).
3.  Implemente ferramentas de "apenas leitura" (SELECT) por padrão para segurança.

## Exemplo de Uso

**Prompt:**
"Preciso que o Claude consiga ler a tabela `fornecedores` do meu banco local. Crie o código do servidor MCP para isso."

**Ação:**
O assistente consultará `reference/python_mcp_server.md` e gerará um script `server.py` que define uma ferramenta `get_fornecedor_by_cnpj`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/narcisolcf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
