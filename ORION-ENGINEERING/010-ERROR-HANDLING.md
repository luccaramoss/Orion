# ORION ERROR HANDLING
## Como Tratamos Erros no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Todo erro é uma informação. Erros silenciados são bugs esperando para aparecer em produção.

No ORION, todo erro é capturado, categorizado, logado e comunicado de forma útil — para o desenvolvedor em logs, e para o usuário em linguagem humana.

---

## HIERARQUIA DE EXCEÇÕES

```typescript
// Base — shared/domain/domain-exception.ts
export class DomainException extends Error {
  constructor(
    public readonly message: string,
    public readonly code: string,
  ) {
    super(message);
    this.name = 'DomainException';
  }
}

// Exceções de domínio específicas
export class CustomerNotFoundException extends NotFoundException {
  constructor(id: string) {
    super({ code: 'CUSTOMER_NOT_FOUND', message: `Cliente ${id} não encontrado` });
  }
}

export class CustomerAlreadyExistsException extends ConflictException {
  constructor(email: string) {
    super({ code: 'CUSTOMER_ALREADY_EXISTS', message: `E-mail já cadastrado` });
    // ⚠️ Não expor o e-mail na mensagem de erro pública (privacidade)
  }
}

export class InsufficientPointsException extends UnprocessableEntityException {
  constructor(available: number, required: number) {
    super({
      code: 'INSUFFICIENT_POINTS',
      message: `Pontos insuficientes. Disponível: ${available}, necessário: ${required}`,
    });
  }
}

export class TenantNotFoundException extends UnauthorizedException {
  constructor() {
    super({ code: 'TENANT_NOT_FOUND', message: 'Tenant não identificado' });
  }
}
```

---

## FILTRO GLOBAL DE EXCEÇÕES

```typescript
// shared/interface/filters/global-exception.filter.ts
@Catch()
export class GlobalExceptionFilter implements ExceptionFilter {
  constructor(private readonly logger: Logger) {}

  catch(exception: unknown, host: ArgumentsHost): void {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();

    let status = HttpStatus.INTERNAL_SERVER_ERROR;
    let code = 'INTERNAL_ERROR';
    let message = 'Algo deu errado. Nossa equipe foi notificada.';

    if (exception instanceof HttpException) {
      status = exception.getStatus();
      const exceptionResponse = exception.getResponse() as any;
      code = exceptionResponse.code ?? 'HTTP_ERROR';
      message = exceptionResponse.message ?? exception.message;
    }

    // Log com contexto — sem PII
    this.logger.error({
      code,
      status,
      path: request.url,
      method: request.method,
      tenantId: request['tenantId'], // UUID apenas — sem dados pessoais
      error: exception instanceof Error ? exception.stack : String(exception),
    });

    response.status(status).json({
      success: false,
      error: { code, message },
    });
  }
}
```

---

## PADRÕES POR CAMADA

### Domain — lança DomainException
```typescript
class Customer {
  redeemReward(pointsCost: number): void {
    if (this.pointBalance.value < pointsCost) {
      throw new InsufficientPointsException(this.pointBalance.value, pointsCost);
    }
    this.pointBalance = this.pointBalance.subtract(pointsCost);
  }
}
```

### Application — captura e relança com contexto
```typescript
async execute(dto: RedeemRewardDto): Promise<void> {
  const customer = await this.customerRepository.findById(dto.tenantId, dto.customerId);
  if (!customer) throw new CustomerNotFoundException(dto.customerId);

  const reward = await this.rewardRepository.findById(dto.tenantId, dto.rewardId);
  if (!reward) throw new RewardNotFoundException(dto.rewardId);

  // Domain exception propagada naturalmente
  customer.redeemReward(reward.pointsCost);
  await this.customerRepository.save(customer);
}
```

### Infrastructure — captura erros externos e traduz
```typescript
async save(customer: Customer): Promise<void> {
  try {
    await this.repo.save(CustomerMapper.toPersistence(customer));
  } catch (error) {
    if (error.code === '23505') { // PostgreSQL unique violation
      throw new CustomerAlreadyExistsException(customer.email);
    }
    this.logger.error('Erro ao persistir customer', { error: error.message });
    throw new InternalServerErrorException('Erro ao salvar dados do cliente');
  }
}
```

---

## ERROS DE INTEGRAÇÃO EXTERNA

```typescript
async syncFromNuvemshop(tenantId: string): Promise<void> {
  try {
    const orders = await this.nuvemshopClient.getOrders(tenantId);
    // processar...
  } catch (error) {
    if (error.response?.status === 401) {
      throw new IntegrationAuthException('nuvemshop');
      // Dispara evento para notificar o Gestor de reconfigurar a integração
    }
    if (error.response?.status === 429) {
      // Rate limit — reagenda para daqui 60 segundos
      await this.syncQueue.add('retry-sync', { tenantId }, { delay: 60000 });
      return;
    }
    this.logger.error('Falha no sync Nuvemshop', { tenantId, error: error.message });
    // Não lança — falha silenciosa com log é aceitável para sync periódico
  }
}
```

---

## MENSAGENS DE ERRO PARA O USUÁRIO

| Código interno | Mensagem para o Gestor |
|----------------|----------------------|
| `CUSTOMER_NOT_FOUND` | "Cliente não encontrado. Verifique o ID informado." |
| `CUSTOMER_ALREADY_EXISTS` | "Já existe um cliente cadastrado com esse e-mail." |
| `INSUFFICIENT_POINTS` | "Pontos insuficientes para este resgate." |
| `CAMPAIGN_ALREADY_SENT` | "Esta campanha já foi enviada e não pode ser alterada." |
| `INTEGRATION_AUTH_ERROR` | "A integração com [serviço] precisa ser reconfigurada." |
| `INTERNAL_ERROR` | "Algo deu errado. Nossa equipe foi notificada." |

**Regra:** Nunca expor detalhes técnicos (stack trace, query, nome de tabela) em mensagens para o usuário.

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [011-LOGGING-POLICY.md](./011-LOGGING-POLICY.md)*
