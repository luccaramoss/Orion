# ORION ENGINEERING BIBLE
## O Manual Completo de Engenharia do Project ORION

**Versão:** 1.0  
**Volume:** ORION Engineering — Camada 2: Engenharia  
**Pré-requisito:** [ORION DNA](../ORION-DNA/) deve ser lido antes deste documento.

> Este documento é o ponto de entrada para qualquer pessoa (ou IA) que vai contribuir tecnicamente com o Project ORION. Ele não substitui os documentos específicos — ele os conecta e contextualiza.

---

## ÍNDICE

1. [O que é a Camada de Engenharia](#1-o-que-é-a-camada-de-engenharia)
2. [Como ler este volume](#2-como-ler-este-volume)
3. [A Stack do ORION](#3-a-stack-do-orion)
4. [A Arquitetura em uma página](#4-a-arquitetura-em-uma-página)
5. [Os módulos do sistema](#5-os-módulos-do-sistema)
6. [O fluxo de desenvolvimento](#6-o-fluxo-de-desenvolvimento)
7. [Hierarquia de documentos técnicos](#7-hierarquia-de-documentos-técnicos)
8. [Regras inegociáveis](#8-regras-inegociáveis)

---

## 1. O QUE É A CAMADA DE ENGENHARIA

A Camada de Engenharia do OEOS é o DNA técnico do Project ORION.

Se o ORION DNA define **quem somos**, a Camada de Engenharia define **como construímos**.

Ela responde perguntas como:
- Como nomeamos variáveis, funções, arquivos e tabelas?
- Como estruturamos as pastas do projeto?
- Como escrevemos testes?
- Como fazemos deploy?
- Como lidamos com erros?
- Como documentamos código?
- Como revisamos código?
- Como tomamos decisões arquiteturais?

Estas respostas estão documentadas de forma que **o Claude, um desenvolvedor júnior eu um desenvolvedor sênior produzam código indistinguível em estilo e padrão**.

---

## 2. COMO LER ESTE VOLUME

Leia nesta ordem:

| # | Documento | Por que ler |
|---|-----------|-------------|
| 1 | `001-ENGINEERING-BI@��-.md` (este) | Visão geral de tudo |
| 2 | `002-CLAUDE-CONTEXT.md` | Contexto específico para o Claude trabalhar no projeto |
| 3 | `003-PROJECT-RULES.md` | Regras que nunca podem ser quebradas |
| 4 | `004-CODING-STANDARDS.md` | Como escrever código no ORION |
| 5 | `005-NAMING-COCΖENTIONS.md` | Como nomear tudo |
| 6 | `006-ARCHITECTURE-PRINCIPLES.md` | Como a arquitetura funciona |
| 7 | `007-STACK-DECISIONS.md` | Por que usamos cada tecnologia |
| 8 | `008-DIRECTORY-STRUCTURE.md` | Como o projeto está organizado |
| 9 | `009-DEPENDENCY-POLICY.md` | Como adicionamos dependências |
| 10 | `010-ERROR-HANDLING.md` | Como tratamos erros |
| 11 | `011-LOGGING-POLICY.md` | Como fazemos logging |
| 12 | `012-TESTING-POLICY.md` | Como testamos |
| 13 | `013-GIT-WORKFLOW.md` | Como usamos git |
| 14 | `014-VERSIONING.md` | Como versionamos |
| 15 | `015-CODE-REVIEW.md` | Como revisamos código |
| 16 | `016-DOCUMENTATION-STANDARD.md` | Como documentamos |

---

## 3. A STACK DO ORION

| Camada | Tecnologia |
|--------|-----------|
| Mobile | Flutter (iOS + Android) |
| Backend | NestJS + TypeScript |
| Banco principal | PostgreSQL |
| Cache / Filas | Redis + Bull |
| Busca | Elasticsearch |
| Auth | JWT + Refresh Token |
| Push / Auth mobile | Firebase |
| IA | OpenAI API (GPT-4o) |
| Integrações | Nuvemshop, Bling, WhatsApp Business API |

---

## 4. A ARQUITETURA EM UMA PÁGINA

O ORION segue **Clean Architecture** com **Domain-Driven Design (DDD)**.

```
INTERFACE (Controllers) --> recebe HTTP, valida DTO
APPLBCATION (Use Cases) --> orquestra domínio
DOMAIN (Entities)       --> regras de negócio puras
INFRASTRUCTURE          --> banco, cache, APIs externas
```

**Regra de ouro:** Domain não conhece Infrastructure. Nunca.

---

## 5. OS MÓ3DULOS DO SISTEMA

```
orion-backend/src/modules/
├── customers/          # Gestão de Clientes e perfis
├── campaigns/         # Criação e disparo de Campanhas
├── rewards/           # Recompensas e resgates
├── points/            # Acúmulo e regras de Pontuação
├── transactions/      # Registro de compras e eventos
├── intelligence/      # Customer Intelligence e IA
├── notifications/     # Push, WhatsApp, e-mail
├── integrations/      # Nuvemshop, Bling, PDV
├── tenants/           # Gestão de Tenants
j└── auth/              # Autenticação e autorização
```

---

## 6. O FLUXO DE DESENVOLVIMENTO

```
1. Leia os docs relevantes (antes de codar)
2. Identifique módulo, entidade e use case
3. Domain first -- lógica sem banco ou API
4. Testes do domínio antes de continuar
5. Application layer -- Use Case que orquestra
6. Infrastructure -- Repository e adapters
7. Interface -- Controller, DTO
8. Testes de integração
9. Documentação -- README, OpenAPI, ADR
10. Code review
-- checklist em 015-CODE-REVIEW.md
```

---

## 7. HIERARQUIA DE DOCUMENTOS TÉCNICOS

```
OEOS ← ORION DNA (Camada 1)
      ← ORION Engineering (Camada 2)
             ← Engineering Bible (este)
             ← Claude Context
             ← Project Rules
             ← Standards (Coding, Naming, Docs)
             ← Architecture (Principles, Stack, Directory)
             ← Policies (Dependency, Error, Logging, Testing, Git, Versioning, Review)
```

---

## 8. REGRAS INEGOCJÁVEIS

1. Nenhum código de domínio vai para produção sem teste unitário
2. Nenhuma query é executada sem tenant_id validado
3. Nenhum log contém PII (nome, CPF, telefone, e-mail)
4. Nenhum secret é commitado no repositório
5. Nenhum merge é feito sem code review aprovado
6. Nenhuma dependência é adicionada sem avaliação de segurança e licença
7. Nenhum erro é silenciado — todo erro é logado ou relançado
8. Nenhuma API endpoint é exposta sem autenticação

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [002-CLAUDE-CONTEXT.md](./002-CLAUDE-CONTEXT.md)*
