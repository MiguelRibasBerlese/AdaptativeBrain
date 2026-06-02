---
type: conhecimento
status: active
tags: [padrão, gestão, produto, discovery, priorizacao, okr, roadmap]
created: 2026-06-01
updated: 2026-06-01
---

# 🏢 Como Empresas de Tecnologia Criam Projetos Internos

> Como Google, Meta, Spotify, Netflix e startups de alto crescimento decidem o que construir — do ponto de vista prático.

---

## Como Empresas Decidem o Que Construir

### Modelo Top-Down + Bottom-Up

**Google / Meta:**
- OKRs trimestrais definem prioridades da empresa (top-down)
- Engenheiros submetem propostas via RFC — revisadas pela liderança (bottom-up)
- Todo projeto significativo começa com RFC de 2–5 páginas: problema, solução proposta, métricas de sucesso

**Spotify — Trio TPD:**
- Cada tribo tem um Trio: Tribe Lead (tecnologia) + Product Lead (produto) + Design Lead (design)
- Decisões sempre tomadas em conjunto pelos três — nenhum decide sozinho
- Squads têm autonomia para escolher metodologia (Scrum, Kanban, Scrumban)

**Netflix — Contexto, não Controle:**
- Liderança fornece contexto estratégico; equipes decidem como executar
- Projetos priorizados por impacto no usuário e alinhamento com valores da empresa

**Startups (Linear, Vercel):**
- Decisões centralizadas nos fundadores no início
- Transição gradual para processos estruturados conforme escala

---

## Quem Participa da Decisão

| Papel | Participação |
|-------|-------------|
| **Product Manager** | Define o "o que" e "por quê", prioriza backlog |
| **Tech Lead** | Avalia viabilidade técnica, estima esforço |
| **Desenvolvedores** | Participam do refinement, dão feedback sobre complexidade |
| **Liderança (CTO/VP Eng)** | Aprova projetos estratégicos, aloca orçamento |
| **Stakeholders de negócio** | Fornecem requisitos, validam prioridades |
| **Designer** | Contribui com perspectiva de UX no discovery |

---

## Como Priorizam o Que Desenvolver vs. Descartar

### Frameworks de Priorização

**RICE Score** (usado por Intercom e startups):
```
RICE = (Reach × Impact × Confidence) / Effort
```
- Reach: quantas pessoas afetadas
- Impact: escala 0.25 a 3
- Confidence: % de certeza nas estimativas
- Effort: horas-pessoa

**Value vs. Effort Matrix:**
- Projetos de alto valor e baixo esforço → primeiro
- Dividir em 4 quadrantes: Quick Wins / Big Bets / Fill-ins / Time Sinks

**WSJF — Weighted Shortest Job First** (SAFe):
```
WSJF = Cost of Delay / Tamanho do Job
```
Prioriza pelo maior valor econômico por unidade de esforço.

### O Que Faz um Projeto Ser Descartado
- Não alinha com OKRs trimestrais
- Impacto no usuário não é claro ou mensurável
- Esforço técnico desproporcional ao valor
- Não passou pelo product discovery adequado

---

## Product Discovery — Como Funciona na Prática

Product Discovery é o processo de identificar e compreender as necessidades dos usuários para determinar **o que deve ser construído e por quê**.

### 4 Fases

**1. Entender o problema (1–2 semanas)**
- Entrevistas com usuários internos/externos
- Análise de dados existentes
- Mapeamento de jornada do usuário

**2. Explorar soluções (1 semana)**
- Brainstorming com equipe multifuncional
- Sketching e prototipação rápida
- Avaliação de alternativas (build vs buy)

**3. Validar hipóteses (1–2 semanas)**
- Testes com protótipos (Figma, papel)
- MVP conceitual sem código
- Medição de interesse real

**4. Definir roadmap (1 semana)**
- PRD escrito e aprovado
- Estimativas de esforço
- Plano de entrega

---

## Antes de Escrever a Primeira Linha de Código

Checklist obrigatório em empresas maduras:
- [ ] PRD aprovado por stakeholders
- [ ] RFC técnico revisado pela engenharia
- [ ] Design system aprovado (se UI)
- [ ] Métricas de sucesso definidas
- [ ] Definition of Done estabelecida
- [ ] Build vs Buy avaliado

---

## Roadmap vs. Backlog

| | Roadmap | Backlog |
|--|---------|---------|
| **Horizonte** | Trimestral/semestral | Sprint/semana |
| **Formato** | Temas ou objetivos | Stories/tasks |
| **Dono** | PM + liderança | PM + time |
| **Mudança** | A cada quarter | Contínua |
| **Visão** | Estratégica | Operacional |

---

## Referências
- [[documentacao-prd-rfc-dod|PRD, RFC e Definition of Done →]]
- [[metodologias-ageis-pratica|Metodologias Ágeis na Prática →]]
- [[validacao-pre-desenvolvimento|Validar Antes de Construir →]]
- [[okr-kpi-priorizacao|OKRs vs KPIs e Priorização →]]
