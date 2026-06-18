# ORION LOGGING POLICY
## Como Fazemos Logging no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Log é a memória do sistema em produção. Sem log, não há diagnóstico. Com log ruim, há confusão. Com log bom, há visibilidade.

---

## NÍVEIS DE LOG

| Nível | Quando usar |
|-------|-------------|
| `error` | Erros não recuperáveis que exigem atenção imediata |
| `warn` | Situações anômalas que não quebraram o fluxo, mas merecem atenção |
| `info` | Eventos importantes do ciclo de vida do sistema |
| `debug` | Informações detalhadas úteis apenas em desenvolvimento |

**Regra:** Em produção, nível mínimo é `info`. `debug` apenas em desenvolvimento local.

---

## O QUE SEMPRE LOGAR

```typescript
// ✅ Início e fim de operações críticas
logger.info({ event: 'campaign.dispatch.started', campaignId, tenantId, segmentSize });
// ... processamento ...
logger.info({ event: 'campaign.dispatch.completed', campaignId, tenantId, sent: 214, duration: '1.2s' });

// ✅ Erros com contexto (sem PII)
logger.error({
  event: 'customer.registration.failed',
  tenantId,
  error: error.message,
  stack: error.stack,
});

// ✅ Integrações externas
logger.info({ event: 'nuvemshop.sync.started', tenantId, orderCount: 45 });
logger.warn({ event: 'nuvemshop.sync.rate_limited', tenantId, retryAfter: 60 });

// ✅ Autenticação (sem senha, sem token)
logger.info({ event: 'auth.login.success', tenantId, userId });
logger.warn({ event: 'auth.login.failed', tenantId, reason: 'invalid_credentials' });
```

---

## O QUE NUNCA LOGAR

```typescript
// ❌ PII — jamais
logger.info({ customerName: 'Maria Silva' });      // nome
logger.info({ email: 'maria@email.com' });         // e-mail
logger.info({ cpf: '123.456.789-00' });            // CPF
logger.info({ phone: '+5511999999999' });           // telefone
logger.info({ token: req.headers.authorization }); // token JWT

// ✅ Use IDs (UUIDs) para rastreabilidade sem exposição
logger.info({ customerId: 'uuid-aqui', tenantId: 'uuid-aqui' });
```

---

## ESTRUTURA DO LOG

Todo log segue estrutura JSON para facilitar indexação no Elasticsearch:

```json
{
  "timestamp": "2026-06-17T10:30:00.000Z",
  "level": "info",
  "service": "orion-api",
  "module": "campaigns",
  "event": "campaign.dispatch.completed",
  "tenantId": "uuid",
  "campaignId": "uuid",
  "sent": 214,
  "duration": "1.2s",
  "traceId": "uuid"
}
```

**Campos obrigatórios em todo log:**
- `timestamp` — gerado automaticamente pelo Winston
- `level` — nível do log
- `service` — nome do serviço (`orion-api`, `orion-worker`)
- `module` — módulo NestJS de origem
- `event` — identificador do evento em `dominio.acao.resultado`
- `tenantId` — sempre que disponível no contexto

**Campos opcionais mas recomendados:**
- `traceId` — para correlacionar logs de um mesmo request
- `duration` — para operações com tempo relevante
- `error` — mensagem de erro (sem stack em produção no nível info)
- `stack` — stack trace apenas em `error` level

---

## CONFIGURAÇÃO DO WINSTON

```typescript
// config/logger.config.ts
import { WinstonModule } from 'nest-winston';
import { format, transports } from 'winston';

export const loggerConfig = WinstonModule.createLogger({
  level: process.env.LOG_LEVEL ?? 'info',
  format: format.combine(
    format.timestamp(),
    format.errors({ stack: true }),
    process.env.NODE_ENV === 'development'
      ? format.prettyPrint()
      : format.json(),
  ),
  transports: [
    new transports.Console(),
    // Em produção: adicionar transporte para Elasticsearch ou serviço de logs
  ],
  defaultMeta: { service: 'orion-api' },
});
```

---

## USO NO CÓDIGO

```typescript
// Em módulos NestJS — injetar Logger com contexto
@Injectable()
export class CampaignService {
  private readonly logger = new Logger(CampaignService.name);

  async dispatch(campaignId: string, tenantId: string): Promise<void> {
    this.logger.log(
      JSON.stringify({ event: 'campaign.dispatch.started', campaignId, tenantId })
    );
    // ...
  }
}
```

---

## RETENÇÃO DE LOGS

| Ambiente | Nível | Retenção |
|----------|-------|----------|
| Produção | error, warn | 90 dias |
| Produção | info | 30 dias |
| Staging | todos | 7 dias |
| Desenvolvimento | todos | local apenas |

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [012-TESTING-POLICY.md](./012-TESTING-POLICY.md)*
