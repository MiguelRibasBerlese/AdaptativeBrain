---
type: conhecimento
status: active
tags: [padrão, métricas, kanban, cycle-time, lead-time, throughput, wip, monte-carlo, lei-de-little]
created: 2026-06-01
updated: 2026-06-01
---

# 📊 Métricas de Fluxo Kanban

> Cycle Time, Lead Time, Throughput, WIP, Monte Carlo e Lei de Little — as métricas que times maduros usam para prever e melhorar entrega.

---

## As 4 Métricas Primárias de Fluxo

### 1. Cycle Time (Tempo de Ciclo)

```
Cycle Time = Data de Conclusão − Data de Início
(Quando o time COMEÇA a trabalhar → Quando TERMINA)
```

- NÃO inclui tempo no backlog esperando
- Mede eficiência interna do time
- Use para: identificar gargalos, planejar capacidade, retrospectiva

**Meta:** Reduzir cycle time ao longo do tempo.

### 2. Lead Time (Tempo de Atendimento)

```
Lead Time = Data de Conclusão − Data de Comprometimento
(Quando o time se COMPROMETE com o item → Quando ENTREGA)
```

- Inclui tempo no backlog após comprometimento
- Mede eficiência do processo de entrega
- Use para: comparar com SLA, reportar para stakeholders

### 3. Customer Lead Time

```
Customer Lead Time = Data de Entrega − Data de Solicitação
(Quando cliente PEDIU → Quando RECEBEU)
```

- Inclui upstream (descoberta, ideação, priorização)
- Mede satisfação do cliente — visão externa
- Sempre: Customer Lead Time ≥ Lead Time ≥ Cycle Time

**Relação:**
```
Customer Lead Time = Tempo Upstream + Lead Time
Lead Time          = Cycle Time + Tempo de espera pós-comprometimento
```

### 4. Throughput (Taxa de Entrega)

```
Throughput = Items Completados / Período
```

Exemplo: 12 items / 2 semanas = 6 items/semana

- Mede capacidade do time
- Use para: planejamento de sprint, previsão de entrega
- Meta: Throughput estável ou crescente

---

## WIP — Work In Progress

WIP = quantidade de trabalho começado mas não completado.

**Lei de Little (fundamental):**
```
Lead Time = WIP / Throughput
```

**Implicação direta:** Reduzir WIP reduz Lead Time.

```
Exemplo:
WIP: 10 items | Throughput: 2 items/dia → Lead Time: 5 dias
WIP:  6 items | Throughput: 2 items/dia → Lead Time: 3 dias (40% ↓)
```

### WIP Limits por Tamanho de Time

| Time | WIP total recomendado | WIP por pessoa |
|------|----------------------|----------------|
| 3 pessoas | 4–5 | 1–2 |
| 5 pessoas | 6–8 | 1–2 |
| 8 pessoas | 10–12 | 1–2 |
| 10+ pessoas | 15–18 | 1–2 |

**Fórmula inicial:** `WIP = 1.5 × número de pessoas`

### WIP Limits por Coluna

| Coluna | WIP típico | Observação |
|--------|-----------|------------|
| To Do | Ilimitado | Backlog pode crescer |
| In Progress | 1–2 por dev | Principal coluna a limitar |
| Review | 2–3 total | Não deixar code review acumular |
| QA/Testing | 2–3 total | Gargalo comum — enxame se acumula |
| Done | Ilimitado | Final do fluxo |

**Regra:** Se qualquer coluna (exceto To Do/Done) está **cheia**, pare de puxar novo trabalho e ajude a completar.

---

## Identificando Gargalos com a Lei de Little

Aplique a Lei de Little em cada coluna separadamente:

```
Lead Time por coluna = WIP da coluna / Throughput da coluna
```

| Coluna | WIP | Throughput/dia | Lead Time na coluna |
|--------|-----|---------------|---------------------|
| In Progress | 4 | 4 | 1 dia |
| Review | 5 | 2.5 | 2 dias |
| QA | **8** | **2** | **4 dias** ← GARGALO |

**Gargalo = coluna com maior Lead Time E maior WIP E menor Throughput.**

---

## Métricas de Variabilidade

Média simples de Cycle Time (ex: 7 dias) não basta — variabilidade é o que impacta previsibilidade.

```
Coeficiente de Variação = Desvio Padrão / Média

< 20% → Variabilidade BAIXA → Previsão confiável
20–40% → Variabilidade MÉDIA → Previsão aceitável
> 40% → Variabilidade ALTA → Ação urgente
```

### Percentil 85 — Promessa Confiável

Em vez de prometer a média (50% de chance de acertar), prometa o percentil 85:

```
Percentil 85 ≈ Média + 1.5 × Desvio Padrão
```

Exemplo:
- Média: 7 dias, Desvio padrão: 0.7 → P85 = 8 dias → "85% de chance de entregar em ≤ 8 dias"
- Média: 8 dias, Desvio padrão: 5.5 → P85 = 16 dias → "85% de chance de entregar em ≤ 16 dias"

---

## Previsão Baseada em Throughput

### Método Simples (Média)

```
Sprints necessárias = Items pendentes / Throughput médio

Exemplo:
  Items pendentes: 50
  Throughput médio: 10 items/sprint
  → 5 sprints = 10 semanas (+/- 20% buffer)
```

### Monte Carlo Simulation (85–95% confiança)

Monte Carlo usa dados históricos reais de Throughput para simular 1000+ cenários possíveis.

**Como funciona:**
1. Coleta Throughput diário dos últimos 30–60 dias
2. Para cada simulação: sorteia Throughput aleatório dos dados históricos
3. Calcula dias necessários = items pendentes / throughput sorteado
4. Repete 1000+ vezes
5. Visualiza percentis: 50%, 75%, 85%, 95%

**Resultado:**
```
50% de chance: entregar até 12/07
75% de chance: entregar até 20/07
85% de chance: entregar até 29/07  ← use esta para stakeholders
95% de chance: entregar até 05/08
```

**Implementação simples (Python):**
```python
import numpy as np
from datetime import datetime, timedelta

throughput_historico = np.array([4, 5, 6, 7, 7, 8, 8, 9, ...])  # dados reais
items_pendentes = 50
n_simulacoes = 1000

datas = []
for _ in range(n_simulacoes):
    tp = np.random.choice(throughput_historico)
    dias = items_pendentes / tp
    datas.append(datetime.now() + timedelta(days=dias))

datas = np.array(datas)
print(f"85%: {np.percentile(datas, 85).strftime('%d/%m')}")
```

### Quando Usar Monte Carlo vs. Média Simples

| Situação | Método recomendado |
|---------|-------------------|
| Estimativa rápida | Média simples |
| Decisão importante (lançamento, contrato) | Monte Carlo |
| Throughput variável (coef. variação > 30%) | Monte Carlo |
| Histórico < 30 dias | Média com buffer manual |

---

## Throughput Calibrado (Excluindo Bloqueios Externos)

Bloqueios por dependências externas inflariam o throughput bruto.

```
Throughput calibrado = Items completados − Items bloqueados por causa externa
```

Use throughput calibrado no Monte Carlo para previsões mais precisas.

**Quando calibrar:** bloqueios externos > 10% dos items.
**Quando não calibrar:** bloqueios raros (< 5%) ou internos (parte do processo).

---

## Retrospectiva com Métricas de Fluxo

**Estrutura (1 hora):**

1. **Abertura (5 min)** — revisar ação da retro anterior
2. **Revisar items concluídos (10 min)** — começar pela coluna Done
3. **Inspecionar métricas de fluxo (20 min):**
   - Cycle Time trend (subindo ou descendo?)
   - Throughput (estável?)
   - WIP (dentro do limite?)
   - CFD — bandas largas indicam instabilidade
4. **Identificar causas raiz (15 min)** — 1–3 aspectos para melhorar
5. **Definir ações (10 min)** — máximo 3 ações, cada uma com responsável e deadline

**Erro comum:** buscar dados na hora da retrospectiva. Preparar métricas antes.

---

## Custo do Atraso (Cost of Delay) e WSJF

### Cost of Delay (CoD)

```
CoD = Quanto perdemos por DIA ao não entregar um item
```

| Tipo de Item | CoD Exemplo |
|-------------|------------|
| Feature de pagamento | R$ 10.000/dia de receita perdida |
| Bug de segurança | R$ 50.000/dia de risco de multa |
| Melhoria de UX | R$ 2.000/dia de churn evitado |
| Refatoração técnica | R$ 500/dia de dev mais lento |

### WSJF — Weighted Shortest Job First

```
WSJF = CoD / Tamanho do Job (esforço em dias)
```

Priorize pelo **maior WSJF** — máximo valor por unidade de esforço.

```
Item A: CoD R$ 10k/dia, tamanho 5 dias → WSJF = 2.000
Item B: CoD R$  5k/dia, tamanho 2 dias → WSJF = 2.500 ← primeiro!
Item C: CoD R$ 15k/dia, tamanho 10 dias → WSJF = 1.500
```

---

## Ferramentas para Métricas de Fluxo

| Ferramenta | Métricas automáticas | Preço |
|-----------|---------------------|-------|
| **Businessmap** (Kanbanize) | Todas + Monte Carlo | $$ |
| **Linear** | Cycle Time, Throughput, WIP | $ |
| **Jira** (com plugins) | CFD, Cycle Time | $$$ |
| **ClickUp** | Cycle Time, Throughput | $ |
| **Google Sheets** | Manual (fórmulas) | Grátis |

**Planilha mínima (grátis):**
```
Coluna A: Item ID
Coluna B: Data Início (In Progress)
Coluna C: Data Conclusão (Done)
Coluna D: Cycle Time = C - B
Coluna E: Percentil 85 = PERCENTIL(D:D; 0,85)
```

---

## Referências
- [[metodologias-ageis-pratica|Metodologias Ágeis na Prática →]]
- [[okr-kpi-priorizacao|OKRs vs KPIs e Priorização →]]
- Businessmap: businessmap.io
- K21 (blog e treinamentos Kanban): br.k21.global
