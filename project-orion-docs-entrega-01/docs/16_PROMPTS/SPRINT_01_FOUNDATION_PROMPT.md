# SPRINT_01_FOUNDATION_PROMPT — Project ORION

Documento: ORION-PROMPT-002  
Versão: 1.0.0  
Status: Oficial  
Data: 2026-06-17

## Objetivo da Sprint 01

Preparar a fundação técnica inicial do projeto para desenvolvimento do MVP.

## Prompt para Claude Code

Inicie a Sprint 01 do Project ORION.

Objetivo: configurar a base do repositório para receber backend NestJS, painel Next.js, app Flutter e documentação.

Antes de executar, leia:

- CLAUDE.md
- docs/15_AI_CONTEXT/PROJECT_RULES.md
- docs/00_FOUNDATION/BUSINESS_BIBLE.md

Tarefas:

1. Validar estrutura atual de pastas.
2. Propor monorepo ou multi-repo e justificar.
3. Criar estrutura inicial de apps: `apps/api`, `apps/admin`, `apps/mobile`, `packages/shared`, `packages/config`.
4. Criar `.env.example` inicial.
5. Criar README técnico de setup.
6. Criar checklist da próxima sprint.
7. Não implementar funcionalidades de negócio ainda sem aprovação.

Regras:

- Não expor chaves.
- Não acoplar Santo Axé ao núcleo.
- Preparar multi-tenant desde o modelo inicial.
- Documentar decisões importantes.
