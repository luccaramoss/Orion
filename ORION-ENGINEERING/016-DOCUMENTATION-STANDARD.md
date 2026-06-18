# ORION DOCUMENTATION STANDARD
## Como Documentamos o Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Documentação desatualizada é pior que ausência de documentação — ela engana. No ORION, documentação é parte do Definition of Done de toda feature.

---

## TIPOS DE DOCUMENTAÇÃO

| Tipo | Onde | Quem mantém | Quando atualizar |
|------|------|-------------|-----------------|
| OEOS (DNA + Engineering) | `/ORION-DNA/`, `/ORION-ENGINEERING/` | Founders + Tech Lead | Quando processo ou decisão muda |
| README de módulo | `src/modules/[modulo]/README.md` | Dev que alterou o módulo | A cada PR que muda o módulo significativamente |
| OpenAPI | Gerado automaticamente via Swagger | Dev do endpoint | A cada novo endpoint ou mudança de contrato |
| ADR | `docs/adr/` | Quem tomou a decisão | Uma vez por decisão arquitetural |
| Comentários no código | Inline no código | Dev que escreveu | Junto com o código |
| CHANGELOG | `CHANGELOG.md` | Gerado automaticamente | A cada release |

---

## README DE MÓDULO

Todo módulo em `src/modules/` tem um `README.md` com esta estrutura:

```markdown
# [Nome do Módulo]

## Responsabilidade
Uma frase descrevendo o que este módulo faz.

## Entidades Principais
- `Customer` — descreva brevemente
- `PointBalance` — descreva brevemente

## Use Cases Disponíveis
| Use Case | Descrição |
|----------|-----------|
| `RegisterCustomerUseCase` | Registra um novo Cliente no tenant |
| `UpdateCustomerUseCase` | Atualiza dados de um Cliente existente |

## Eventos Emitidos
| Evento | Quando |
|--------|--------|
| `customer.registered` | Após registro bem-sucedido de novo Cliente |
| `customer.updated` | Após atualização de dados do Cliente |

## Eventos Consumidos
| Evento | Ação |
|--------|------|
| `purchase.completed` | Atualiza o perfil comportamental do Cliente |

## Dependências Externas
- Nenhuma (ou liste as integrações)

## Como Testar Localmente
Instruções específicas se houver algo especial.
```

---

## COMENTÁRIOS NO CÓDIGO

### O que comentar

```typescript
// ✅ Comente o PORQUÊ de uma decisão não-óbvia
// Usamos delay de 100ms para evitar rate limit da API do WhatsApp
// que permite no máximo 80 req/s por número de telefone
await sleep(100);

// ✅ Comente edge cases e regras de negócio complexas
// Clientes com menos de 30 dias de cadastro não participam do cálculo de churn
// pois não temos dados suficientes para predição confiável
if (customer.daysSinceRegistration < 30) return null;

// ✅ Comente referências a regras de negócio documentadas
// Regra de acúmulo: R$ 1,00 gasto = 1 ponto (ver OEOS / regras de pontuação do tenant)
const points = Math.floor(purchaseAmount);
```

### O que NÃO comentar

```typescript
// ❌ O que o código já diz claramente
// Incrementa o contador  ← desnecessário
count++;

// ❌ Código comentado (use git para histórico)
// const oldLogic = customer.points * 1.5;
const points = customer.points * rule.multiplier;

// ❌ TODO sem data e responsável
// TODO: refatorar isso depois  ← inútil

// ✅ TODO com contexto quando necessário
// TODO(gabi, 2026-08): extrair para domain service quando módulo de gamification for criado
```

---

## OPENAPI / SWAGGER

Todo endpoint público tem documentação completa:

```typescript
@Post()
@ApiOperation({
  summary: 'Registra um novo Cliente',
  description: 'Cria o perfil do Cliente no tenant. Se o e-mail já existir, retorna 409.',
})
@ApiBody({ type: RegisterCustomerDto })
@ApiResponse({
  status: 201,
  description: 'Cliente registrado com sucesso',
  type: CustomerResponseDto,
})
@ApiResponse({
  status: 409,
  description: 'Já existe um Cliente com este e-mail neste tenant',
})
@ApiResponse({
  status: 422,
  description: 'Dados inválidos (e-mail mal formatado, telefone inválido, etc.)',
})
async register(@Body() dto: RegisterCustomerDto): Promise<ApiResponse<CustomerResponseDto>> {}
```

---

## ARCHITECTURE DECISION RECORDS (ADR)

```markdown
# ADR-006: Estratégia de Isolamento de Tenant

**Data:** 2026-06-17  
**Status:** Aceito  
**Decisores:** Gabi (founder), Claude (tech lead)

## Contexto
O ORION é uma plataforma multi-tenant. Precisamos garantir que dados de um tenant
nunca sejam acessados por outro. Existem duas abordagens principais: schema-per-tenant
e row-level-security (RLS) com tenant_id em todas as tabelas.

## Alternativas Consideradas

### Alternativa 1: Schema por tenant
- **Prós:** Isolamento perfeito, migrations independentes por tenant
- **Contras:** Complexidade operacional alta para muitos tenants, pooling de conexões complexo

### Alternativa 2: RLS com tenant_id (escolhida)
- **Prós:** Simples de operar, um banco para todos, pool de conexões eficiente
- **Contras:** Requer disciplina de código para nunca esquecer o tenant_id

## Decisão
Adotar RLS com tenant_id obrigatório em todas as tabelas, combinado com:
1. Middleware que injeta e valida tenantId em todo request
2. CI check que rejeita queries sem WHERE tenant_id
3. Testes de integração que verificam isolamento

## Consequências
- Todo desenvolvedor (e o Claude) deve sempre incluir tenantId em queries
- Review checklist inclui verificação de tenantId
- Testes de integração têm caso específico de verificação de isolamento
- Escala para até ~10.000 tenants sem necessidade de revisão arquitetural
```

---

## DEFINITION OF DONE (DOCUMENTAÇÃO)

Uma feature está completa quando:

- [ ] README do módulo atualizado (se mudança no módulo)
- [ ] OpenAPI completo para todos os novos endpoints
- [ ] Código tem comentários nos pontos não-óbvios
- [ ] ADR criado (se decisão arquitetural)
- [ ] CHANGELOG será gerado automaticamente no release

---

*Versão: 1.0 | Junho 2026*  
*Este é o último documento do ORION Engineering — Camada 2.*  
*Próxima camada: ORION Product — Camada 3*
