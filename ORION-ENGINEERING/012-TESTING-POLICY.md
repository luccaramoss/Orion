# ORION TESTING POLICY
## Como Testamos o Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Código sem teste é código que funciona por acidente. No ORION, testes são parte do desenvolvimento — não uma etapa após.

---

## PIRÂMIDE DE TESTES

```
         /\
        /  \
       / E2E \         ← Poucos, lentos, testam fluxos críticos completos
      /________\
     /          \
    / Integration \    ← Médios, testam módulos com banco/cache reais
   /______________\
  /                \
 /   Unit Tests     \  ← Muitos, rápidos, testam domínio isolado
/____________________ \
```

---

## 1. TESTES UNITÁRIOS (Domínio)

**O que testamos:** Entities, Value Objects, Domain Services — lógica de negócio pura.  
**Cobertura mínima:** 80% do código em `domain/`  
**Ferramentas:** Jest  
**Velocidade:** < 1ms por teste  
**Dependências externas:** Nenhuma — tudo mockado ou in-memory

```typescript
// customers/application/use-cases/register-customer.use-case.spec.ts
describe('RegisterCustomerUseCase', () => {
  let useCase: RegisterCustomerUseCase;
  let customerRepository: MockCustomerRepository;

  beforeEach(() => {
    customerRepository = new MockCustomerRepository();
    useCase = new RegisterCustomerUseCase(customerRepository, new MockEventEmitter());
  });

  it('deve registrar um novo cliente com sucesso', async () => {
    const dto = {
      tenantId: 'tenant-uuid',
      name: 'Maria Silva',
      email: 'maria@email.com',
      phone: '+5511999999999',
    };

    const result = await useCase.execute(dto);

    expect(result.name).toBe('Maria Silva');
    expect(customerRepository.saved).toHaveLength(1);
  });

  it('deve lançar exceção quando e-mail já existe', async () => {
    customerRepository.existingEmail = 'maria@email.com';

    await expect(useCase.execute({
      tenantId: 'tenant-uuid',
      name: 'Maria Silva',
      email: 'maria@email.com',
      phone: '+5511999999999',
    })).rejects.toThrow(CustomerAlreadyExistsException);
  });

  it('deve emitir evento CustomerRegistered após registro', async () => {
    const eventEmitter = new MockEventEmitter();
    useCase = new RegisterCustomerUseCase(customerRepository, eventEmitter);

    await useCase.execute({ tenantId: 't', name: 'Maria', email: 'maria@test.com', phone: '+5511' });

    expect(eventEmitter.emittedEvents).toContainEqual(
      expect.objectContaining({ name: 'customer.registered' })
    );
  });
});

// Testando Entity de domínio diretamente
describe('Customer Entity', () => {
  it('deve acumular pontos corretamente', () => {
    const customer = Customer.create({ tenantId: 't', name: 'Maria', email: 'x@x.com', phone: '11' });
    customer.accruePoints(100);
    expect(customer.pointBalance).toBe(100);
  });

  it('deve lançar exceção ao resgatar com pontos insuficientes', () => {
    const customer = Customer.create({ tenantId: 't', name: 'Maria', email: 'x@x.com', phone: '11' });
    customer.accruePoints(50);
    expect(() => customer.redeemReward(100)).toThrow(InsufficientPointsException);
  });
});
```

---

## 2. TESTES DE INTEGRAÇÃO

**O que testamos:** Repositórios com banco real, Workers com Redis real, Integrações com APIs (usando servidor mock).  
**Ferramentas:** Jest + TypeORM com banco de teste PostgreSQL + `@testcontainers/postgresql`  
**Banco:** PostgreSQL em container Docker para testes

```typescript
// customers/infrastructure/repositories/customer.repository.spec.ts
describe('CustomerRepository (Integration)', () => {
  let dataSource: DataSource;
  let repository: CustomerRepository;

  beforeAll(async () => {
    dataSource = await createTestDataSource(); // PostgreSQL em memória ou container
    repository = new CustomerRepository(dataSource.getRepository(CustomerOrmEntity));
  });

  afterAll(() => dataSource.destroy());
  afterEach(() => dataSource.query('DELETE FROM customers WHERE tenant_id = $1', ['test-tenant']));

  it('deve salvar e recuperar um customer', async () => {
    const customer = Customer.create({
      tenantId: 'test-tenant',
      name: 'Maria',
      email: 'maria@test.com',
      phone: '+5511',
    });

    await repository.save(customer);
    const found = await repository.findByEmail('test-tenant', 'maria@test.com');

    expect(found).not.toBeNull();
    expect(found!.name).toBe('Maria');
  });

  it('deve respeitar isolamento de tenant', async () => {
    // Salva customer no tenant A
    await repository.save(Customer.create({ tenantId: 'tenant-a', ... }));

    // Busca no tenant B — deve retornar null
    const found = await repository.findByEmail('tenant-b', 'maria@test.com');
    expect(found).toBeNull();
  });
});
```

---

## 3. TESTES E2E

**O que testamos:** Fluxos críticos de ponta a ponta via HTTP.  
**Fluxos obrigatórios:**
- Registro de Cliente → acúmulo de pontos → resgate de recompensa
- Criação de campanha → disparo → confirmação de envio
- Sync de integração Nuvemshop → criação de transação

```typescript
// test/e2e/customer-journey.e2e.spec.ts
describe('Customer Journey (E2E)', () => {
  let app: INestApplication;

  beforeAll(async () => {
    app = await createTestApp();
  });

  afterAll(() => app.close());

  it('deve completar o fluxo: registro → compra → pontos → resgate', async () => {
    // 1. Registrar cliente
    const { body: customer } = await request(app.getHttpServer())
      .post('/api/v1/customers')
      .set('Authorization', `Bearer ${testJwt}`)
      .set('x-tenant-id', testTenantId)
      .send({ name: 'Maria Silva', email: 'maria@test.com', phone: '+5511999999999' })
      .expect(201);

    // 2. Registrar compra (via integração ou endpoint direto)
    await request(app.getHttpServer())
      .post('/api/v1/transactions')
      .set('Authorization', `Bearer ${testJwt}`)
      .set('x-tenant-id', testTenantId)
      .send({ customerId: customer.data.id, amount: 150.00, source: 'pdv' })
      .expect(201);

    // 3. Verificar pontos acumulados
    const { body: intelligence } = await request(app.getHttpServer())
      .get(`/api/v1/customers/${customer.data.id}/points`)
      .set('Authorization', `Bearer ${testJwt}`)
      .set('x-tenant-id', testTenantId)
      .expect(200);

    expect(intelligence.data.balance).toBeGreaterThan(0);
  });
});
```

---

## FACTORIES DE TESTE

```typescript
// test/factories/customer.factory.ts
export class CustomerFactory {
  static create(overrides: Partial<CustomerProps> = {}): Customer {
    return Customer.create({
      tenantId: 'default-tenant-uuid',
      name: 'Cliente Teste',
      email: `test-${Date.now()}@orion.test`,
      phone: '+5511999999999',
      ...overrides,
    });
  }

  static createMany(count: number, overrides: Partial<CustomerProps> = {}): Customer[] {
    return Array.from({ length: count }, (_, i) =>
      CustomerFactory.create({ email: `test-${i}-${Date.now()}@orion.test`, ...overrides })
    );
  }
}
```

---

## COBERTURA MÍNIMA POR CAMADA

| Camada | Cobertura mínima | Tipo de teste |
|--------|-----------------|---------------|
| `domain/entities` | 90% | Unitário |
| `domain/value-objects` | 90% | Unitário |
| `application/use-cases` | 80% | Unitário |
| `infrastructure/repositories` | 70% | Integração |
| `interface/controllers` | 60% | E2E ou Integração |

**Verificado no CI:** `jest --coverage --coverageThreshold='{"./src/modules/**/domain/**":{"lines":80}}'`

---

## REGRAS

1. Nenhum PR mergeado com cobertura abaixo do mínimo
2. Testes que dependem de ordem de execução são reescritos
3. `setTimeout` ou `sleep` em testes são proibidos — use mocks de tempo
4. Banco de testes é limpo entre testes de integração (`afterEach`)
5. Testes E2E rodam em pipeline separado (mais lentos)

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [013-GIT-WORKFLOW.md](./013-GIT-WORKFLOW.md)*
