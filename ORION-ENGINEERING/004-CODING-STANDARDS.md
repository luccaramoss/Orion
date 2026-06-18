# ORION CODING STANDARDS
## Como Escrevemos Código no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## 1. TYPESCRIPT

### 1.1 Tipagem

```typescript
// ❌ Proibido
function processCustomer(data: any): any { }

// ✅ Correto
function processCustomer(data: RegisterCustomerDto): CustomerResponseDto { }

// ✅ Correto — generic com constraint
function findOrThrow<T extends { id: string }>(items: T[], id: string): T { }

// ✅ Permitido com justificativa explícita
// eslint-disable-next-line @typescript-eslint/no-explicit-any
const externalApiResponse: any = await thirdPartyClient.call(); // API externa sem tipagem
```

### 1.2 Interfaces vs Types

```typescript
// Use interface para shapes de objetos (extensível)
interface CustomerProps {
  id: string;
  tenantId: string;
  name: string;
}

// Use type para unions, intersections e aliases
type CustomerStatus = 'active' | 'inactive' | 'churned';
type CustomerWithSegment = Customer & { segment: Segment };
```

### 1.3 Enums

```typescript
// ✅ Const enums para valores de domínio
export const CustomerStatus = {
  ACTIVE: 'active',
  INACTIVE: 'inactive',
  CHURNED: 'churned',
} as const;

export type CustomerStatus = typeof CustomerStatus[keyof typeof CustomerStatus];
```

---

## 2. NESTJS

### 2.1 Injeção de dependência

```typescript
// ✅ Sempre injetar interfaces, nunca implementações concretas
@Injectable()
export class RegisterCustomerUseCase {
  constructor(
    @Inject(CUSTOMER_REPOSITORY)
    private readonly customerRepository: ICustomerRepository,
  ) {}
}

// Token de injeção definido em constante
export const CUSTOMER_REPOSITORY = Symbol('ICustomerRepository');
```

### 2.2 Controllers

```typescript
@Controller('customers')
@UseGuards(JwtAuthGuard, TenantGuard)
@ApiTags('Customers')
export class CustomerController {
  constructor(private readonly registerCustomer: RegisterCustomerUseCase) {}

  @Post()
  @ApiOperation({ summary: 'Registra um novo Cliente' })
  @ApiResponse({ status: 201, type: CustomerResponseDto })
  @ApiResponse({ status: 409, description: 'Cliente já existe' })
  async register(
    @Body() dto: RegisterCustomerDto,
    @CurrentTenant() tenantId: string,
  ): Promise<ApiResponse<CustomerResponseDto>> {
    const result = await this.registerCustomer.execute({ ...dto, tenantId });
    return { success: true, data: result };
  }
}
```

### 2.3 DTOs

```typescript
export class RegisterCustomerDto {
  @IsString()
  @IsNotEmpty()
  @MaxLength(100)
  @ApiProperty({ example: 'Maria Silva' })
  name: string;

  @IsEmail()
  @ApiProperty({ example: 'maria@email.com' })
  email: string;

  @IsString()
  @Matches(/^\+?[1-9]\d{10,14}$/)
  @ApiProperty({ example: '+5511999999999' })
  phone: string;
}
```

---

## 3. ESTRUTURA DE MÓDULO

Cada módulo segue esta estrutura sem variação:

```
src/modules/customers/
├── domain/
│   ├── entities/
│   │   └── customer.entity.ts
│   ├── value-objects/
│   │   └── customer-email.vo.ts
│   ├── events/
│   │   └── customer-registered.event.ts
│   ├── exceptions/
│   │   └── customer-not-found.exception.ts
│   └── repositories/
│       └── customer.repository.interface.ts
├── application/
│   └── use-cases/
│       ├── register-customer.use-case.ts
│       └── register-customer.use-case.spec.ts
├── infrastructure/
│   ├── persistence/
│   │   ├── customer.orm-entity.ts
│   │   ├── customer.repository.ts
│   │   └── customer.mapper.ts
│   └── subscribers/
│       └── customer-registered.subscriber.ts
├── interface/
│   ├── controllers/
│   │   └── customer.controller.ts
│   └── dtos/
│       ├── register-customer.dto.ts
│       └── customer-response.dto.ts
├── customers.module.ts
└── README.md
```

---

## 4. TRATAMENTO DE ERROS

```typescript
// Exceções de domínio tipadas
export class CustomerNotFoundException extends NotFoundException {
  constructor(id: string) {
    super({
      code: 'CUSTOMER_NOT_FOUND',
      message: `Cliente com id ${id} não encontrado`,
    });
  }
}

export class CustomerAlreadyExistsException extends ConflictException {
  constructor(email: string) {
    super({
      code: 'CUSTOMER_ALREADY_EXISTS',
      message: `Já existe um Cliente com o e-mail ${email}`,
    });
  }
}

// ❌ Nunca silenciar erros
try {
  await this.doSomething();
} catch (e) {
  // vazio — PROIBIDO
}

// ✅ Sempre tratar ou relançar
try {
  await this.doSomething();
} catch (error) {
  this.logger.error('Falha ao processar', { error: error.message, customerId });
  throw new InternalServerErrorException('Falha ao processar o cliente');
}
```

---

## 5. ASYNC/AWAIT

```typescript
// ✅ Sempre await em funções assíncronas
const customer = await this.customerRepository.findById(tenantId, id);

// ✅ Promise.all para operações paralelas independentes
const [customer, segment] = await Promise.all([
  this.customerRepository.findById(tenantId, customerId),
  this.segmentRepository.findByCustomer(tenantId, customerId),
]);

// ❌ Nunca misturar .then() com await no mesmo fluxo
const customer = await this.repo.find(id).then(c => c); // desnecessário e confuso
```

---

## 6. FLUTTER

### 6.1 Estrutura de widgets

```dart
// ✅ Widgets pequenos e com responsabilidade única
class PointsBalanceCard extends StatelessWidget {
  const PointsBalanceCard({
    super.key,
    required this.balance,
    required this.onRedeem,
  });

  final int balance;
  final VoidCallback onRedeem;

  @override
  Widget build(BuildContext context) {
    return Card(
      child: Column(
        children: [
          Text('$balance pontos', style: Theme.of(context).textTheme.headlineMedium),
          ElevatedButton(onPressed: onRedeem, child: const Text('Resgatar')),
        ],
      ),
    );
  }
}
```

### 6.2 State management

Usar **Riverpod** como solução de state management. Providers são definidos próximos à feature que os utiliza.

### 6.3 Nomenclatura Flutter

```dart
// Arquivos: snake_case
customer_profile_screen.dart
points_balance_card.dart

// Classes: PascalCase
class CustomerProfileScreen extends StatefulWidget {}
class PointsBalanceCard extends StatelessWidget {}

// Variáveis e funções: camelCase
final int pointsBalance;
void handleRedemption() {}
```

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [005-NAMING-CONVENTIONS.md](./005-NAMING-CONVENTIONS.md)*
