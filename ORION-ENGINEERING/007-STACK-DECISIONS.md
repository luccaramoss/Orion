# ORION STACK DECISIONS
## Por Que Usamos Cada Tecnologia

**Volume:** ORION Engineering — Camada 2: Engenharia

> Cada tecnologia na stack foi escolhida por razões específicas. Este documento registra o raciocínio para que futuras decisões de mudança sejam conscientes dos trade-offs originais.

---

## BACKEND — NestJS + TypeScript

**Decisão:** NestJS com TypeScript como framework principal do backend.

**Por que NestJS:**
- Arquitetura modular nativa (módulos, injeção de dependência) alinha perfeitamente com DDD
- TypeScript first — tipagem forte em todo o sistema
- Ecossistema maduro para APIs REST, filas (Bull), WebSockets, GraphQL
- Decorators facilitam a implementação de Clean Architecture sem boilerplate excessivo
- Documentação Swagger (OpenAPI) gerada automaticamente

**Por que não:**
- Express puro: sem estrutura arquitetural nativa, levaria a inconsistências entre módulos
- Fastify puro: mesmo problema do Express
- ASP.NET Core: equipe e Claude têm mais contexto no ecossistema Node/TypeScript

**Trade-offs aceitos:**
- NestJS adiciona overhead de aprendizado inicial
- Decorators podem obscurecer o fluxo de dados para desenvolvedores sem experiência no framework

---

## BANCO DE DADOS — PostgreSQL

**Decisão:** PostgreSQL como banco relacional principal.

**Por que PostgreSQL:**
- ACID compliance — crítico para transações de pontos e resgates
- Suporte nativo a JSON/JSONB — flexibilidade para dados comportamentais variados
- Row Level Security (RLS) — isolamento de tenant no nível do banco
- pgvector — extensão para embeddings de IA (Customer Intelligence)
- Ecossistema maduro, confiável, com suporte de longo prazo
- TypeORM tem suporte excelente para PostgreSQL

**Por que não MongoDB:**
- Dados de pontos, transações e relacionamentos são naturalmente relacionais
- Consistência transacional é crítica (sem eventual consistency aqui)
- RLS não existe em MongoDB (isolamento de tenant seria mais complexo)

**Trade-offs aceitos:**
- Schema migrations exigem cuidado em produção com dados existentes
- Escala horizontal é mais complexa que em bancos NoSQL (mas não é problema para o horizonte de 1-2 anos)

---

## CACHE / FILAS — Redis + Bull

**Decisão:** Redis para cache e Bull (sobre Redis) para filas de processamento assíncrono.

**Por que Redis:**
- Performance extremamente alta para operações de cache
- Suporte nativo a estruturas: strings, hashes, sorted sets (ranking de Clientes)
- TTL nativo por chave — perfeito para cache com expiração
- Pub/Sub para eventos em tempo real

**Por que Bull:**
- Built on Redis — sem nova infraestrutura
- Retry automático com backoff exponencial
- Priorização de jobs
- Dashboard de monitoramento (Bull Board)
- Suporte nativo em NestJS via `@nestjs/bull`

**Trade-offs aceitos:**
- Redis é single-threaded — não recomendado para operações computacionalmente intensivas diretamente nele
- Persistência do Redis precisa ser configurada explicitamente (RDB/AOF)

---

## MOBILE — Flutter

**Decisão:** Flutter para o app do Cliente (iOS + Android).

**Por que Flutter:**
- Single codebase para iOS e Android — reduz custo de desenvolvimento pela metade
- Performance próxima de nativo (Dart compilado para ARM)
- UI consistente em ambas as plataformas — importante para white-label
- Ecossistema maduro para apps de consumo
- Customização visual facilitada — crítico para white-label (cada tenant tem sua identidade)

**Por que não React Native:**
- Bridge JavaScript aumenta latência em animações complexas
- Flutter tem UI engine própria — sem dependência de componentes nativos de cada plataforma
- White-label é mais controlável em Flutter

**Trade-offs aceitos:**
- Dart tem curva de aprendizado
- Tamanho do bundle maior que React Native em alguns casos
- Comunidade menor que React Native (mas crescendo rapidamente)

---

## AUTENTICAÇÃO — JWT + Refresh Token

**Decisão:** JWT para autenticação stateless com Refresh Token de longa duração.

**Por que JWT:**
- Stateless — backend não precisa armazenar sessões
- Contém tenant_id e claims no token — reduz queries ao banco por request
- Padrão amplamente conhecido com boa biblioteca de suporte

**Fluxo:**
```
1. Login → Access Token (15 min) + Refresh Token (30 dias)
2. Request → Access Token no header Authorization
3. Access Token expira → usa Refresh Token para obter novo par
4. Refresh Token comprometido → revogação via blacklist no Redis
```

**Trade-offs aceitos:**
- Access Token não pode ser revogado instantaneamente (TTL de 15 min é o máximo de exposição)
- Refresh Token requer storage seguro no cliente

---

## PUSH / STORAGE — Firebase

**Decisão:** Firebase para autenticação mobile, push notifications e storage de arquivos.

**Por que Firebase:**
- FCM (Firebase Cloud Messaging) é o padrão de mercado para push em iOS e Android
- Firebase Auth simplifica autenticação no app do Cliente
- Firebase Storage para imagens de recompensas e perfis

**Trade-offs aceitos:**
- Dependência de serviço Google — plano de contingência documentado em ADR
- Custo por volume de mensagens acima do free tier

---

## IA — OpenAI API (GPT-4o)

**Decisão:** OpenAI GPT-4o para geração de Customer Intelligence e personalização de mensagens.

**Por que OpenAI:**
- Melhor qualidade de geração de linguagem natural para mensagens personalizadas
- API madura com rate limiting e retry nativo
- Suporte a function calling — integração estruturada com o domínio do ORION
- Custo por token viável para o volume inicial

**Modelo de uso no ORION:**
- Geração de mensagens personalizadas de campanha
- Análise de padrão comportamental para sugestão de segmentação
- Síntese de Customer Intelligence em linguagem natural para o Gestor

**Modelos de ML próprios (roadmap):**
- Scores de churn e LTV: modelos próprios (scikit-learn ou TensorFlow) treinados nos dados do ORION — tais modelos são mais eficientes e baratos que LLM para dados estruturados

**Trade-offs aceitos:**
- Custo variável por uso
- Latência adicional em features que dependem da API
- Dados enviados para processamento externo (anonymizados — sem PII)

---

## INTEGRAÇÕES

| Integração | Propósito | Status |
|-----------|-----------|--------|
| Nuvemshop | Sync de pedidos e Clientes do e-commerce | MVP |
| Bling | Sync de estoque, produtos e vendas do PDV | MVP |
| WhatsApp Business API | Envio de mensagens de campanha | MVP |
| Firebase FCM | Push notifications no app | MVP |
| OpenAI | Customer Intelligence e personalização | MVP |
| Stripe / Pagar.me | Pagamento de assinatura dos tenants | Pós-MVP |
| Mercado Pago | Alternativa de pagamento | Pós-MVP |

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [008-DIRECTORY-STRUCTURE.md](./008-DIRECTORY-STRUCTURE.md)*
