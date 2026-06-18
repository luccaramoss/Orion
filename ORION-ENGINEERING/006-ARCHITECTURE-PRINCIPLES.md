# ORION ARCHITECTURE PRINCIPLES
## Os Princípios que Guiam a Arquitetura do ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## 1. CLEAN ARCHITECTURE

O ORION segue Clean Architecture como base. As camadas, de dentro para fora:

```
┌────────────────────────────────┐
│   DOMAIN (centro)               │
│   Entities, Value Objects,      │
│   Domain Events, Interfaces     │
│   de Repository                 │
├─────────────────────────────────┤
│   APPLICATION                   │
│   Use Cases, DTOs de entrada    │
├─────────────────────────────────┤
│   INFRASTRUCTURE                │
│   Repositories (TypeORM),       │
│   Adapters externos,            │
│   ORM Entities, Mappers         │
├─────────────────────────────────┤
│   INTERFACE (borda externa)     │
│   Controllers, Middlewares,     │
│   DTOs de resposta              │
└─────────────────────────────────┘
```

**A Regra de Dependência:** Dependências sempre apontam para dentro. Domínio não conhece nada externo. Infrastructure implementa interfaces definidas no Domain.

---

## 2. DOMAIN-DRIVEN DESIGN (DDD)

### Conceitos aplicados no ORION

**Entities:** Objetos com identidade única que persiste ao longo do tempo.
```typescript
// Customer é uma Entity — tem ID e persiste
class Customer {
  constructor(public readonly id: string, ...) {}
}
```

**Value Objects:** Objetos sem identidade, definidos pelos seus valores.
```typescript
// CustomerEmail é um Value Object — não tem ID
class CustomerEmail {
  constructor(private readonly value: string) {
    if (!value.includes('@')) throw new DomainException('Email inválido');
  }
  getValue(): string { return this.value; }
}
```

**Aggregates:** Cluster de entidades tratadas como uma unidade. A Aggregate Root controla o acesso.
```typescript
// Customer é Aggregate Root — PointBalance só existe dentro do contexto de Customer
class Customer {
  private pointBalance: PointBalance;

  accruePoints(amount: number): void {
    this.pointBalance.add(amount);
    this.addDomainEvent(new PointsAccruedEvent(this.id, amount));
  }
}
```

**Domain Events:** Fatos que aconteceram no domínio.
```typescript
class PurchaseCompletedEvent {
  constructor(
    public readonly customerId: string,
    public readonly tenantId: string,
    public readonly amount: number,
    public readonly occurredAt: Date,
  ) {}
}
```

**Repository (interface no Domain):**
```typescript
// Defined in Domain layer
interface ICustomerRepository {
  save(customer: Customer): Promise<void>;
  findById(tenantId: string, id: string): Promise<Customer | null>;
}
```

**Bounded Contexts no ORION:**
- `Customer Context` — identidade, perfil, comportamento
- `Campaign Context` — criação, segmentação, disparo
- `Rewards Context` — catálogo, resgate, saldo
- `Intelligence Context` — scores, insights, recomendações
- `Integration Context` — Nuvemshop, Bling, PDV

---

## 3. MULTI-TENANCY

### Estratégia: Row-Level Security (RLS) + Schema por tenant

Para o MVP e primeiros 50 tenants: **`tenant_id` em todas as tabelas + RLS no PostgreSQL**.

```sql
-- Toda tabela tem tenant_id
CREATE TABLE customers (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id   UUID NOT NULL REFERENCES tenants(id),
  -- ...
);

-- Índice composto sempre inclui tenant_id primeiro
CREATE INDEX idx_customers_tenant_search 
ON customers(tenant_id, email_address, status);

-- RLS Policy
ALTER TABLE customers ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON customers
  USING (tenant_id = current_setting('app.current_tenant')::UUID);
```

### Middleware de tenant

```typescript
@Injectable()
export class TenantMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const tenantId = req.headers['x-tenant-id'] as string;
    if (!tenantId) throw new UnauthorizedException('Tenant não identificado');
    
    // Injeta tenant no contexto da request
    req['tenantId'] = tenantId;
    next();
  }
}
```

---

## 4. EVENT-DRIVEN ARCHITECTURE

Operações assíncronas usam eventos de domínio processados via Bull (Redis):

```
Compra registrada no PDV
    ↓
PurchaseCompletedEvent emitido
    ↓
┌───────────────────────────────────────┐
│  Queue Workers (paralelos)            │
│  ├── AccruePointsWorker               │
│  ├── UpdateCustomerProfileWorker      │
│  ├── TriggerIntelligenceWorker        │
│  └── CheckCampaignEligibilityWorker   │
└───────────────────────────────────────┘
    ↓
NotificationDispatchEvent (se elegível)
    ↓
SendPushNotificationWorker
```

**Por que event-driven para essas operações:**
- A compra é confirmada imediatamente (sem aguardar processamento de pontos)
- Workers falham e retentam independentemente
- Fácil adicionar novo comportamento sem modificar o fluxo de compra

---

## 5. CACHING STRATEGY

```
┌──────────────────────────────────────────┐
│  Cache em Redis — TTL por categoria      │
│                                          │
│  Customer Profile       TTL: 5 min       │
│  Tenant Configuration   TTL: 60 min      │
│  Segment Definition     TTL: 30 min      │
│  Reward Catalog         TTL: 15 min      │
│  Intelligence Score     TTL: 10 min      │
└──────────────────────────────────────────┘
```

**Cache Invalidation:**
- Sempre invalidar na escrita (write-through ou write-around)
- Nunca retornar dado de cache sem TTL definido
- Prefixo de cache inclui sempre tenant_id: `orion:{tenantId}:customer:{customerId}`

---

## 6. API DESIGN

### Versionamento
```
/api/v1/customers
/api/v1/campaigns
```

Versão na URL. Nova versão apenas quando há breaking change.

### Paginação padrão
```typescript
interface PaginatedResult<T> {
  data: T[];
  meta: {
    total: number;
    page: number;
    limit: number;
    totalPages: number;
  };
}
```

### Respostas consistentes
```typescript
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
    details?: Record<string, string>[];
  };
  meta?: PaginationMeta;
}
```

---

## 7. DECISÕES ARQUITETURAIS REGISTRADAS

Toda mudança arquitetural significativa gera um ADR em `docs/adr/`.

Template de ADR:
```markdown
# ADR-001: [Título da Decisão]

**Data:** YYYY-MM-DD  
**Status:** [Proposto | Aceito | Substituído]  
**Decisores:** [nomes]

## Contexto
O que motivou essa decisão?

## Alternativas consideradas
1. Alternativa A — prós e contras
2. Alternativa B — prós e contras

## Decisão
O que foi decidido e por quê.

## Consequências
O que muda? Quais são os trade-offs aceitos?
```

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [007-STACK-DECISIONS.md](./007-STACK-DECISIONS.md)*
