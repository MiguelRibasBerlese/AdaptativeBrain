---
type: conhecimento
status: active
tags: [padrão, okr, kpi, priorizacao, rice, wsjf, cost-of-delay, roadmap]
created: 2026-06-01
updated: 2026-06-01
---

# 🎯 OKRs vs KPIs e Frameworks de Priorização

> Como empresas de tecnologia definem e medem sucesso — e como priorizar o que construir.

---

## OKR vs. KPI — A Diferença Fundamental

**KPI** = métrica isolada que monitora o estado atual  
**OKR** = sistema de objetivos que direciona para onde ir

> "KPI te diz **como você está indo AGORA**. OKR te diz **por que importa chegar até aqui, tão rápido**."

| Característica | KPI | OKR |
|---------------|-----|-----|
| O que é | Métrica isolada | Sistema de objetivos |
| Foco temporal | "Como estou AGORA" | "Por que importa chegar rápido" |
| Estados | Monitora steady-state | Descreve outcomes |
| Propósito | Monitoramento contínuo | Estratégia + execução |
| Ciclo | Contínuo (dashboard) | Trimestral/semestral |
| Ligação a remuneração | Frequente | Evitar — distorce comportamento |
| Transparência | Depende da empresa | Obrigatoriamente público |

**Modelo correto:** KPIs e OKRs coexistem, não se substituem.

```
KPIs (dashboard contínuo)
├── Receita mensal (MRR)
├── Churn rate
├── NPS
└── Tempo de resposta ao cliente

OKRs (trimestral, estratégico)
├── O1: Expandir para novo mercado
│   ├── KR1: 1.000 usuários no Brasil
│   └── KR2: R$ 500k MRR no Brasil
└── O2: Melhorar qualidade do produto
    ├── KR1: Aumentar NPS de 30 → 50
    └── KR2: Reduzir bugs críticos em 80%
```

---

## OKR — Estrutura e Boas Práticas

### Formato

```
Objetivo: [Inspirador, qualitativo, ambicioso]
  KR1: [Resultado mensurável com valor inicial e final]
  KR2: [Resultado mensurável com valor inicial e final]
  KR3: [Resultado mensurável com valor inicial e final]
```

### Exemplo — Área de Engenharia

```
O: Tornar a plataforma confiável o suficiente para grandes clientes
  KR1: Uptime de 99.9% (hoje: 99.2%)
  KR2: MTTR < 1 hora (hoje: 4 horas)
  KR3: Cobertura de testes > 80% (hoje: 45%)
```

### Boas Práticas de OKR

- **3–5 Objetivos** por time por quarter
- **3–4 Key Results** por Objetivo
- **KRs são outcomes** (resultados), não outputs (atividades)
  - ❌ "Implementar sistema de monitoramento" (output)
  - ✅ "Reduzir MTTR de 4h para 1h" (outcome)
- **OKR não é lista de tarefas** — projetos/features são inputs para alcançar KRs
- **OKR de 70% é ótimo** — se você atinge 100% toda vez, o objetivo era fácil demais

### OKR vs. Projetos

| | Projeto | OKR |
|--|---------|-----|
| Foco | Esforços/entregáveis | Resultados |
| Exemplos | "Implementar feature de login" | "Aumentar retenção em 20%" |
| Formato | Checklist | Meta com valor inicial e final |
| Dependência | Não precisa de indicadores | Depende de KPIs |

---

## Como KPI Vira OKR

Quando um KPI começa a piorar, ele deve virar um Key Result de OKR.

```
KPI existente (monitoramento):
  Tempo médio de resposta ao suporte: 48h
  Status: piorando, aumentando reclamações

→ Transformar em KR de OKR:

O: Melhorar experiência de atendimento ao cliente
  KR1: Reduzir tempo médio de resposta de 48h → 24h
  KR2: Aumentar CSAT de 3.5 → 4.2
  KR3: Reduzir tickets abertos em 20%
```

**Regra:** KPI ruim → OKR para melhorá-lo. KPI bom → não precisa de OKR.

---

## Frameworks de Priorização

### RICE Score

```
RICE = (Reach × Impact × Confidence) / Effort
```

- **Reach:** quantas pessoas afetadas por período (ex: 500 usuários/mês)
- **Impact:** escala 0.25 / 0.5 / 1 / 2 / 3
- **Confidence:** % de certeza nas estimativas (100%, 80%, 50%)
- **Effort:** horas-pessoa total

Maior RICE → maior prioridade. Usado por Intercom e muitas startups.

### Value vs. Effort Matrix

4 quadrantes:
- **Quick Wins** (alto valor, baixo esforço) → fazer primeiro
- **Big Bets** (alto valor, alto esforço) → planejar e investir
- **Fill-ins** (baixo valor, baixo esforço) → fazer se sobrar tempo
- **Time Sinks** (baixo valor, alto esforço) → não fazer

### WSJF — Weighted Shortest Job First (SAFe)

```
WSJF = Cost of Delay / Tamanho do Job
```

Maximiza o valor econômico entregue por unidade de tempo.

```
Item A: CoD R$ 10k/dia, Tamanho 5 dias  → WSJF = 2.000
Item B: CoD R$  5k/dia, Tamanho 2 dias  → WSJF = 2.500 ← PRIMEIRO
Item C: CoD R$ 15k/dia, Tamanho 10 dias → WSJF = 1.500
```

**WSJF é superior ao RICE** quando há dados de custo financeiro disponíveis.

### MoSCoW

- **Must have** — sem isso não entrega (P0)
- **Should have** — importante mas workaround existe (P1)
- **Could have** — desejável se houver tempo (P2)
- **Won't have** — explicitamente fora do escopo

---

## Cost of Delay (CoD) — Priorizando com Dados Financeiros

CoD quantifica **quanto perdemos financeiramente por não entregar hoje**.

```
CoD = Receita perdida/dia + Custo adicional/dia + Valor perdido/dia
```

### Tipos de CoD

| Tipo | Perfil | Exemplo |
|------|--------|---------|
| **Urgente** | Sobe rapidamente, não espera | Bug de segurança: R$ 50k/dia de risco |
| **Data Fixa** | Baixo até a data, depois despenca | Feature para Black Friday: zero depois |
| **Linear** | Cresce linearmente todo dia | Feature de retenção: R$ 1k/dia de churn |
| **Intangível** | Baixo/zero | Refatoração: R$ 500/dia de dev mais lento |

### Backlog Priorizado por CoD + WSJF

```
Rank | Item         | CoD/dia | Tamanho | WSJF  | Classe
1    | Bug crítico  | 50.000  | 1 dia   | 50.000 | Expedite
2    | Feature B    |  5.000  | 2 dias  |  2.500 | Standard
3    | Feature A    | 10.000  | 5 dias  |  2.000 | Standard
4    | Integração Z | 15.000  | 10 dias |  1.500 | Fixed Date
5    | Refatoração  |    500  | 3 dias  |    167 | Intangible
```

---

## Quando Usar Cada Framework

| Situação | Framework Recomendado |
|---------|----------------------|
| Startup early-stage, pouco dados | MoSCoW + intuição |
| Time com dados de uso/receita | RICE ou WSJF |
| Decisão financeira importante | WSJF + Cost of Delay |
| Alinhamento estratégico | OKR |
| Monitoramento de saúde | KPI dashboard |
| Roadmap trimestral | OKR + roadmap por tema |

---

## Referências
- Whatmatters.com — OKR Playbook (Google)
- [[gestao-projetos-internos|Gestão de Projetos Internos →]]
- [[metricas-fluxo-kanban|Métricas de Fluxo Kanban →]]
- [[erros-projetos-software|Erros Comuns em Projetos →]]
