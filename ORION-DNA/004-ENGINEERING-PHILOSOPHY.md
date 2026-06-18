# ORION ENGINEERING PHILOSOPHY
## Como Nossa Engenharia Pensa

**Volume:** ORION DNA — Camada 1: Fundação

---

> *"Nunca criar código inteligente. Criar código simples. Arquitetura vence velocidade. Legibilidade vence complexidade. Documentação vence memória. Automação vence repetição."*

---

## 1. A FILOSOFIA CENTRAL

Engenharia no ORION não é sobre escrever código elegante.

É sobre construir um sistema que funciona hoje, em 6 meses e em 3 anos — com ou sem o desenvolvedor original presente.

Isso exige disciplina, padrões e documentação. Não como burocracia, mas como respeito ao trabalho de quem vem depois.

---

## 2. OS MANDAMENTOS DA ENGENHARIA ORION

### Mandamento 1 — Código simples vence código inteligente

Se você precisar comentar um trecho de código para explicar o que ele faz, reescreva o código.

O código deve ser tão claro que a intenção seja óbvia apenas pela leitura.

Truques de linguagem, one-liners crípticos e abstrações prematuras são proibidos. Clareza é a virtude máxima.

### Mandamento 2 — Arquitetura é decisão, não consequência

A arquitetura do ORION foi pensada antes de uma linha de código ser escrita.

Mudanças arquiteturais exigem ADR (Architecture Decision Record) documentado e aprovado.

Nunca deixamos a arquitetura "emergir" do código — ela é definida, documentada e seguida.

### Mandamento 3 — Documentação não é opcional

Todo módulo tem README.  
Toda decisão não-óbvia tem comentário.  
Toda API tem documentação OpenAPI.  
Todo ADR está na pasta `/docs/adr`.

Código sem documentação é código incompleto.

### Mandamento 4 — Testes não são extras

Nenhum código de domínio vai para produção sem teste unitário.  
Nenhum fluxo crítico vai para produção sem teste de integração.  
Nenhuma API pública vai para produção sem teste de contrato.

Cobertura mínima: 80% nos módulos de domínio.

### Mandamento 5 — A consistência é inegociável

Todos os desenvolvedores (e o Claude) seguem os mesmos padrões — independente de quem escreveu o código.

Não existe "meu estilo". Existe o estilo do ORION.

Padrões estão documentados em `CODING_STANDARDS.md`, `NAMING_CONVENTIONS.md` e `DIRECTORY_STRUCTURE.md`.

### Mandamento 6 — Segurança não é feature de v2

Autenticação, autorização, validação de inputs e isolamento de tenant são requisitos de v1.0.

Segurança não é adicionada depois. É construída desde o primeiro commit.

### Mandamento 7 — Logging e observabilidade desde o início

Toda ação crítica é logada.  
Todo erro é capturado e rastreável.  
Todo endpoint tem tempo de resposta monitorado.

Um sistema que não conseguimos observar é um sistema que não conseguimos operar.

---

## 3. STACK TECNOLÓGICA

> Decisões de stack são ADRs. Mudanças exigem justificativa e aprovação.

| Camada | Tecnologia | Justificativa |
|--------|-----------|---------------|
| Mobile | Flutter | Cross-platform, performance nativa, único codebase |
| Backend | NestJS (Node.js/TypeScript) | Arquitetura modular, DI nativa, ecossistema maduro |
| Banco de dados | PostgreSQL | Confiabilidade, suporte a JSON, extensões (pgvector para IA) |
| Cache | Redis | Performance para sessões, filas e dados frequentes |
| Busca | Elasticsearch | Consultas complexas de clientes e comportamento |
| Mensageria | Bull (Redis Queue) | Processamento assíncrono de campanhas e eventos |
| Auth | JWT + Refresh Token | Stateless, escalável, padrão de mercado |
| Infra | Firebase (Auth, Push, Storage) | Reduz complexidade de infra para MVP |
| IA | OpenAI API (GPT-4o) | Qualidade de linguagem e raciocínio |
| Integrações | Nuvemshop, Bling, WhatsApp Business API | Ecossistema alvo |
| Monitoramento | (a definir em ADR) | |

---

## 4. PRINCÍPIOS ARQUITETURAIS

### Multi-tenancy isolado

Cada tenant tem seus dados completamente isolados. Isolamento por schema no PostgreSQL (schema-per-tenant) ou por campo tenant_id com RLS (Row Level Security) — decisão via ADR.

Nunca um query que possa vazar dados entre tenants chega a produção.

### Domain-Driven Design (DDD)

O código espelha o domínio do negócio. Entidades como `Cliente`, `Campanha`, `Pontuação`, `Recompensa` existem como conceitos explícitos no código — não escondidos em tabelas genéricas.

### Clean Architecture

Dependências apontam sempre para dentro. O domínio não conhece o banco de dados. O banco de dados não conhece a API. A API não conhece o Flutter.

Cada camada tem responsabilidade única e clara.

### Event-driven para operações assíncronas

Campanhas, notificações, processamento de pontos e atualizações de Customer Intelligence acontecem de forma assíncrona via eventos — dunca bloqueando o fluxo principal do usuário.

---

## 5. O QUE NUNCA FAZEMOS

- Lógica de negócio dentro de Controller
- Acesso direto ao banco de dados fora do Repository
- Uso de `any` em TypeScript
- Commits sem mensagem descritiva (seguindo Conventional Commits)
- Merge sem code review
- Deploy em produção sem passa nos testes automatizados
- Misturar domínio com infraestrutura

---

*Versão: 1.0 | Junho 2026*
