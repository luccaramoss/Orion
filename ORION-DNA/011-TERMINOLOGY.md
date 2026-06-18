# ORION TERMINOLOGY
## Padrões de Linguagem e Termos Controlados

**Volume:** ORION DNA — Camada 1: Fundação

> A terminologia define como pensamos. Quando toda a equipe — humana e IA — usa os mesmos termos, a comunicação é mais rápida, o código é mais legível e a documentação é mais consistente.

---

## 1. REGRAS GERAIS DE LINGUAGEM

### 1.1 Sempre use substantivos do domínio, nunca genéricos

| ❌ Genérico | ✅ ORION |
|------------|----------|
| "o usuário clicou" | "o Cliente resgatou a recompensa" |
| "o admin configurou" | "o Gestor criou uma campanha" |
| "o sistema enviou" | "a Plataforma disparou a notificação" |
| "o banco salvou" | "o repositório persistiu o evento de compra" |

### 1.2 Em código, use os termos do domínio como nomes de entidades

```typescript
// ❌ Errado
class User { ... }
class Store { ... }
class Product { ... }

// ✅ ORION
class Customer { ... }
class Tenant { ... }
class Reward { ... }
class Campaign { ... }
```

### 1.3 Em mensagens para o Gestor, use linguagem de negócio — nunca técnica

| ❌ Técnico | ✅ Negócio |
|-----------|-----------|
| "Query executada com sucesso" | "Dados atualizados" |
| "Webhook recebido" | "Nova compra registrada" |
| "Token expirado" | "Sua sessão foi encerrada por segurança. Entre novamente." |
| "500 Internal Server Error" | "Algo deu errado. Estamos verificando. Tente em instantes." |

---

## 2. CONVENÇÕES DE NOMENCLATURA

### 2.1 Entidades principais — sempre singular, PascalCase no código

```
Customer       → cliente final
Tenant         → empresa que usa o ORION
Campaign       → campanha de marketing
Reward         → recompensa disponível para resgate
Points         → saldo de pontos do Customer
Transaction    → registro de compra ou evento de pontuação
Segment        → grupo de Customers
Notification   → mensagem enviada ao Customer
Integration    → conexão com sistema externo
```

### 2.2 Eventos de domínio — passado, PascalCase

```
CustomerRegistered
PurchaseCompleted
PointsAccrued
RewardRedeemed
CampaignSent
CustomerChurnRiskDetected
SegmentUpdated
IntegrationSynced
```

### 2.3 Comandos — imperativo, PascalCase

```
RegisterCustomer
ProcessPurchase
AccruePoints
RedeemReward
SendCampaign
UpdateSegment
SyncIntegration
GenerateCustomerIntelligence
```

### 2.4 Repositórios — entidade + Repository

```
CustomerRepository
CampaignRepository
RewardRepository
TransactionRepository
```

### 2.5 Serviços de domínio — entidade + Service

```
CustomerService
CampaignService
PointsService
IntelligenceService
NotificationService
```

### 2.6 Controllers — entidade + Controller

```
CustomerController
CampaignController
RewardController
```

---

## 3. CONVENÇÕES DE ARQUIVOS E PASTAS

```
# Arquivos TypeScript/NestJS
customer.entity.ts
customer.repository.ts
customer.service.ts
customer.controller.ts
customer.dto.ts
customer.module.ts

# Testes
customer.service.spec.ts
customer.repository.spec.ts

# Flutter
customer_profile_screen.dart
campaign_card_widget.dart
points_balance_component.dart
```

---

## 4. CONVENÇÕES DE BANCO DE DADOS

```sql
-- Tabelas: snake_case, plural
customers
campaigns
rewards
transactions
point_balances
customer_segments

-- Colunas: snake_case
customer_id
tenant_id
created_at
updated_at
churn_risk_score

-- Índices: idx_tabela_coluna
idx_customers_tenant_id
idx_transactions_customer_id
idx_campaigns_tenant_id_status
```

---

## 5. CONVENÇÕES DE API

```
# Endpoints: kebab-case, plural, RESTful
GET    /customers
GET    /customers/:id
POST   /customers
PUT    /customers/:id
DELETE /customers/:id

GET    /campaigns
POST   /campaigns
POST   /campaigns/:id/send

GET    /customers/:id/intelligence
GET    /customers/:id/transactions
GET    /customers/:id/rewards
```

---

## 6. CONVENÇÕES DE GIT

```
# Branches
feat/campaign-ai-recommendation
fix/customer-points-calculation
docs/engineering-bible
refactor/tenant-isolation-middleware
chore/update-dependencies

# Commits (Conventional Commits)
feat(campaigns): add AI recommendation engine
fix(points): correct rounding on cashback calculation
docs(adr): add decision record for multi-tenancy approach
refactor(customers): extract RFM calculation to domain service
test(campaigns): add integration tests for campaign dispatch
chore: update NestJS to v10.3
```

---

*Versão: 1.0 | Junho 2026*
