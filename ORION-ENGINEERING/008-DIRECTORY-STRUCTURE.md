# ORION DIRECTORY STRUCTURE
## Como o Projeto está Organizado

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## ESTRUTURA DO MONOREPO

```
orion/                                    # Raiz do monorepo
├── apps/
│   ├── api/                            # Backend NestJS
│   ├── mobile/                           # App Flutter (Cliente)
│   └── panel/                           # Painel do Gestor (web)
├── packages/
│   ├── shared-types/                     # Types TypeScript compartilhados
│   └── shared-utils/                     # Utilitários sem dependências de framework
├── docs/
│   ├── adr/                            # Architecture Decision Records
│   ├── api/                             # Documentação OpenAPI gerada
│   └── diagrams/                         # Diagramas de arquitetura (C4, ERD, fluxo)
├── scripts/
│   ├── seed/                             # Scripts de seed para desenvolvimento
│   └── migrations/                       # Scripts de migração manual (se necessário)
├── .github/
│   ├── workflows/                        # CI/CD pipelines
│   └── PULL_REQUEST_TEMPLATE.md
├── ORION-DNA/                            # Documentos fundacionais (Camada 1)
├── ORION-ENGINEERING/                    # Documentos de engenharia (Camada 2)
├── .env.example                          # Variáveis de ambiente (sem valores reais)
├── docker-compose.yml                    # Ambiente local de desenvolvimento
├── docker-compose.test.yml               # Ambiente de testes
└── README.md                             # Entrada do projeto
```

---

## ESTRUTURA DO BACKEND (apps/api/)

```
apps/api/
├── src/
│   ├── modules/                          # Módulos de domínio
│   │   ├── auth/
│   │   │   ├── domain/
│   │   │   ├── application/
│   │   │   ├── infrastructure/
│   │   │   ├── interface/
│   │   │   ├── auth.module.ts
│   │   │   └── README.md
│   │   │
│   │   ├── tenants/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── customers/
│   │   │   ├── domain/
│   │   │   │   ├── entities/
│   │   │   │   │   └── customer.entity.ts
│   │   │   │   ├── value-objects/
│   │   │   │   │   ├── customer-email.vo.ts
│   │   │   │   │   └── customer-phone.vo.ts
│   │   │   │   ├── events/
│   │   │   │   │   ├── customer-registered.event.ts
│   │   │   │   │   └── customer-updated.event.ts
│   │   │   │   ├── exceptions/
│   │   │   │   │   ├── customer-not-found.exception.ts
│   │   │   │   │   └── customer-already-exists.exception.ts
│   │   │   │   └── repositories/
│   │   │   │       └── customer.repository.interface.ts
│   │   │   ├── application/
│   │   │   │   └── use-cases/
│   │   │   │       ├── register-customer.use-case.ts
│   │   │   │       ├── register-customer.use-case.spec.ts
│   │   │   │       ├── update-customer.use-case.ts
│   │   │   │       ├── find-customer.use-case.ts
│   │   │   │       └── list-customers.use-case.ts
│   │   │   ├── infrastructure/
│   │   │   │   ├── persistence/
│   │   │   │   │   ├── customer.orm-entity.ts
│   │   │   │   │   ├── customer.repository.ts
│   │   │   │   │   └── customer.mapper.ts
│   │   │   │   └── subscribers/
│   │   │   │       └── customer-registered.subscriber.ts
│   │   │   ├── interface/
│   │   │   │   ├── controllers/
│   │   │   │   │   └── customer.controller.ts
│   │   │   │   └── dtos/
│   │   │   │       ├── register-customer.dto.ts
│   │   │   │       ├── update-customer.dto.ts
│   │   │   │       └── customer-response.dto.ts
│   │   │   ├── customers.module.ts
│   │   │   └── README.md
│   │   │
│   │   ├── campaigns/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── rewards/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── points/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── transactions/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── intelligence/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   ├── notifications/
│   │   │   └── [mesma estrutura]
│   │   │
│   │   └── integrations/
│   │       ├── nuvemshop/
│   │       ├── bling/
│   │       ├── whatsapp/
│   │       └── integrations.module.ts
│   │
│   ├── shared/
│   │   ├── domain/
│   │   │   ├── base-entity.ts           # Base class para entities
│   │   │   ├── aggregate-root.ts        # Base class para aggregate roots
│   │   │   ├── domain-event.ts          # Base class para domain events
│   │   │   ├── domain-exception.ts      # Base exception para domain
│   │   │   └── value-object.ts          # Base class para value objects
│   │   ├── infrastructure/
│   │   │   ├── base-repository.ts       # Implementação base de repository
│   │   │   └── database/
│   │   │       ├── database.module.ts
│   │   │       └── migrations/
│   │   ├── interface/
│   │   │   ├── decorators/
│   │   │   │   ├── current-tenant.decorator.ts
│   │   │   │   └── current-user.decorator.ts
│   │   │   ├── guards/
│   │   │   │   ├── jwt-auth.guard.ts
│   │   │   │   └── tenant.guard.ts
│   │   │   ├── filters/
│   │   │   │   └── global-exception.filter.ts
│   │   │   └── interceptors/
│   │   │       ├── logging.interceptor.ts
│   │   │       └── tenant-context.interceptor.ts
│   │   └── utils/
│   │       ├── date.utils.ts
│   │       └── string.utils.ts
│   │
│   ├── config/
│   │   ├── app.config.ts
│   │   ├── database.config.ts
│   │   ├── redis.config.ts
│   │   └── openai.config.ts
│   │
│   ├── app.module.ts
│   └── main.ts
│
├── test/
│   ├── factories/                        # Factories para testes
│   ├── helpers/                          # Helpers de teste (setup de banco, etc.)
│   └── e2e/                             # Testes end-to-end
│
├── package.json
├── tsconfig.json
├── nest-cli.json
└── README.md
```

---

## ESTRUTURA DO APP FLUTTER (apps/mobile/)

```
apps/mobile/
├── lib/
│   ├── core/
│   │   ├── config/                       # Configuração do app
│   │   ├── constants/                    # Constantes globais
│   │   ├── errors/                       # Classes de erro
│   │   ├── network/                      # HTTP client configurado
│   │   ├── router/                       # Rotas do app (go_router)
│   │   └── theme/                        # Tema e design tokens
│   │
│   ├── features/
│   │   ├── auth/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   ├── presentation/
│   │   │   └── auth.providers.dart
│   │   │
│   │   ├── home/
│   │   ├── rewards/
│   │   ├── points/
│   │   ├── profile/
│   │   └── notifications/
│   │
│   ├── shared/
│   │   ├── widgets/                      # Widgets reutilizáveis
│   │   ├── extensions/                   # Dart extensions
│   │   └── utils/                        # Utilitários
│   │
│   └── main.dart
│
├── assets/
│   ├── images/
│   ├── fonts/
│   └── icons/
│
├── test/
└── pubspec.yaml
```

---

## DOCS — ADR

```
docs/adr/
├── ADR-001-multi-tenancy-strategy.md
├── ADR-002-backend-framework-choice.md
├── ADR-003-mobile-framework-choice.md
├── ADR-004-database-choice.md
├── ADR-005-caching-strategy.md
└── [ADR-NNN-titulo.md]
```

---

## REGRAS DE ESTRUTURA

1. **Nunca criar arquivos na raiz de `src/`** — tudo em módulo ou shared
2. **Nunca criar módulo sem README.md**
3. **Nunca misturar código de dois módulos diferentes no mesmo arquivo**
4. **Shared é para código sem dependência de módulo** — se depende de um módulo específico, não é shared
5. **Migrations ficam em `shared/infrastructure/database/migrations/`** — nunca em pasta de módulo

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [009-DEPENDENCY-POLICY.md](./009-DEPENDENCY-POLICY.md)*
