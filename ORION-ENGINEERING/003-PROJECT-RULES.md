# ORION PROJECT RULES
## Regras Inegociáveis do Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

> Estas regras não tëm exceção. Para qualquer desvio, é necessário ADR aprovado pelos fundadores. Não há "mas nesse caso específico...".

---

## REGRAS DE SEGURANÇA

**R-SEC-01 — Isolamento de tenant é absoluto**  
Toda query ao banco de dados que acessa dados de tenants DEVE incluir `tenantId` como filtro. Não existe exceção. Nenhum endpoint retorna dados de tenants diferentes do tenant autenticado.

**R-SEC-02 — Zero secrets no repositório**  
Nenhuma chave de API, senha, connection string ou secret de qualquer natureza é commitado no repositório. Tudo em variáveis de ambiente. `.env` está no `.gitignore`. Nunca usar `.env.example` com valores reais.

**R-SEC-03 — Autenticação em todo endpoint privado**  
Todo endpoint que não está explicitamente marcado como público DEVE exigir JWT válido. Middleware de autenticação aplicado no nível do módulo, não do controller.

**R-SEC-04 — PII nunca em logs**  
Logs não contêm: nome, CPF, e-mail, telefone, endereço, dados de cartão ou qualquer informação pessoal identificável. Use IDs (UUIDs) nos logs para rastreabilidade sem exposição de dados.

**R-SEC-05 — Inputs sempre validados**  
Todo input externo (body, query, params) é validado via DTO com class-validator antes de chegar ao Use Case. Nunca confie em dados externos.

---

## REGRAS DE ARQUITETURA

**R-ARCH-01 — Domain não conhece Infrastructure**  
Entidades de domínio, Value Objects e Domain Services não importam nada de `typeorm`, `redis`, `axios` ou qualquer biblioteca de infraestrutura.

**R-ARCH-02 — Lógica de negócio fica no Domain**  
Controllers fazem: receber request → validar DTO → chamar Use Case → retornar response. Nunca contëm lógica de negócio. Use Cases orquestram o domínio. Domínio contém as regras.

**R-ARCH-03 — Módulos se comunicam via eventos**  
Um módulo não importa o Service de outro módulo diretamente. Comunicação entre módulos é feita via `EventEmitter` (eventos de domínio). Exceção: módulo `shared` pode ser importado por qualquer módulo.

**R-ARCH-04 — Repository implementa interface do domínio**  
Toda implementação de Repository implementa uma interface definida na camada de Domain. O Use Case depende da interface, nunca da implementação concreta.

**R-ARCH-05 — Nenhuma abstração prematura**  
Não crie abstrações antes de ter pelo menos dois casos de uso concretos que justifiquem. "Pode ser útil no futuro" não é justificativa.

---

## REGRAS DE CÓDIGO

**R-CODE-01 — Tipagem TypeScript completa**  
`any` é proibido sem comentário explícito justificando. `unknown` é preferível a `any`. Generics são bem-vindos quando reduzem duplicação com segurança de tipo.

**R-CODE-02 — Erros nunca são silenciados**  
`try/catch` vazio é proibido. Todo erro é logado ou relançado. Erros de domínio são lançados como exceções tipadas (`DomainException`, `NotFoundException`, `ConflictException`).

**R-CODE-03 — Funções puras no domínio**  
Funções de domínio são puras sempre que possível: mesmo input → mesmo output, sem efeitos colaterais. Efeitos colaterais (banco, cache, API externa) ficam na Infrastructure.

**R-CODE-04 — Nomes que documentam**  
Variáveis, funções e classes tëm nomes que explicam a intenção. Abreviações são proibidas (exceto convenções universais como `id`, `dto`, `i` em loops). Se você precisar comentar o nome, renomeie.

**R-CODE-05 — Funções com responsabilidade única**  
Uma função faz uma coisa. Se você consegue descrever o que ela faz com "e", ela faz coisas demais. Máximo recomendado: 30 linhas por função. Acima disso, avalie extração.

---

## REGRAS DE TESTES

**R-TEST-01 — Domínio tem cobertura mínima de 80%**  
Todo código na camada Domain tem cobertura de testes unitários de no mínimo 80%. Medido no CI a cada PR.

**R-TEST-02 — Fluxos críticos tëm teste de integração**  
Os fluxos: registro de compra, acúmulo de pontos, resgate de recompensa e disparo de campanha tëm testes de integração cobrindo o caminho feliz e os principais erros.

**R-TEST-03 — Testes são independentes**  
Cada teste é independente. Não depende de ordem de execução. Não deixa estado no banco de testes. Usa factories para criar dados de teste.

**R-TEST-04 — Mocks apenas na boundary**  
Mocks são usados apenas para simular dependências externas (banco, APIs externas, cache). Domínio é testado com implementações reais.

---

## REGRAS DE GIT

**R-GIT-01 — Commits seguem Conventional Commits**  
Formato: `tipo(escopo): descrição`. Tipos: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`. Exemplo: `feat(customers): add RFM segmentation logic`.

**R-GIT-02 — Nenhum commit direto na main**  
Todo código vai para main via Pull Request com ao menos um review aprovado.

**R-GIT-03 — Branches têm nome descritivo**  
Formato: `tipo/descricao-curta`. Exemplos: `feat/customer-churn-score`, `fix/points-rounding-error`.

**R-GIT-04 — PR tem descrição completa**  
Todo PR tem: o que foi feito, por que foi feito, como testar, screenshots se houver mudança visual, e checklist de review preenchido.

---

## REGRAS DE DOCUMENTAÇÃO

**R-DOC-01 — Todo módulo tem README**  
Todo módulo em `src/modules/` tem um `README.md` explicando: propósito, entidades principais, use cases disponíveis, eventos emitidos e consumidos, e dependências externas.

**R-DOC-02 — Toda API tem OpenAPI**  
Todo endpoint tem decorators `@ApiOperation`, `@ApiResponse` e `@ApiBody` completos. Documentação gerada automaticamente via Swagger.

**R-DOC-03 — Decisões arquiteturais tëm ADR**  
Toda decisão de alto impacto (mudança de stack, nova abstração arquitetural, mudança de banco) tem ADR em `docs/adr/`.

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [004-CODING-STANDARDS.md](./004-CODING-STANDARDS.md)*
