# ORION GIT WORKFLOW
## Como Usamos Git no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## ESTRATÉGIA DE BRANCHES

```
main                    ← produção — protegida, nunca commit direto
  └── staging           ← pré-produção — merge de feature branches aprovadas
        └── feat/xxx    ← feature branches — onde o trabalho acontece
        └── fix/xxx     ← correção de bug
        └── docs/xxx    ← documentação
        └── refactor/xx ← refatoração
        └── chore/xxx   ← manutenção (deps, CI, config)
```

**Fluxo:**
```
feat/customer-churn-score
    ↓ (PR aprovado)
staging
    ↓ (testes em staging + aprovação manual)
main
    ↓ (deploy automático)
produção
```

---

## NOMES DE BRANCH

```bash
# feat/descricao-curta-do-que-faz
feat/customer-churn-score
feat/campaign-ai-recommendation
feat/nuvemshop-integration

# fix/descricao-do-bug
fix/points-rounding-error
fix/tenant-isolation-middleware

# docs/o-que-esta-sendo-documentado
docs/engineering-bible
docs/adr-multi-tenancy

# refactor/o-que-esta-sendo-refatorado
refactor/customer-repository-clean-arch

# chore/o-que-esta-sendo-feito
chore/update-nestjs-v10
chore/add-dependabot-config
```

---

## CONVENTIONAL COMMITS

Todo commit segue o padrão [Conventional Commits](https://www.conventionalcommits.org/):

```
tipo(escopo): descrição curta em imperativo

[corpo opcional — explica o porquê, não o o quê]

[rodapé opcional — breaking changes, closes #issue]
```

### Tipos

| Tipo | Quando usar |
|------|-------------|
| `feat` | Nova funcionalidade |
| `fix` | Correção de bug |
| `docs` | Documentação apenas |
| `refactor` | Refatoração sem mudança de comportamento |
| `test` | Adição ou correção de testes |
| `chore` | Manutenção (deps, CI, config, scripts) |
| `perf` | Melhoria de performance |
| `style` | Formatação (sem mudança de lógica) |

### Escopos (módulos do ORION)

`customers`, `campaigns`, `rewards`, `points`, `transactions`, `intelligence`, `notifications`, `integrations`, `auth`, `tenants`, `shared`, `infra`, `ci`, `docs`

### Exemplos

```bash
feat(customers): add RFM segmentation calculation
fix(points): correct decimal rounding on cashback accrual
docs(adr): add ADR-005 for caching strategy decision
refactor(campaigns): extract dispatch logic to domain service
test(rewards): add integration tests for reward redemption flow
chore: update NestJS to v10.3.2
feat(intelligence): add customer churn risk score generation

# Com breaking change
feat(api)!: change customer endpoint response format

BREAKING CHANGE: customer response now wraps data in { success, data } envelope.
All consumers must update their response parsing.
```

---

## PULL REQUEST

### Regras

1. Todo PR vai para `staging` (nunca direto para `main`)
2. Mínimo 1 review aprovado antes do merge
3. CI deve passar (testes + lint + build)
4. PR deve ter descrição preenchida (template abaixo)

### Template de PR

```markdown
## O que foi feito
Descrição clara e objetiva do que foi implementado.

## Por que foi feito
Contexto do problema ou feature que motivou essa mudança.

## Como testar
1. Passo 1
2. Passo 2
3. Resultado esperado

## Checklist
- [ ] Testes unitários escritos/atualizados
- [ ] Cobertura mínima mantida
- [ ] Nenhum `any` sem justificativa
- [ ] Todas as queries têm `tenantId`
- [ ] Logs sem PII
- [ ] README do módulo atualizado (se necessário)
- [ ] OpenAPI atualizado (se novo endpoint)
- [ ] ADR criado (se decisão arquitetural)

## Screenshots (se mudança visual)
[anexar se aplicável]
```

---

## PROTEÇÃO DE BRANCHES

**main:**
- Require PR review: 1 aprovação mínima
- Require status checks: `test`, `lint`, `build`
- No direct push — nunca, sem exceção
- Require linear history (no merge commits — rebase only)

**staging:**
- Require PR review: 1 aprovação mínima
- Require status checks: `test`, `lint`

---

## MERGE STRATEGY

**Usamos Squash Merge para `staging` ← feature branches:**
- Mantém histórico de `staging` limpo
- Um commit por feature/fix

**Usamos Merge Commit para `main` ← `staging`:**
- Preserva o contexto de cada release no histórico de main

---

## TAGS E RELEASES

```bash
# Versão semântica: MAJOR.MINOR.PATCH
v1.0.0    ← release inicial
v1.1.0    ← nova feature
v1.1.1    ← bugfix
v2.0.0    ← breaking change

# Tag sempre na main após deploy confirmado
git tag -a v1.1.0 -m "feat: add AI campaign recommendations"
git push origin v1.1.0
```

---

## .GITIGNORE OBRIGATÓRIO

```gitignore
# Ambiente
.env
.env.local
.env.*.local

# Dependências
node_modules/
.dart_tool/
.pub-cache/

# Build
dist/
build/
.next/

# IDE
.idea/
.vscode/
*.swp

# Logs
*.log
logs/

# OS
.DS_Store
Thumbs.db

# Testes
coverage/
.nyc_output/
```

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [014-VERSIONING.md](./014-VERSIONING.md)*
