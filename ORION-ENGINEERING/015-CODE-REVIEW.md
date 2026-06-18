# ORION CODE REVIEW
## Como Revisamos Código no Project ORION

**Volume:** ORION Engineering — Camada 2: Engenharia

---

## PRINCÍPIO

> Code review não é sobre encontrar bugs. É sobre garantir que o código pertence ao projeto — em padrão, em clareza e em intenção.

---

## QUEM REVISA

- Todo PR requer mínimo **1 aprovação**
- PRs que afetam mais de 1 módulo: **2 aprovações recomendadas**
- PRs de arquitetura ou mudança de stack: **aprovação dos fundadores obrigatória**
- O autor do código **não aprova** seu próprio PR

---

## CHECKLIST DO REVISOR

### Arquitetura e Design
- [ ] O código está na camada correta (domínio, aplicação, infraestrutura, interface)?
- [ ] Existe lógica de negócio no Controller? (proibido)
- [ ] Repositórios são acessados fora da camada de infraestrutura? (proibido)
- [ ] Módulos se comunicam diretamente em vez de via eventos? (proibido)
- [ ] Foi adicionada abstração prematura sem justificativa?

### Segurança e Multi-tenancy
- [ ] Todas as queries incluem `tenantId`?
- [ ] Inputs externos estão sendo validados via DTO?
- [ ] Logs contêm PII? (proibido)
- [ ] Algum secret está no código? (proibido)
- [ ] Endpoints novos têm autenticação?

### Qualidade de Código
- [ ] Existem usos de `any` sem justificativa? (proibido)
- [ ] Erros estão sendo silenciados com `catch` vazio? (proibido)
- [ ] Funções têm responsabilidade única?
- [ ] Nomes são descritivos sem precisar de comentário?
- [ ] Código duplicado que poderia ser extraído?

### Testes
- [ ] Testes unitários para toda lógica de domínio nova?
- [ ] Cenários de erro estão cobertos?
- [ ] Testes são independentes e não dependem de ordem?
- [ ] Cobertura mínima mantida?

### Documentação
- [ ] README do módulo atualizado se necessário?
- [ ] OpenAPI atualizado para novos endpoints?
- [ ] ADR criado para decisões arquiteturais?
- [ ] Comentários explicam o "porquê", não o "o quê"?

---

## COMO DAR FEEDBACK

### Prefixos de comentário

| Prefixo | Significado |
|---------|-------------|
| `[blocker]` | Deve ser resolvido antes do merge |
| `[suggestion]` | Melhoria recomendada, mas não bloqueia |
| `[question]` | Dúvida genuína — pode ser esclarecida com comentário |
| `[nit]` | Detalhe menor (formatação, nomenclatura) — não bloqueia |
| `[praise]` | Reconhecimento de boa prática — importante para cultura |

### Tom do feedback

```
# ❌ Evitar — pessoal e vago
"Isso está errado."
"Péssima abordagem."

# ✅ Usar — específico e construtivo
"[blocker] Essa query não inclui tenantId, o que pode vazar dados entre tenants.
Sugiro adicionar: where: { id, tenantId }"

"[suggestion] Poderíamos extrair essa lógica de cálculo de RFM para um
Domain Service, facilitando o teste unitário e a reutilização."

"[praise] Boa decisão de emitir o evento CustomerChurnRiskDetected aqui —
mantém o módulo de intelligence desacoplado."
```

---

## TEMPO DE REVIEW

- PRs pequenos (< 200 linhas): review em até **4 horas**
- PRs médios (200-500 linhas): review em até **1 dia útil**
- PRs grandes (> 500 linhas): considerar quebrar o PR — mas review em até **2 dias úteis**

PRs sem review por mais de 2 dias úteis: autor pinga o revisor.

---

## O QUE NÃO É RESPONSABILIDADE DO CODE REVIEW

- Detectar todos os bugs possíveis (isso é responsabilidade dos testes)
- Garantir que a feature está correta do ponto de vista de produto (isso é do PR description + testes)
- Decidir se a feature deve existir (isso é do processo de produto)

Code review garante: **o código pertence ao projeto em qualidade e padrão**.

---

*Versão: 1.0 | Junho 2026*  
*Próximo: [016-DOCUMENTATION-STANDARD.md](./016-DOCUMENTATION-STANDARD.md)*
