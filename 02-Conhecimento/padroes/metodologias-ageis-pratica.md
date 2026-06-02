---
type: conhecimento
status: active
tags: [padrão, scrum, kanban, shape-up, agile, scrumban, sprint, metodologia]
created: 2026-06-01
updated: 2026-06-01
---

# ⚡ Metodologias Ágeis na Prática Real

> A diferença entre o que os livros dizem e o que as empresas realmente fazem.

---

## Scrum na Prática (Não Como nos Livros)

### Cerimônias Reais

| Cerimônia | Duração real | O que realmente acontece |
|-----------|-------------|--------------------------|
| **Sprint Planning** | 2–4 horas | Time seleciona 5–8 items do backlog que consegue entregar em 2 semanas |
| **Daily** | 15 min | Cada dev: o que fiz ontem, o que farei hoje, bloqueios |
| **Sprint Review** | 1 hora | Demonstração do que foi feito, feedback dos stakeholders |
| **Retrospective** | 1 hora | Time discute o que melhorar no processo — define 1–2 ações |

### Teoria vs. Prática

| Teoria | Prática real |
|--------|-------------|
| Sprint de 2–4 semanas fixas, papéis rigorosos | Muitas empresas usam Scrumban (híbrido Scrum + Kanban) |
| Product Owner único decide tudo | PMs colaboram com tech leads na priorização |
| Backlog perfeitamente refinado | Backlog é vivo, itens refinados continuamente |
| Daily obrigatória e formal | Muitas empresas fazem async ou integram no Slack |
| Story points para tudo | Times maduros estimam em dias ou usam tamanhos de camiseta (P/M/G) |

---

## Kanban na Prática

- Quadro visual: **To Do → In Progress → Review → Done**
- **WIP Limits** (Work In Progress): máximo de 1–2 tarefas por dev em paralelo
- Foco em fluxo contínuo, não em sprints
- Ideal para: suporte, manutenção, times com muitas interrupções
- Métricas centrais: Cycle Time, Throughput, Lead Time

### WIP Limits por Tamanho de Time

| Time | WIP total | WIP por pessoa |
|------|-----------|----------------|
| 3 pessoas | 4–5 | 1–2 |
| 5 pessoas | 6–8 | 1–2 |
| 8 pessoas | 10–12 | 1–2 |
| 10+ pessoas | 15–18 (split por sub-time) | 1–2 |

**Fórmula inicial:** `WIP = 1.5 × número de pessoas`

---

## Shape Up (Basecamp) — Alternativa ao Scrum

Shape Up é a metodologia criada pela Basecamp, documentada no livro gratuito em [basecamp.com/shapeup](https://basecamp.com/shapeup).

### Princípios

| Conceito | O que é |
|----------|---------|
| **Ciclos de 6 semanas** | Tempo suficiente para entregar algo significativo sem pressão artificial |
| **Apetite, não estimativa** | "Vale 2 semanas de trabalho?" — não "quantas story points?" |
| **Cool-down de 2 semanas** | Entre ciclos, para reparos, bugs e aprendizado |
| **Betting, not planning** | Equipe escolhe no que "apostar" — não planeja tudo |
| **Low-hierarchy** | Equipes têm autonomia para decidir como executar |
| **3 fases** | Frame → Shape → Build |

### Shape Up vs. Scrum

| | Scrum | Shape Up |
|--|-------|----------|
| Ciclo | 2 semanas | 6 semanas |
| Estimativa | Story points | Apetite (tempo disponível) |
| Backlog | Refinado continuamente | Pitches escolhidos no Betting |
| Interrupção | Sprint protection | Cool-down absorve dívida |
| Origem | Scrum.org | Basecamp/37signals |

---

## Scrumban — Quando e Como Usar

Scrumban é o híbrido entre Scrum e Kanban. Funciona quando:
- Time tem mix de features novas (Scrum) + bugs/suporte (Kanban)
- Produto maduro com muitas interrupções

### O Que Integrar de Cada Um

| Elemento | Do Scrum | Do Kanban | Como equilibrar |
|----------|---------|---------|----------------|
| Planejamento | Sprint planning (2 semanas) | Contínuo | Planning + novos items entram no backlog anytime |
| Execução | Commit para sprint | Fluxo contínuo | Sprint como meta, mas work flui |
| WIP | Sem WIP limits | WIP limits | Adicionar WIP limits ao sprint |
| Cerimônias | Daily, review, retro | Sem cerimônias fixas | Daily + retro de sprint; review opcional |
| Métricas | Velocity | Cycle time, throughput | Usar ambos |

### Por que Scrumban Falha

- Scrum Master vira "gerente de tarefas"
- Sprints sem duração fixa
- WIP limits não são respeitados
- Daily vira relatório de status sem resolver bloqueios
- **Resultado: pior que Scrum puro OU Kanban puro**

**Regra de ouro:** Comece com UM framework completo por 3–6 meses. Só crie híbrido depois de dominar os dois.

---

## Linear Method — Uma Visão Diferente

A Linear, amplamente copiada por startups em hipercrescimento, desenvolveu o **Linear Method** como alternativa ao Scrum tradicional.

### User Stories são um Anti-Pattern

A Linear argumenta que user stories ("Como usuário, eu quero X para obter Y") consomem tempo excessivo e **obscurecem a clareza técnica**. Em vez disso, usam **Issues** — descrições curtas e concretas do problema em linguagem pragmática:

❌ User story: "Como gestor, eu quero um relatório consolidado para acompanhar a equipe"  
✅ Issue: "Relatório semanal de performance por squad — agregar dados de X, Y, Z"

### Ciclos Semanais Curtos

Em vez de sprints de 2 semanas, a Linear usa ciclos semanais **com um único propósito: criar cadência**. O objetivo não é entregar tudo — é evitar que equipes corram freneticamente até o esgotamento no fim do ciclo.

### Directives e Initiatives

- Liderança define **Directions** (direções estratégicas) e **Initiatives** (iniciativas)
- Execução e granularidade da solução ficam com equipes transversais pequenas
- Projetos são filtrados como "enablers" (multiplicam engajamento) ou "blockers" (removem atrito)
- **Scope down:** se não pode ser finalizado em 1–3 semanas por 1–3 pessoas, fatia em entregas menores

---

## Como Empresas Específicas Estruturam o Processo

### Basecamp
- Usa apenas Shape Up (sem Scrum)
- 6 semanas de trabalho + 2 de cool-down
- Equipes de 3–5 pessoas com alta autonomia

### Linear
- Desenvolveu sua própria ferramenta de gestão
- **Trunk-based development** (não Git Flow tradicional)
- CI/CD altamente automatizado — deploy em minutos
- Time pequeno, alta autonomia

### Notion
- Combina Scrum + Kanban
- Usa sua própria ferramenta para gestão
- Foco em documentação primeiro (PRDs, RFCs)

---

## Papel de PM vs. Tech Lead vs. Desenvolvedor

| Papel | Responsabilidade principal | Tomada de decisão |
|-------|---------------------------|------------------|
| **Product Manager** | "O que" construir e "por quê" | Prioriza backlog, define MVP |
| **Tech Lead** | "Como" construir tecnicamente | Arquitetura, padrões de código, estimativas |
| **Desenvolvedor** | Escrever código de qualidade | Implementação técnica, code review |
| **Designer** | Experiência do usuário | Fluxos, UI, protótipos |
| **QA** | Qualidade e testes | Casos de teste, automação |
| **Data Analyst** | Métricas e análise | Definição de KPIs, acompanhamento |

---

## Classes de Serviço no Kanban

Classes de serviço definem política de como tratar cada tipo de trabalho — não é só "prioridade alta/média/baixa".

| Classe | CoD | Ação | Exemplo |
|--------|-----|------|---------|
| **Expedite** (🔴) | Muito alto — sobe rapidamente | Time PARA e foca, quebrar WIP | Bug crítico de produção |
| **Fixed Date** (🟡) | Alto após data específica | Planejar com antecedência | Feature para evento de lançamento |
| **Standard** (🔵) | Moderado, cresce linearmente | Puxar quando WIP permitir | Features normais |
| **Intangible** (🟢) | Baixo/zero | Fazer quando sobrar tempo | Refatoração, dívida técnica |

**Distribuição ideal de capacidade:**
- Standard: 60–70%
- Fixed Date: 10–20%
- Expedite: 0–5% (se > 20%, processo está quebrado)
- Intangible: 10–20%

---

## Referências
- Shape Up (gratuito): [basecamp.com/shapeup](https://basecamp.com/shapeup)
- Getting Real (gratuito): [gettingreal.37signals.com](https://gettingreal.37signals.com)
- [[metricas-fluxo-kanban|Métricas de Fluxo Kanban →]]
- [[gestao-projetos-internos|Gestão de Projetos Internos →]]
