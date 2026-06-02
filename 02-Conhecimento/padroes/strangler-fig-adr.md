---
type: conhecimento
status: active
tags: [padrão, strangler-fig, adr, refatoração, legado, arquitetura]
created: 2026-05-20
---

# 🌿 Strangler Fig + Architecture Decision Records (ADR)

---

## Strangler Fig Pattern

> Martin Fowler — estratégia segura para modernizar monólitos sem big bang rewrite.

### O Problema
Reescrever um sistema legado do zero (big bang) é arriscado: o novo sistema deve replicar décadas de comportamento antes de ir para produção.

### A Estratégia

```
Fase 1: Interpor fachada/proxy entre clientes e monólito
         Clientes → [Proxy] → Monólito (100%)

Fase 2: Extrair e reescrever verticais em microsserviços
         Clientes → [Proxy] → Novo Serviço A (rota /checkout)
                            → Monólito (demais rotas)

Fase 3: Continuar até o monólito ter zero tráfego
         Clientes → [Proxy] → Novo Serviço A
                            → Novo Serviço B
                            → Novo Serviço C

Fase 4: Desligar monólito com risco zero
```

### Táticas de Extração

| Tática | Quando Usar |
|--------|------------|
| **Branch by Abstraction** | Extrair uma classe/módulo sem quebrar builds |
| **Feature Toggle** | Redirecionar tráfego para novo serviço gradualmente |
| **API Gateway como fachada** | Roteamento baseado em path ou header |
| **Event Interception** | Interceptar eventos do monólito para o novo serviço |

### Princípios

1. **Nunca modificar o monólito** para acomodar o novo serviço — crie a fachada
2. **Retorno gradual** — cada extração entrega valor antes da próxima
3. **Testar comportamento**, não implementação — testes de contrato garantem paridade
4. **Monitorar** ambos os caminhos com feature flags para rollback seguro

---

## Architecture Decision Records (ADR)

> Registro imutável de decisões arquiteturais significativas. Filosofia: **append-only log** — nunca reescreva, sempre acrescente.

### Quando Criar um ADR
- Mudança de banco de dados, framework ou protocolo
- Escolha entre padrões arquiteturais (ex: Saga vs 2PC)
- Adoção de nova tecnologia de infraestrutura
- Decisão que afeta múltiplos times ou serviços
- Qualquer decisão que você precisaria explicar para um novo engenheiro

### Template ADR

```markdown
# ADR-[número]: [Título da Decisão]

**Data:** YYYY-MM-DD
**Status:** Proposta | Aceita | Depreciada | Substituída por ADR-XXX
**Decisores:** [nomes ou equipes]

## Contexto
[Situação que exige uma decisão. Forças em jogo, restrições, requisitos.]

## Decisão
[A mudança que estamos fazendo, formulada positivamente: "Vamos adotar X..."]

## Opções Consideradas
1. **Opção A** — [prós e contras]
2. **Opção B** — [prós e contras]
3. **Opção C** (escolhida) — [prós e contras]

## Consequências

### Positivas
- 

### Negativas / Trade-offs
- 

### Neutras
- 

## Notas
[Referências, links, decisões relacionadas]
```

### Exemplo Real

```markdown
# ADR-007: Migrar de Saga Coreografada para Orquestrada

**Data:** 2026-03-15
**Status:** Aceita

## Contexto
O ecossistema de eventos cresceu para 14 serviços participando do fluxo de 
pedidos. A ausência de um coordenador central tornou o rastreamento de 
falhas extremamente difícil. Incidentes recentes exigiram análise manual 
de logs de 8 serviços para identificar o ponto de falha.

## Decisão
Adotaremos Saga Orquestrada usando Temporal.io como Saga Orchestrator 
para todos os novos fluxos. Fluxos existentes serão migrados gradualmente.

## Consequências
- ✅ Visibilidade total do estado da Saga em dashboard único
- ✅ Compensação automática gerenciada pelo orquestrador
- ❌ Temporal.io é novo componente de infraestrutura (curva de aprendizado)
- ❌ Orquestrador é ponto único de falha (mitigado por clustering do Temporal)
```

### Onde Armazenar ADRs
- `docs/decisions/` na raiz do repositório
- `ADR-001.md`, `ADR-002.md`, ... (numeração sequencial)
- Nunca deletar — marcar como `Depreciada` ou `Substituída por ADR-XXX`

---

## Referências
- *Strangler Fig Application* — Martin Fowler (martinfowler.com)
- *Architectural Decision Records* — Michael Nygard
- [[saga-event-sourcing-outbox|Saga Pattern →]]
- [[ddd-domain-driven-design|DDD — contexto para ADRs de domínio →]]
