# ORION VERSIONING
## Como Versionamos o Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## SEMANTIC VERSIONING (SemVer)

O ORION segue [SemVer 2.0.0](https://semver.org/): `MAJOR.MINOR.PATCH`

| Componente | Quando incrementar |
|------------|-------------------|
| `MAJOR` | Breaking change — mudança incompatível com versão anterior |
| `MINOR` | Nova feature — compatível com versão anterior |
| `PATCH` | Bug fix — compatível com versão anterior |

### Exemplos

```
v1.0.0  → Release inicial em produção
v1.1.0  → Adicionado módulo de Customer Intelligence
v1.1.1  → Corrigido cálculo de pontos em resgates parciais
v1.2.0  → Integração com Bling adicionada
v2.0.0  → Breaking change no formato de resposta da API
```

---

## VERSIONAMENTO DA API

A API do ORION é versionada na URL:

```
/api/v1/customers
/api/v1/campaigns
/api/v2/customers   → quando há breaking change
```

**Política de suporte:**
- Versão atual (v2): suporte completo
- Versão anterior (v1): suporte por 6 meses após lançamento da v2
- Versões mais antigas: deprecadas sem suporte

**Header de deprecação:**
```
Deprecation: true
Sunset: Sat, 31 Jan 2027 00:00:00 GMT
Link: <https://api.orion.app/v2/customers>; rel="successor-version"
```

---

## VERSIONAMENTO DO APP MOBILE

O app Flutter segue versionamento diferenciado para evitar forçar atualização desnecessária:

```
version: MAJOR.MINOR.PATCH+BUILD_NUMBER
exemplo:  1.2.0+45
```

- `MAJOR.MINOR.PATCH`: versão de negócio (SemVer)
- `BUILD_NUMBER`: número incremental de build (para lojas)

**Política de atualização obrigatória:**
- MAJOR: atualização obrigatória (app não funciona sem atualizar)
- MINOR: atualização recomendada (banner no app)
- PATCH: atualização silenciosa (sem notificação)

---

## CHANGELOG

Cada release tem um CHANGELOG.md atualizado automaticamente via `conventional-changelog`:

```markdown
# Changelog

## [1.2.0] - 2026-07-15

### Features
- **intelligence**: add customer churn risk score generation (#45)
- **campaigns**: add AI-powered campaign recommendations (#48)
- **integrations**: add Bling ERP integration (#50)

### Bug Fixes
- **points**: fix decimal rounding error in cashback calculation (#52)
- **auth**: fix refresh token rotation on concurrent requests (#53)

### Performance
- **customers**: optimize RFM query with compound index (#54)

## [1.1.1] - 2026-07-01
...
```

---

## PROCESSO DE RELEASE

```bash
# 1. Merge staging → main (via PR aprovado)

# 2. Gerar tag de versão
git tag -a v1.2.0 -m "release: v1.2.0"
git push origin v1.2.0

# 3. CI/CD pipeline executa automaticamente:
#    - Build da imagem Docker
#    - Push para registry
#    - Deploy em produção
#    - Smoke tests pós-deploy
#    - Notificação de sucesso/falha

# 4. Atualizar CHANGELOG.md
npm run changelog  # gera automaticamente via conventional-changelog

# 5. Criar GitHub Release com notas de release
```

---

## VERSIONAMENTO DE DOCUMENTOS

Documentos do OEOS têm versão própria no cabeçalho:

```markdown
**Versão:** 1.0
**Última atualização:** Junho 2026
**Status:** Ativo | Deprecado | Rascunho
```

Documentos são atualizados quando:
- A realidade do projeto muda (nova stack, nova decisão)
- Um First Principle é revisado
- Um processo é alterado

Versão de documento é incrementada a cada atualização significativa. Histórico de mudanças é rastreado via git.

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [015-CODE-REVIEW.md](./015-CODE-REVIEW.md)*
