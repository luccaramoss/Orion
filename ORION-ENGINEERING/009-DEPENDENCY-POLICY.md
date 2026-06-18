# ORION DEPENDENCY POLICY
## Como Adicionamos e Gerenciamos Dependências

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Cada dependência adicionada é uma dívida que pagaremos para sempre: em atualizações, vulnerabilidades, breaking changes e curva de aprendizado.

Adicionamos dependências quando o custo de não ter é maior que o custo de manter.

---

## ANTES DE ADICIONAR QUALQUER DEPENDÊNCIA

Responda às 5 perguntas:

1. **Existe no ecossistema ORION?** Verifique se já existe algo similar em uso. Não adicione duas bibliotecas para o mesmo propósito.

2. **Conseguimos implementar em < 2 horas?** Se sim, implemente. Não adicione biblioteca para algo trivial.

3. **A biblioteca está ativa?** Último commit há menos de 6 meses. Stars > 500 (para libs de propósito geral). Issues respondidas.

4. **A licença é compatível?** MIT, Apache 2.0, BSD — ok. GPL — requer avaliação. Licenças proprietárias — requer aprovação dos fundadores.

5. **Tem vulnerabilidades conhecidas?** Verifique com `npm audit` ou `pub outdated` antes de adicionar.

---

## DEPENDÊNCIAS APROVADAS (backend)

| Pacote | Propósito | Versão mínima |
|--------|-----------|---------------|
| `@nestjs/core` | Framework principal | ^10 |
| `@nestjs/typeorm` | ORM integration | ^10 |
| `typeorm` | ORM | ^0.3 |
| `@nestjs/bull` | Filas assíncronas | ^10 |
| `bull` | Queue engine | ^4 |
| `ioredis` | Redis client | ^5 |
| `@nestjs/jwt` | JWT authentication | ^10 |
| `@nestjs/passport` | Auth middleware | ^10 |
| `passport-jwt` | JWT strategy | ^4 |
| `class-validator` | DTO validation | ^0.14 |
| `class-transformer` | DTO transformation | ^0.5 |
| `@nestjs/swagger` | OpenAPI docs | ^7 |
| `@nestjs/config` | Config management | ^3 |
| `@nestjs/event-emitter` | Domain events | ^2 |
| `openai` | OpenAI API client | ^4 |
| `uuid` | UUID generation | ^9 |
| `bcrypt` | Password hashing | ^5 |
| `winston` | Logging | ^3 |
| `nest-winston` | Winston integration | ^1 |

---

## DEPENDÊNCIAS APROVADAS (Flutter)

| Pacote | Propósito |
|--------|-----------|
| `flutter_riverpod` | State management |
| `go_router` | Navegação |
| `dio` | HTTP client |
| `firebase_messaging` | Push notifications |
| `firebase_auth` | Autenticação mobile |
| `flutter_secure_storage` | Storage seguro de tokens |
| `cached_network_image` | Cache de imagens |
| `intl` | Internacionalização e formatação |
| `json_annotation` | Serialização JSON |
| `freezed` | Value objects imutáveis |

---

## DEPENDÊNCIAS PROIBIDAS

| Categoria | Motivo |
|-----------|--------|
| Múltiplas libs de HTTP client | Use apenas `axios` (backend) ou `dio` (Flutter) |
| Múltiplos ORMs | Use apenas TypeORM |
| `moment.js` | Usar `date-fns` — tree-shakeable e menor |
| `lodash` completo | Importar funções específicas ou implementar |
| Qualquer lib de cripto não auditada | Segurança — aprovação obrigatória |
| Libs com última atualização > 2 anos sem substituto ativo | Risco de abandono |

---

## PROCESSO DE ADIÇÃO

1. Abrir issue ou discussão: "Proposta: adicionar [lib] para [propósito]"
2. Responder as 5 perguntas acima na issue
3. Aprovação de ao menos um revisor
4. Adicionar à lista de aprovadas neste documento
5. Commit: `chore: add [lib] for [propósito]`

---

## ATUALIZAÇÃO DE DEPENDÊNCIAS

- **Patch** (`^1.0.x`): atualização automática mensal via Dependabot/Renovate
- **Minor** (`^1.x.0`): revisão manual trimestral
- **Major**: ADR obrigatório documentando breaking changes e estratégia de migração

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [010-ERROR-HANDLING.md](./010-ERROR-HANDLING.md)*
