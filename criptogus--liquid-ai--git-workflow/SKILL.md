---
name: git-workflow
description: Git workflow - Boas práticas para branches, commits e PRs Use when this capability is needed.
metadata:
  author: criptogus
---

# Git Workflow - Boas Práticas

Esta skill implementa um workflow Git consistente para desenvolvimento em equipe.

## Modelo de Branches

```
main (ou master)
  │
  ├── develop
  │     │
  │     ├── feature/user-auth
  │     ├── feature/payment-flow
  │     └── feature/dashboard-v2
  │
  ├── release/v1.2.0
  │
  └── hotfix/critical-bug
```

### Branch Naming Convention

| Tipo | Pattern | Exemplo |
|------|---------|---------|
| Feature | `feature/<descrição>` | `feature/user-authentication` |
| Bugfix | `bugfix/<issue-id>` | `bugfix/issue-123` |
| Hotfix | `hotfix/<descrição>` | `hotfix/security-patch` |
| Release | `release/<version>` | `release/v1.2.0` |
| Experiment | `experiment/<descrição>` | `experiment/new-cache` |

**Regras de Nomenclatura:**
- Use kebab-case (palavras separadas por hífen)
- Seja descritivo mas conciso
- Inclua issue ID quando relevante
- Evite caracteres especiais

## Commits

### Conventional Commits

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

**Types:**

| Type | Quando Usar |
|------|-------------|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `docs` | Apenas documentação |
| `style` | Formatação (não afeta código) |
| `refactor` | Refatoração (sem feat/fix) |
| `perf` | Melhoria de performance |
| `test` | Adição/correção de testes |
| `chore` | Manutenção (build, deps, etc) |
| `ci` | Mudanças em CI/CD |

**Exemplos:**

```bash
# Feature
feat(auth): add OAuth2 login with Google

# Bug fix
fix(api): handle null response from payment gateway

Closes #123

# Breaking change
feat(api)!: change response format for /users endpoint

BREAKING CHANGE: response now returns array instead of object
```

### Regras de Commit

```
┌─────────────────────────────────────────────────────────────┐
│  1. Commits atômicos - Uma mudança lógica por commit       │
│  2. Mensagem clara - Explica O QUE e POR QUÊ               │
│  3. Presente imperativo - "Add feature" não "Added"        │
│  4. Max 72 chars no título                                 │
│  5. Body para contexto adicional                           │
└─────────────────────────────────────────────────────────────┘
```

**Commit Atômico - Exemplos:**

```bash
# ❌ RUIM - Múltiplas mudanças
git commit -m "Add login, fix header, update deps"

# ✅ BOM - Commits separados
git commit -m "feat(auth): add login form"
git commit -m "fix(ui): correct header alignment"
git commit -m "chore(deps): update React to v18"
```

## Workflow Diário

### 1. Começar Feature

```bash
# Atualizar develop
git checkout develop
git pull origin develop

# Criar branch
git checkout -b feature/minha-feature

# Trabalhar...
git add .
git commit -m "feat(scope): description"
```

### 2. Manter Branch Atualizada

```bash
# Opção 1: Rebase (preferido para features)
git fetch origin
git rebase origin/develop

# Opção 2: Merge (se já compartilhou a branch)
git fetch origin
git merge origin/develop
```

### 3. Resolver Conflitos

```bash
# Durante rebase
git rebase origin/develop
# Se conflito:
# 1. Resolver conflitos nos arquivos
# 2. git add <arquivos-resolvidos>
# 3. git rebase --continue

# Se quiser abortar
git rebase --abort
```

### 4. Preparar para PR

```bash
# Squash commits se necessário
git rebase -i HEAD~<numero-de-commits>
# Marcar commits para squash (s) ou fixup (f)

# Push
git push origin feature/minha-feature

# Se já fez push antes e rebased
git push --force-with-lease origin feature/minha-feature
```

## Pull Requests

### Checklist Antes de Abrir PR

- [ ] Código compila sem erros
- [ ] Testes passam localmente
- [ ] Linter/formatter aplicado
- [ ] Branch atualizada com develop
- [ ] Commits organizados e descritivos
- [ ] Self-review feito

### Template de PR

```markdown
## Descrição
[O que este PR faz e por quê]

## Tipo de Mudança
- [ ] Feature nova
- [ ] Bug fix
- [ ] Refatoração
- [ ] Documentação
- [ ] Outro: ___

## Como Testar
1. [Passo 1]
2. [Passo 2]
3. [Resultado esperado]

## Checklist
- [ ] Código segue padrões do projeto
- [ ] Testes adicionados/atualizados
- [ ] Documentação atualizada
- [ ] Sem breaking changes (ou documentado)

## Screenshots (se aplicável)
[Imagens aqui]

## Issues Relacionadas
Closes #123
```

### Tamanho do PR

```
┌─────────────────────────────────────────────────────────────┐
│  IDEAL: < 400 linhas de código                             │
│  MÁXIMO: 1000 linhas (divida se maior)                     │
│                                                             │
│  PRs menores = Reviews melhores = Menos bugs               │
└─────────────────────────────────────────────────────────────┘
```

## Comandos Úteis

### Desfazer Mudanças

```bash
# Descartar mudanças não commitadas
git checkout -- <arquivo>
git restore <arquivo>  # Git 2.23+

# Desfazer último commit (mantém mudanças)
git reset --soft HEAD~1

# Desfazer último commit (descarta mudanças)
git reset --hard HEAD~1

# Reverter commit já pushed
git revert <commit-hash>
```

### Stash (Guardar Temporariamente)

```bash
# Guardar mudanças
git stash
git stash save "descrição"

# Listar stashes
git stash list

# Recuperar último stash
git stash pop

# Recuperar stash específico
git stash apply stash@{2}
```

### Histórico e Busca

```bash
# Log bonito
git log --oneline --graph --all

# Buscar em commits
git log --grep="palavra"

# Buscar quem mudou linha
git blame <arquivo>

# Buscar quando código foi adicionado
git log -S "código" --source --all
```

### Cherry-pick

```bash
# Aplicar commit específico na branch atual
git cherry-pick <commit-hash>

# Cherry-pick sem commitar
git cherry-pick -n <commit-hash>
```

## Situações Comuns

### "Commitei na Branch Errada"

```bash
# Se ainda não fez push
git reset --soft HEAD~1
git stash
git checkout branch-correta
git stash pop
git commit -m "mensagem"
```

### "Preciso Mudar Mensagem do Último Commit"

```bash
# Apenas último commit, não pushed
git commit --amend -m "nova mensagem"

# Commits mais antigos
git rebase -i HEAD~3
# Marcar commit com 'reword'
```

### "Quero Desfazer Merge"

```bash
# Se não fez push
git reset --hard HEAD~1

# Se já fez push
git revert -m 1 <merge-commit-hash>
```

### "Branch Divergiu Muito de Develop"

```bash
# Opção 1: Rebase incremental
git fetch origin
git rebase origin/develop
# Resolver conflitos commit a commit

# Opção 2: Merge
git merge origin/develop
# Resolver todos conflitos de uma vez
```

## Anti-Patterns

| Anti-Pattern | Problema | Solução |
|--------------|----------|---------|
| `git add .` cego | Commita arquivos indesejados | `git add -p` ou review antes |
| Force push em shared branch | Perde trabalho dos outros | Use `--force-with-lease` |
| Commits gigantes | Difícil review e rollback | Commits atômicos |
| Branch de longa vida | Conflitos acumulam | Merge frequente ou feature flags |
| Mensagens vagas | "fix stuff", "wip" | Conventional commits |

---

**Esta skill ativa AUTOMATICAMENTE quando:**
- Discussão sobre branches, commits, ou merges
- Usuário menciona "git", "PR", "pull request"
- Problemas com histórico ou conflitos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/criptogus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
