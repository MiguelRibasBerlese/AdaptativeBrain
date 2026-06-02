---
type: conhecimento
status: active
tags: [padrão, equipe, squads, spotify, tribos, papéis, cultura-engenharia, onboarding]
created: 2026-06-01
updated: 2026-06-01
---

# 👥 Estrutura de um Time de Desenvolvimento Profissional

> Como organizar times de produto — do squad ao modelo Spotify, e como profissionalizar com recursos limitados.

---

## Modelo Spotify — Squads, Tribos, Capítulos, Guildas

O modelo mais usado por empresas de tecnologia como referência (mas com ressalvas).

```
TRIBO (40–150 pessoas) ← Trio TPD aqui
├── SQUAD 1 (6–12 pessoas)
│   └── PM + Tech Lead + devs + designer
├── SQUAD 2 (6–12 pessoas)
│   └── PM + Tech Lead + devs + designer
│
CAPÍTULOS (especialistas na mesma disciplina)
└── Frontend, Backend, QA/UX
    (mantêm padrões de engenharia na disciplina)

GUILDA (voluntária entre todas as tribos)
└── Comunidade de interesse: "Grupo de AI", "Grupo de Performance"
```

| Estrutura | Descrição | Tamanho |
|-----------|-----------|---------|
| **Squad** | Equipe autônoma multifuncional | 6–12 pessoas |
| **Tribo** | Vários squads na mesma área de função | 40–150 pessoas |
| **Capítulo** | Especialistas na mesma disciplina (JS devs, DBAs) | Variável |
| **Guilda** | Comunidade voluntária de interesse entre tribos | Variável |

### Como Funciona na Prática
- Squads escolhem sua metodologia (Scrum, Kanban, Scrumban)
- Trio TPD (Tribo + Produto + Design) garante alinhamento
- Capítulos mantêm padrões de engenharia na disciplina
- Guildas são voluntárias — ninguém é forçado a participar

> ⚠️ **Importante:** O Spotify **não usa mais o modelo original** — evoluiu conforme cresceu. Muitas empresas copiaram a estrutura sem entender a cultura por trás.

---

## Trio TPD — Como Funciona na Prática (Spotify)

**Trio TPD = Tribe Lead + Product Lead + Design Lead**

| Membro | Representa | Responsabilidade |
|--------|-----------|-----------------|
| **Tribe Lead** (Tecnologia) | Visão técnica | Viabilidade, arquitetura, recursos |
| **Product Lead** (Produto) | Visão de negócio | ROI, prioridades, métricas de sucesso |
| **Design Lead** (Design) | Visão de usuário | UX, pesquisa, protótipos |

- Toda iniciativa média/grande é conduzida pelo Trio TPD
- Decisões conjuntas — nenhum dos 3 decide sozinho
- Roadmap compartilhado e visível para todos

**Exemplo de decisão:**
```
Problema: "Adicionar autenticação em 2 fatores"

Tecnologia: "Precisamos de 2 sprints + 1 semana de testes de segurança"
Produto:    "Isso aumenta retenção em 15% e reduz churn em 8%"
Design:     "Testamos 3 protótipos de UX — podemos fazer sem frustrar o usuário"

Decisão: Lançar em 2 fases (beta 10% → geral)
```

---

## Papéis Essenciais em um Time de Produto

| Papel | Quando contratar | Responsabilidade |
|-------|-----------------|-----------------|
| **Product Manager** | Desde o início (ou founder) | Prioriza, define MVP, faz ponte com negócio |
| **Tech Lead / Senior Dev** | Desde o início | Arquitetura, padrões, mentoria |
| **Dev Full-stack** | 1–3 primeiros | Implementação |
| **Designer (UX/UI)** | Quando tiver produto definido | Experiência, protótipos |
| **QA / Test Engineer** | Após ter 5+ devs | Qualidade, automação de testes |
| **Data Analyst** | Após ter métricas definidas | KPIs, análise |

### Time Mínimo Viável (5–10 pessoas)
- 1 PM (ou founder fazendo papel de PM)
- 1 Tech Lead
- 3–5 Full-stack devs
- 1 Designer (pode ser part-time)

---

## Como Profissionalizar o Setor de TI com Recursos Limitados (5–20 pessoas)

### Passo a Passo

**1. Processos básicos primeiro**
- Daily de 15 min (mesmo remota ou assíncrona)
- Sprint de 2 semanas com planning e review
- PRD simples para cada feature grande

**2. Ferramentas essenciais (baixo custo)**
- **Linear** ou **Jira** para gestão de projetos
- **GitHub/GitLab** para código
- **Notion** ou **Confluence** para documentação
- **Figma** para design

**3. Documentação mínima viável**
- PRD para cada feature grande
- `README.md` com como rodar projeto localmente em < 30 minutos
- RUNBOOK para sistemas críticos
- ADRs para decisões arquiteturais

**4. Cultura desde o início**
- Code review obrigatório (nada mergeia sem revisão)
- Testes automatizados desde o primeiro dia
- Postmortem sem busca de culpado

**5. Onboarding documentado**
- Guia para novo dev rodar projeto em 1 dia
- Documentação de arquitetura básica do sistema

---

## O Que Stripe, Linear e Vercel Fazem Diferente

### Stripe
- **Stripe 101:** programa de onboarding de 2 semanas (100% dos novos entendem produtos)
- **/dev/start:** onboarding técnico com projeto prático real
- Rubricas escritas para todas as entrevistas de contratação
- **StripeSat:** pesquisa de engajamento 2×/ano
- Hackathons internos — 80 pessoas, 7 projetos lançados
- Crescimento lento e deliberado da engenharia — qualidade > quantidade

### Linear
- Usa **sua própria ferramenta** para gestão (construiu o que não existia)
- Trunk-based development (não Git Flow tradicional)
- CI/CD extremamente rápido — deploy em minutos
- Time pequeno com alta autonomia e responsabilidade

### Vercel
- Foco em **Developer Experience (DX)** como produto
- Criou Next.js (framework mais usado para React)
- Infraestrutura em nuvem otimizada para frontend
- Cultura remote-first global

---

## Cultura de Engenharia — Como se Forma

Cultura de engenharia é o conjunto de **valores, práticas e comportamentos** que definem como uma organização desenvolve software.

### Elementos Formadores

1. Padrões de código compartilhados e **seguidos** (não só escritos)
2. Code review como norma, não opção
3. Testes automatizados como parte do processo
4. CI/CD — deploy automático, não manual
5. Feedback contínuo — não apenas review anual
6. Aprendizado constante — hackathons, tech talks, conferências

### Como Construir

- Liderança modela o comportamento esperado
- Documentação clara de expectativas
- Reconhecimento público do que é valorizado
- Consistência — práticas aplicadas **sempre**, não quando é conveniente

---

## Como Empresas Desenvolvem Desenvolvedores Internamente

1. **Code review como aprendizado** — seniors explicam o "por quê", não só o "o quê"
2. **Plano de desenvolvimento individual (IDP)** — todo dev tem um plano de crescimento
3. **Orçamento para conferências e cursos**
4. **Internal tech talks** — devs ensinam outros devs (1×/mês)
5. **Pair programming** para transferência de conhecimento
6. **Mentoria** — novos devs têm mentor dedicado (modelo Stripe)

---

## Roles Inovadores em Empresas de Alto Desempenho

### Vercel — "Design Engineers"

A Vercel criou o role de **Design Engineer** — profissional híbrido que integra design e código simultaneamente:

- Não existe handoff de assets visuais para outro departamento
- O mesmo profissional concebe a paleta interacional de acessibilidade E implementa o código
- Elimina o gargalo clássico de "design → review → handoff → implementação"
- Resultado: features de UI entregues com mais velocidade e fidelidade ao design original

### Stripe — "Minions" (AI Coding Agents)

A Stripe criou agentes de IA internos chamados **"minions"** — sistemas de AI que:

- Operam via **reações do Slack** (um emoji dispara um agente)
- Geram automaticamente PRs de código — **1.300+ PRs por semana**
- Trabalham em tarefas repetitivas de refatoração, atualização de dependências, geração de testes
- Rodam com mínima supervisão humana em ciclos iterativos contínuos

**Outros diferenciais Stripe:**
- **15 Essential Journeys**: as 15 jornadas críticas do usuário que nunca podem quebrar
- **PQR (Product Quality Review)**: revisão formal antes de qualquer liberação sistêmica — garante qualidade coesa, não só estética

---

## Referências
- [[metodologias-ageis-pratica|Metodologias Ágeis na Prática →]]
- [[cultura-engenharia-forte|Cultura e Mentalidade de Tecnologia Forte →]]
- [[ferramentas-engenharia|Ferramentas e Sistemas →]]
