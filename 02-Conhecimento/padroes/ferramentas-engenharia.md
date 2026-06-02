---
type: conhecimento
status: active
tags: [padrão, ferramentas, linear, jira, git-flow, trunk-based, cicd, adr, runbook, divida-tecnica]
created: 2026-06-01
updated: 2026-06-01
---

# 🛠️ Ferramentas e Sistemas que Times Profissionais Usam

> O stack de gestão e processos de times de alto desempenho.

---

## Ferramentas de Gestão de Projeto

| Ferramenta | Quem usa | Por que escolhem |
|-----------|---------|-----------------|
| **Linear** | Startups, scale-ups (Linear, Vercel) | Velocidade, feita para devs, UX excelente |
| **Jira** | Grandes empresas (Atlassian, bancos) | Customizável, integra com tudo, escala |
| **Notion** | Startups e empresas modernas | Flexível, documentação + gestão em um |
| **ClickUp** | Pequenas empresas | Custo-benefício, tudo-em-um |
| **GitHub Projects** | Times de devs | Integrado com código, simples |
| **Asana** | Empresas de produto | Visual, fácil de usar |

### Tendência 2025–2026
- **Startups:** Linear ou Notion (velocidade > customização)
- **Empresas tradicionais:** Jira (ecossistema maduro)
- **Times pequenos:** GitHub Projects ou Trello (simplicidade)

---

## Fluxo de Código: Git Flow vs. Trunk-Based Development

### Git Flow (Tradicional)

```
main ← develop ← feature branches
```

- Cada feature em branch separada
- Merge para develop → depois main
- Release branches + hotfix branches
- **Problema:** complexo, merges conflituosos, ciclo de deploy lento

### Trunk-Based Development (Usado por Stripe, Linear, Google)

```
main (trunk) ← feature branches curtas (1–2 dias)
```

- Branches vivem **pouquíssimo tempo** (horas/dias, não semanas)
- Merges frequentes no trunk (main)
- **Feature flags** para funcionalidades não prontas
- **Vantagem:** menos conflitos, deploy mais rápido, integração contínua real

### Code Review (Prática Universal)

- Todo código precisa de review antes de merge
- 2 aprovadores para mudanças críticas
- Automated checks passam antes de review humano
- Tempo médio de review: **< 1 hora** (times maduros)
- Foco no código, não na pessoa: "este trecho pode melhorar" ≠ "você errou"

---

## CI/CD — Continuous Integration / Continuous Deployment

### Pipeline CI/CD Típico

```
Push → Lint → Testes Unitários → Testes Integração → Build → Deploy Staging → Deploy Prod
```
**Tempo total:** 5–15 minutos (times maduros)

### CI (Continuous Integration)
- Dev faz push → testes rodam automaticamente
- Se testes passam → código pode ser mergado
- Ferramentas: **GitHub Actions**, GitLab CI, CircleCI

### CD (Continuous Deployment)
- Código mergado → deploy automático em produção
- Sem aprovação manual (times maduros)
- Rollback automático se métricas caírem

---

## ADRs — Architecture Decision Records

ADRs registram decisões arquiteturais importantes e **por que foram tomadas**.

### Quando Usar ADR
- Mudança de banco de dados
- Escolha de nova linguagem ou framework
- Mudança de arquitetura (monólito → microserviços)
- Decisões que afetam múltiplos times

### Estrutura de ADR

```markdown
# ADR-001: Escolha de Banco de Dados

## Status
Aprovado (2026-01)

## Contexto
Precisamos de banco para dados de usuários com 1M+ registros.

## Decisão
Escolhemos PostgreSQL em vez de MongoDB.

## Consequências
+ Queries complexas mais fáceis com CTEs e window functions
- Horizontal scaling mais difícil

## Alternativas Consideradas
- MongoDB: rejeitado porque queries relacionais são mais difíceis
- MySQL: rejeitado porque performance para OLAP é limitada
```

**Por que times maduros documentam:**
- Novos integrantes entendem por que decisões foram tomadas
- Evita repetir decisões erradas
- Contexto permanece quando pessoas saem

---

## Runbooks

Runbook é **documentação passo-a-passo para operações de sistema** — especialmente para incidentes.

### Quando Ter Runbook
- Sistema crítico (produção, pagamento, autenticação)
- Incidentes comuns (downtime, erro 500, queries lentas)
- Deploy e rollback
- Backup e recovery

### Estrutura de Runbook

```markdown
# Runbook: Sistema de Pagamentos

## Symptoms
- Erro 500 em /api/payments
- Latência > 5s
- Alertas de monitoring disparando

## Diagnóstico Rápido
1. Checar status do banco: `SELECT 1 FROM db_health`
2. Checar queue: `redis-cli LLEN payment_queue`
3. Checar logs: `tail -f logs/payment.log | grep ERROR`

## Mitigação
1. Se banco down → failover automático (5 min)
2. Se queue cheia → escalar workers (2 min)
3. Se erro específico → rollback versão anterior (3 min)

## Prevenção
- Alerta quando queue > 1000
- Auto-scaling quando CPU > 70%
```

**Por que ter runbook:**
- On-call menos estressante — roteiro claro
- Novos devs podem operar sistemas críticos
- MTTR (Mean Time To Recovery) cai de horas para minutos
- Conhecimento não fica tribal (na cabeça de uma só pessoa)

---

## Gestão de Dívida Técnica

Dívida técnica é o custo de manutenção de soluções rápidas/temporárias que ficaram permanentes.

### Como Empresas Maduras Gerenciam

**1. Tornar visível**
- Dashboard de dívida técnica
- Métricas: test coverage, build time, code smell count, DORA metrics

**2. Alocar capacidade fixa**
- 20% de cada sprint para dívida técnica
- "Tech debt sprint" a cada 6 semanas (Shape Up: cool-down)

**3. Priorizar estrategicamente**
- Dívida que **bloqueia features** → alta prioridade
- Dívida cosmética → baixa prioridade

**4. Prevenir nova dívida**
- Code review obrigatório
- Testes automatizados
- Definition of Done inclui qualidade de código

---

## Runbook vs. Playbook — Distinção Importante

Ambos vêm da cultura **Google SRE (Site Reliability Engineering)**, mas têm propósitos distintos:

| | Runbook | Playbook |
|--|---------|---------|
| **Escopo** | Alarme ou falha **específica** e localizada | Colapso sistêmico **global** e aberto |
| **Formato** | Lista linear de passos pragmáticos | Estratégias cognitivas de investigação |
| **Exemplo** | "CPU > 90% no serviço de pagamentos: passos 1, 2, 3" | "Sistema de autenticação completamente fora: como investigar, comunicar, escalar" |
| **Audiência** | Dev on-call na madrugada | Time de resposta a incidentes (SRE, TL, PMs) |

**Runbook:** atenua ou remove paliativamente uma falha específica monitorada, reduzindo erros humanos sob pressão.  
**Playbook:** aborda estratégias macro decisórias para colapsos sistêmicos profundos.

---

## Lei de Conway

> "Organizations which design systems are constrained to produce designs which are copies of the communication structures of those organizations." — Melvin Conway (1968)

**Implicação prática:** a arquitetura do seu software reflete a estrutura do seu time.

- Time com frontend separado + backend separado + QA separado → sistema monolítico com silos
- Times em squads multifuncionais por domínio → serviços mais coesos e independentes

**Por que o Spotify Model surgiu:** visava quebrar divisões arcaicas (frontend, backend, QA isolados) que fragmentavam a cadeia de inovação, substituindo por squads multifuncionais por missão/produto.

**Lição:** antes de refatorar a arquitetura, observe a estrutura organizacional. Se o time está fragmentado, o código estará também.

---

## DORA Metrics — Medir a Saúde de Entrega

As 4 métricas do DevOps Research and Assessment:

| Métrica | O que mede | Elite |
|---------|-----------|-------|
| **Deployment Frequency** | Com que frequência deploya | Várias vezes/dia |
| **Lead Time for Changes** | Commit → produção | < 1 hora |
| **Change Failure Rate** | % de deploys que causam incidentes | < 5% |
| **Time to Restore Service** | MTTR após incidente | < 1 hora |

Times de alto desempenho fazem deploy várias vezes por dia com MTTR < 1 hora. Times de baixo desempenho fazem deploy 1×/mês com MTTR de dias.

---

## Empresa com Tecnologia como Vantagem vs. Problema

| Vantagem Competitiva | Problema Crônico |
|---------------------|-----------------|
| Deploy várias vezes por dia | Deploy uma vez por mês |
| Code review obrigatório | Código sem review |
| Testes automatizados | Testes manuais |
| Dívida técnica gerenciada | Dívida técnica crescendo |
| Postmortem sem culpa | Postmortem com busca de culpado |
| Documentação atualizada | Documentação inexistente |
| Time pequeno e autônomo | Time grande e burocrático |
| Métricas monitoradas | Métricas desconhecidas |

---

## Referências
- [[documentacao-prd-rfc-dod|ADRs e documentação técnica →]]
- [[cultura-engenharia-forte|Cultura de Engenharia Forte →]]
- [[estrutura-time-produto|Estrutura de Time →]]
