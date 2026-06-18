# ORION GLOSSARY
## Vocabulário Oficial do Project ORION

**Volume:** ORION DNA — Camada 1: Fundação

> Todo documento, código, conversa e decisão usa os termos abaixo. Sem exceção.  
> Quando há dúvida sobre o nome correto de algo, este é o documento de referência.

---

## A

**Ação (Action)**  
Qualquer operação executada pelo ORION de forma automática ou semi-automática: envio de campanha, atualização de pontuação, geração de insight.

**ADR (Architecture Decision Record)**  
Documento que registra uma decisão arquitetural importante: contexto, alternativas consideradas, decisão tomada e consequências.

---

## C

**Campanha (Campaign)**  
Ação de marketing segmentada e automatizada, criada manualmente pelo Gestor ou sugerida pela IA do ORION. Sempre tem segmento-alvo, mensagem, canal e métrica de resultado definidos.

**Cliente (Customer)**  
Pessoa física que é cliente da Empresa tenant. **Nunca usar "usuário" para se referir ao Cliente.** O Cliente interage com o App do Cliente.

**Customer Intelligence**  
Conjunto de insights gerados continuamente pela IA sobre o comportamento, valor e risco de churn de cada Cliente.

---

## E

**Empresa (Company / Tenant)**  
Organização que utiliza o ORION como plataforma (ex: Santo Axé). Sinônimo de Tenant em contextos técnicos. **Nunca usar "loja"** — Empresa é o termo correto.

**Evento (Event)**  
Ação rastreável realizada pelo Cliente: compra, abertura de notificação, resgate de recompensa, login no app.

---

## G

**Gestor (Manager)**  
Pessoa da Empresa tenant que usa o Painel do Gestor para operar e configurar o ORION. Pode ser o dono da empresa, gerente de marketing ou operador.

---

## I

**Insight**  
Informação acionável gerada pela IA a partir de dados de comportamento. Um Insight sempre vem acompanhado de uma ação sugerida.

**Integração (Integration)**  
Conexão entre o ORION e um sistema externo: Nuvemshop, Bling, WhatsApp Business, Firebase.

---

## L

**Lifetime Value (LTV)**  
Projeção do valor total que um Cliente gerará para a Empresa durante todo o período de relacionamento.

---

## M

**Módulo (Module)**  
Unidade funcional independente do ORION no backend: `customers`, `campaigns`, `rewards`, `intelligence`, `integrations`, `notifications`.

**Multi-tenancy**  
Arquitetura onde múltiplas Empresas (tenants) usam a mesma infraestrutura do ORION, com dados completamente isolados entre si.

---

## O

**OEOS (ORION Engineering Operating System)**  
A metodologia e conjunto de documentos que define como o Project ORION é construído, operado e evoluído.

**ORION DNA**  
Camada 1 do OEOS. Os 12 documentos fundacionais que definem a empresa, o produto e a cultura de engenharia.

---

## P

**Painel do Gestor (Manager Panel)**  
Interface web usada pela Empresa tenant para operar o ORION: ver Customer Intelligence, criar e aprovar campanhas, configurar regras de pontuação, acompanhar métricas.

**PDV (Ponto de Venda)**  
Loja física da Empresa tenant. Integrado ao ORION para registro de compras e identificação do Cliente presencialmente.

**Plataforma (Platform)**  
O ORION como um todo — backend, app, painel, IA e integrações. **Nunca usar "sistema" ou "app" para se referir ao conjunto completo.**

**Pontuação (Points)**  
Mecanismo de acúmulo de créditos pelo Cliente a partir de comportamentos de compra. A regra de acúmulo é configurável por Empresa.

---

## R

**Recompensa (Reward)**  
Benefício resgatável pelo Cliente usando sua Pontuação acumulada. Pode ser desconto, produto gratuito, cashback ou experiência exclusiva.

**RFM (Recency, Frequency, Monetary)**  
Modelo de segmentação de Clientes baseado em: quando comprou pela última vez, com que frequência compra e quanto gasta.

---

## S

**Score**  
Valor numérico calculado pela IA para representar uma dimensão do Cliente: Score de Churn, Score de Valor, Score de Engajamento.

**Segmento (Segment)**  
Grupo de Clientes com características comportamentais em comum, usado para direcionar Campanhas.

---

## T

**Tenant**  
Sintnimo técnico de Empresa no contexto de multi-tenancy. Cada tenant tem seus dados isolados no banco de dados.

**Tenant ID**  
Identificador \unico de cada Empresa no banco de dados. Presente em todas as queries que envolvem dados de tenant.

---

## V

**Vertical Slice**  
Abordagem de desenvolvimento que prioriza entregar um fluxo completo ponta a ponta (compra → pontos → histórico → notificação → dashboard → recompra) antes de expandir horizontalmente em funcionalidades.

---

## TERMOS PROIBIDOS

| Em vez de | Use |
|-----------|-----|
| Usuário | Cliente (para consumidor final) ou Gestor (para operador da plataforma) |
| Loja | Empresa ou PDV |
| Sistema | Plataforma |
| App | App do Cliente (se contexto for o mobile) ou Plataforma (se contexto for o todo) |
| Banco | Banco de dados (em documentação técnica) |
| Produto (referindo-se ao ORION) | Plataforma |

---

*Versão: 1.0 | Junho 2026*
