# ORION NAMING CONVENTIONS
## Como Nomeamos Tudo no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

> Bons nomes eliminam a necessidade de comentários. No ORION, nomes são documentação.

---

## 1. REGRAS GERAIS

| Regra | Detalhe |
|-------|---------|
| Sem abreviações | `customer` não é `cust`. `campaign` não é `camp`. |
| Sem prefixos desnecessários | `customerName` não é `strCustomerName`. |
| Sem sufixos genéricos | `CustomerData` → `Customer`. `CampaignInfo` → `Campaign`. |
| Intenção clara | `findActiveCustomersBySegment()` não é `getCustomers()`. |
| Em inglês | Todo código em inglês. Comentários e documentação em português. |

---

## 2. TYPESCRIPT / NESTJS

### Classes
```typescript
// PascalCase — sempre
class Customer {}
class RegisterCustomerUseCase {}
class CustomerRepository {}
class CustomerController {}
class CustomerRegisteredEvent {}
class CustomerNotFoundException {}
```

### Interfaces
```typescript
// PascalCase com prefixo I para repositories e services injetáveis
interface ICustomerRepository {}
interface INotificationService {}

// Sem prefixo I para shapes de dados
interface CustomerProps {}
interface CreateCampaignInput {}
```

### Funções e métodos
```typescript
// camelCase — verbo + substantivo descritivo
async registerCustomer(dto: RegisterCustomerDto): Promise<Customer> {}
async findActiveCustomersBySegment(tenantId: string, segmentId: string): Promise<Customer[]> {}
async calculateChurnRiskScore(customer: Customer): Promise<number> {}
async sendCampaignToSegment(campaignId: string, segmentId: string): Promise<void> {}
```

### Variáveis
```typescript
// camelCase — substantivo descritivo
const customer: Customer;
const activeCustomers: Customer[];
const churnRiskScore: number;
const campaignSentAt: Date;
const isEligibleForReward: boolean; // booleanos: is/has/can/should
const hasRedeemedReward: boolean;
const canAccruePoints: boolean;
```

### Constantes
```typescript
// UPPER_SNAKE_CASE para constantes de configuração
const MAX_POINTS_PER_TRANSACTION = 1000;
const CHURN_RISK_THRESHOLD = 0.7;
const CAMPAIGN_RETRY_ATTEMPTS = 3;

// PascalCase para tokens de injeção
const CUSTOMER_REPOSITORY = Symbol('ICustomerRepository');
const INTELLIGENCE_SERVICE = Symbol('IIntelligenceService');
```

### Arquivos
```
// kebab-case — tipo no sufixo
customer.entity.ts
customer.repository.interface.ts
customer.repository.ts
customer.mapper.ts
customer.controller.ts
customer.service.ts
customer.module.ts
register-customer.use-case.ts
register-customer.use-case.spec.ts
customer-registered.event.ts
customer-not-found.exception.ts
register-customer.dto.ts
customer-response.dto.ts
```

---

## 3. BANCO DE DADOS

### Tabelas
```sql
-- snake_case, plural, em inglês
customers
campaigns
rewards
point_transactions
customer_segments
campaign_dispatches
reward_redemptions
tenant_configurations
```

### Colunas
```sql
-- snake_case, descritivo
customer_id         UUID PRIMARY KEY
tenant_id           UUID NOT NULL        -- SEMPRE presente em tabelas de tenant
full_name           VARCHAR(100)
email_address       VARCHAR(255)
phone_number        VARCHAR(20)
churn_risk_score    DECIMAL(3,2)
lifetime_value      DECIMAL(10,2)
last_purchase_at    TIMESTAMPTZ
created_at          TIMESTAMPTZ DEFAULT NOW()
updated_at          TIMESTAMPTZ DEFAULT NOW()
deleted_at          TIMESTAMPTZ           -- soft delete quando aplicável
```

### Índices
```sql
-- idx_tabela_coluna(s)
idx_customers_tenant_id
idx_customers_tenant_id_email
idx_transactions_customer_id_created_at
idx_campaigns_tenant_id_status
```

### Constraints
```sql
-- fk_tabela_coluna_referencia
fk_transactions_customer_id_customers
fk_campaign_dispatches_campaign_id_campaigns

-- uq_tabela_coluna(s)
uq_customers_tenant_id_email
```

---

## 4. API ENDPOINTS

```
# Padrão: /recurso (plural, kebab-case)

# Recursos principais
GET    /customers
POST   /customers
GET    /customers/:customerId
PUT    /customers/:customerId
DELETE /customers/:customerId

# Sub-recursos
GET    /customers/:customerId/transactions
GET    /customers/:customerId/rewards
GET    /customers/:customerId/intelligence
POST   /customers/:customerId/points/accrue

# Ações que não são CRUD
POST   /campaigns/:campaignId/send
POST   /campaigns/:campaignId/cancel
POST   /rewards/:rewardId/redeem
POST   /integrations/nuvemshop/sync

# Parâmetros de query
GET    /customers?segment=vip&status=active&page=1&limit=20&sort=createdAt&order=desc
```

---

## 5. EVENTOS DE DOMÍNIO

```typescript
// PascalCase — Entidade + Verbo no passado + "Event"
CustomerRegisteredEvent
PurchaseCompletedEvent
PointsAccruedEvent
RewardRedeemedEvent
CampaignSentEvent
CustomerChurnRiskDetectedEvent
CustomerSegmentUpdatedEvent
IntegrationSyncCompletedEvent

// Nome do evento como string (para EventEmitter)
'customer.registered'
'purchase.completed'
'points.accrued'
'reward.redeemed'
'campaign.sent'
'customer.churn_risk_detected'
```

---

## 6. FLUTTER

```dart
// Telas (Screens): PascalCase + Screen
CustomerProfileScreen
CampaignListScreen
RewardRedemptionScreen
PointsHistoryScreen

// Widgets reutilizáveis: PascalCase + tipo do widget
PointsBalanceCard
CampaignTileWidget
RewardBadgeComponent
CustomerAvatarWidget

// Providers (Riverpod): camelCase + Provider
customerProfileProvider
campaignListProvider
pointsBalanceProvider

// Arquivos: snake_case
customer_profile_screen.dart
points_balance_card.dart
campaign_tile_widget.dart
customer_profile_provider.dart
```

---

## 7. AMBIENTE E CONFIGURAÇÃO

```bash
# Variáveis de ambiente: UPPER_SNAKE_CASE com prefixo do serviço
DATABASE_URL=
DATABASE_SCHEMA=
REDIS_URL=
ELASTICSEARCH_URL=
FIREBASE_PROJECT_ID=
FIREBASE_PRIVATE_KEY=
OPENAI_API_KEY=
NUVEMSHOP_CLIENT_ID=
NUVEMSHOP_CLIENT_SECRET=
BLING_CLIENT_ID=
BLING_CLIENT_SECRET=
WHATSAPP_API_KEY=
JWT_SECRET=
JWT_EXPIRATION=
REFRESH_TOKEN_SECRET=
REFRESH_TOKEN_EXPIRATION=
```

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [006-ARCHITECTURE-PRINCIPLES.md](./006-ARCHITECTURE-PRINCIPLES.md)*
