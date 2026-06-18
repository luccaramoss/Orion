# ORION FIRST PRINCIPLES
## Os Princípios Fundamentais Imutáveis

**Volume:** ORION DNA — Camada 1: Fundação

> First Principles são o piso — não o teto. Toda decisão de produto, negócio, engenharia e design é avaliada contra eles. Se uma decisão viola um First Principle, ela não passa.

---

## OS 12 PRIMEIROS PRINCÍPIOS DO ORION

---

### Princípio 01 — Relacionamento vale mais que desconto

**Enunciado:** Fidelidade construída sobre desconto dura até o próximo concorrente oferecer 1% a mais. Fidelidade construída sobre reconhecimento dura décadas.

**Implicação para produto:** Toda funcionalidade deve fortalecer o relacionamento entre a Empresa e o Cliente — não apenas reduzir preço.

**Implicação para engenharia:** Sistemas que personalizam experiência têm prioridade sobre sistemas que automatizam promoções genéricas.

**Teste:** "Isso faz o Cliente se sentir mais reconhecido pela Empresa?" Se não, reavalie.

---

### Princípio 02 — Dados comportamentais são ativos estratégicos

**Enunciado:** O histórico de compras de um Cliente vale mais do que o produto que ele comprou. Quem entende o comportamento do Cliente controla o relacionamento.

**Implicação para produto:** Todo evento do Cliente deve ser rastreado, armazenado e utilizado — nunca descartado.

**Implicação para engenharia:** A modelagem de dados prioriza riqueza de contexto comportamental — não apenas transações.

**Teste:** "Estamos usando esse dado para gerar valor real, ou apenas para encher dashboard?" Se for o segundo, o dado não precisa existir.

---

### Princípio 03 — IA existe para gerar resultado, não para impressionar

**Enunciado:** Inteligência Artificial sem resultado mensurável é marketing. No ORION, toda função de IA tem uma métrica de sucesso em reais.

**Implicação para produto:** Nenhuma feature de IA entra no roadmap sem definir: "como mediremos o resultado em receita gerada ou churn evitado?"

**Implicação para engenharia:** Modelos são avaliados por assertividade e impacto financeiro — não por sofisticação técnica.

**Teste:** "Quanto dinheiro isso gerou nos últimos 30 dias?" Se não conseguimos responder, a feature precisa ser auditada.

---

### Princípio 04 — Automação deve eliminar trabalho repetitivo humano

**Enunciado:** O Gestor não deve precisar fazer manualmente o que o ORION pode aprender a fazer automaticamente.

**Implicação para produto:** Toda tarefa que o Gestor executa mais de uma vez rsemana é candidata a automação.

**Implicação para engenharia:** Filas assíncronas, workers e triggers de evento são tão importantes quanto as APIs.

**Teste:** "O Gestor precisa lembrar de fazer isso? Se sim, por que ainda não automatizamos?"

---

### Princípio 05 — Toda funcionalidade precisa gerar valor mensurável

**Enunciado:** Se não conseguimos definir a métrica de sucesso antes de construir, não estamos prontos para construir.

**Implicação para produto:** Todo item do backlog tem "métrica de sucesso" como campo obrigatório.

**Implicação para engenharia:** Instrumentação (logging, tracking de eventos) é parte do desenvolvimento da feature — não afterthought.

**Teste:** "Qual número vai mudar se essa feature funcionar como esperado?" Se não sabemos, voltamos para o início.

---

### Princípio 06 — Simplicidade na interface, sofisticação no motor

**Enunciado:** O Gestor de uma PME não tem tempo para aprender software complexo. A sofisticação do ORION deve ser invisível — os resultados, óbvios.

**Implicação para produto:** Cada tela tem no máximo uma ação primária. Cada recomendação vem em linguagem humana, pronta para executar.

**Implicação para engenharia:** Complexidade algorítmica é bem-vinda no domínio. Complexidade de interface é sempre um problema.

**Teste:** "Um Gestor sem treinamento consegue executar essa ação em menos de 3 cliques?" Se não, simplificamos.

---

### Princípio 07 — Os dados do Cliente pertencem ao Cliente

**Enunciado:** A Empresa que usa o ORION é dona dos seus dados. Nós somos guardiões, não proprietários.

**Implicação para produto:** Exportação de dados completa sempre disponível. Offboarding limpo sem retenção de dados.

**Implicação para engenharia:** Isolamento de tenant é inegociável. RLS (Row Level Security) ou schema-per-tenant. Auditoria de acesso em produção.

**Teste:** "Se o tenant decidir sair amanhão, ele leva todos os seus dados e nenhum traão fica no nosso sistema?" Se não, temos um problema.

---

### Princípio 08 — Consistência vence velocidade

**Enunciado:** Um sistema inconsistente que entrega rápido cria dívida técnica que cancela todos os ganhos de velocidade em 6 meses.

**Implicação para produto:** Features novas seguem os padrões existentes — mesmo que seja mais trabalhoso.

**Implicação para engenharia:** Code review não é opcional. Desvio de padrão sem aprovação explícita não vai para produção.

**Teste:** "Alguém que nunca viu esse código consegue entender o que ele faz no 5 minutos?" Se não, refatoramos antes de mergear.

---

### Princípio 09 — Arquitetura é decisão, não acidente

**Enunciado:** Sistemas que crescem sem arquitetura intencional viram monólitos impossíveis de manter. No ORION, toda decisão arquitetural é documentada antes de ser implementada.

**Implicação para produto:** Novas features são avaliadas quanto ao impacto arquitetural antes de entrar no sprint.

**Implicação para engenharia:** ADR (Architecture Decision Record) para toda decisão de alto impacto. A arquitetura no código espelha a arquitetura documentada.

**Teste:** "Existe um ADR para essa decisão?" Se não, por que não?"

---

### Princípio 10 — O Vertical Slice vence o horizontal

**Enunciado:** Um fluxo completo funcionando ponta a ponta gera mais aprendizado e valor do que dez funcionalidades parcialmente implementadas.

**Implicação para produto:** O MVP é um Vertical Slice: compra → pontos → histórico → push → dashboard → IA → recompra. Completo e funcional antes de expandir.

**Implicação para engenharia:** Prioridade para completar fluxos existentes antes de iniciar fluxos novos.

**Teste:** "Se parássemos de desenvolver hoje, o que entregamos geraria resultado real para o tenant?" Se sim, estamos no caminho certo.

---

### Princípio 11 — Documentação é código

**Enunciado:** Código sem documentação é código incompleto. Decisão sem registro é decisão que vai ser repetida desnecessariamente.

**Implicação para produto:** PRDs, wireframes e especificações existem antes do desenvolvimento começar.

**Implicação para engenharia:** Todo módulo tem README. Toda API tem OpenAPI. Todo ADR está versionado no repositório. Documentação desatualizada é bug.

**Teste:** "Um desenvolvedor novo consegue entender o propósito desse módulo e como contribuir sem precisar perguntar para ninguém?" Se não, a documentação está incompleta.

---

### Princípio 12 — Privacidade é respeito, não compliance

**Enunciado:** LGPD é o mínimo. Tratamos dados de Clientes com o mesmo cuidado que gostaríamos que tratassem os nossos.

**Implicação para produto:** Coleta de dado só acontece quando há uso claro e imediato. Dado desnecessário não é coletado.

**Implicação para engenharia:** Dados sensíveis são encriptados em repouso e em trânsito. Logs não contêm PII (Personal Identifiable Information). Acesso a dados de produção é auditado.

**Teste:** "Se o Cliente soubesse exatamente o que fazemos com cada dado que coletamos, ele ficaria confortável?" Se não, revisamos.

---

## COMO USAR ESTE DOCUMENTO

**Em reuniões de produto:** Quando houver impasse sobre uma feature, referencie os First Principles. Eles desempatam.

**Em code reviews:** Quando um padrão novo propõe algo que parece inconsistente, verifique se viola algum princípio.

**No planejamento de sprint:** Antes de priorizar um backlog item, verifique se ele serve pelo menos um princípio.

**Com o Claude:** Em toda sessão de desenvolvimento, o Claude deve ter acesso a este documento. Ele orienta decisões de implementação quando não há especificação explícita.

---

## REVISÃO DOS PRINCÍPIOS

Os First Principles são estáveis — mas não eternos.

Uma vez por ano (ou em momento de mudança significativa de estratégia), os fundadores revisam se os princípios ainda refletem a realidade do projeto.

Mudança de First Principle requer: proposta escrita, período de reflexão de 7 dias, decisão documentada.

---

*Versão: 1.0 | Junho 2026*  
*Este é o último documento do ORION DNA — Camada 1: Fundação.*  
*Próxima camada: [ORION Engineering — Camada 2](../ORION-ENGINEERING/)*
