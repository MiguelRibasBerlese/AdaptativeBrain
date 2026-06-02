---
type: conhecimento
status: ativo
tags: [observabilidade, comparação, guia, decisão, ferramentas]
created: 2026-06-02
updated: 2026-06-02
---

# Observabilidade — Comparação e Guia de Decisão

## Tabela Comparativa Geral

| Ferramenta | Tipo | Open-source | Curva de Aprendizado | Melhor Caso de Uso | Custo |
|-----------|------|-------------|---------------------|-------------------|-------|
| **ELK Stack** | Logs + Search | Sim (Elastic License) | Alta | Logs em grande volume, full-text search | Médio (infra) |
| **Datadog** | Full-stack | Não (SaaS) | Baixa | Startups com múltiplos serviços, setup rápido | Alto ($15+/host) |
| **Splunk** | Logs + SIEM | Não | Alta (SPL) | Enterprise, segurança, compliance | Muito alto |
| **Dynatrace** | Full-stack + AI | Não (SaaS) | Média | Enterprise, infra complexa, causa raiz automática | Alto ($69/host) |
| **New Relic** | Full-stack | Não (SaaS) | Baixa/Média | Times médios, preço por dados (não por host) | Médio-alto |
| **Prometheus + Grafana** | Métricas | Sim (Apache) | Média | Métricas de infra e app, orçamento limitado | Baixo (só infra) |
| **SigNoz** | Full-stack (OTel) | Sim (Apache) | Média | Alternativa self-hosted ao Datadog/New Relic | Muito baixo |
| **Redash** | BI / SQL | Sim (SSPL) | Baixa (precisa SQL) | Dashboards de dados de negócio, relatórios internos | Baixo |

## Stack de Observabilidade Ideal por Contexto

### Startup / Produto Inicial (< 5 serviços, orçamento limitado)

**Recomendação: Prometheus + Grafana + Loki**

```
Por que:
- Zero custo de licença
- Prometheus: métricas de infra e app
- Loki: logs (muito mais barato que ELK)
- Grafana: UI unificada para tudo
- Docker Compose sobe em 10 minutos
```

Alternativa SaaS: **Datadog** (free tier até 5 hosts) ou **New Relic** (100GB/mês grátis)

### Startup em Crescimento (5-50 serviços, time técnico pequeno)

**Recomendação: Datadog**

```
Por que:
- Setup de 5 minutos, dashboards pré-construídos
- APM nativo + logs + infra em um lugar
- Integrações nativas AWS/Kubernetes/Docker
- Time não precisa operar nenhum serviço
- Custo justificável vs custo de engenheiro operando ELK
```

### Scale-up / Empresa Média (50-200 serviços, budget certo)

**Recomendação: SigNoz (self-hosted) + Prometheus**

```
Por que:
- SigNoz: traces distribuídos + logs (OpenTelemetry nativo)
- Prometheus: métricas (mais maduro que SigNoz para métricas)
- Grafana: dashboard unificado conectando ambos
- Custo 10-20x menor que Datadog
- Requer DevOps dedicado para operar ClickHouse
```

### Enterprise (compliance rigoroso, dados sensíveis)

**Recomendação: Dynatrace ou ELK Stack (self-hosted)**

```
Dynatrace quando:
- Infra muito complexa (centenas de microserviços)
- AI para causa raiz automática é crítica
- Time de SRE dedicado
- Budget não é constraint

ELK quando:
- Dados não podem sair da empresa (HIPAA/PCI-DSS)
- Full-text search em logs é requisito
- Time com expertise Elasticsearch
```

**Splunk quando:**
- SIEM é o caso de uso principal (segurança)
- Compliance forense com trilha de auditoria
- Já há investimento em Splunk (lock-in alto)

## Guia de Decisão: "Se você tem X, use Y"

### Por Tipo de Time

| Time | Ferramenta | Motivo |
|------|-----------|--------|
| **DevOps/SRE** | Prometheus + Grafana | Modelo pull, PromQL, alertas customizados |
| **Desenvolvimento** | Datadog ou New Relic | APM, traces, setup simples |
| **Dados/Analytics** | Redash | SQL nativo, múltiplas fontes, BI |
| **Segurança** | Splunk | SIEM, correlação, compliance |
| **Full-stack pequeno** | SigNoz | Tudo em um, open-source, custo baixo |

### Por Constraint

**Se custo é o constraint principal:**
1. Prometheus + Grafana + Loki (open-source, só paga infra)
2. SigNoz self-hosted (~$50-100/mês de infra)
3. New Relic (100GB/mês grátis, depois preço por dados)

**Se velocidade de setup é o constraint:**
1. Datadog (5 minutos, dashboards automáticos)
2. New Relic (agente único, instrumentação automática)

**Se compliance / dados on-premise é o constraint:**
1. ELK Stack self-hosted (controle total)
2. Dynatrace on-premise
3. SigNoz self-hosted (open-source, dados no seu infra)

**Se você quer evitar vendor lock-in:**
1. OpenTelemetry + SigNoz (padrão aberto, troca o backend sem mudar instrumentação)
2. Prometheus (formato de métricas virou padrão de facto)

**Se AI/automação de causa raiz é crítica:**
1. Dynatrace (Davis AI — o mais avançado)
2. Datadog (Watchdog — detecção de anomalias)
3. New Relic (Applied Intelligence — básico)

## Como Ferramentas Se Complementam

### Stack Open-source Completa (LGTM)

```
Loki    → Logs (indexa só labels, 10x mais barato que ELK)
Grafana → Visualização unificada de tudo
Tempo   → Traces distribuídos (OTel nativo)
Mimir   → Métricas Prometheus em escala horizontal
```

```yaml
# Correlação automática no Grafana
# Log → clica em trace ID → abre trace no Tempo
# Trace → clica em span → abre métricas do serviço
# Métrica anômala → clica → abre logs do período
```

### Stack Híbrida (Open-source + Pago)

```
Prometheus (métricas) → Grafana (visualização)
     +
Datadog (APM + logs) → usado só para APM
```

**Por que híbrido?** Prometheus para métricas de infra (free) + Datadog só para APM (pago mas limitado ao necessário) = 60% mais barato que Datadog full.

### Usando OpenTelemetry como Camada de Abstração

```
App → OTel SDK → OTel Collector → SigNoz (dev)
                              → Datadog  (prod)
                              → New Relic (backup)
```

Muda o backend sem mudar o código da aplicação. A **decisão mais importante** de instrumentação é usar OTel desde o início.

## Stack Ideal 2025/2026

Para a maioria das empresas (não Fortune 500):

```
1. Instrumentação: OpenTelemetry (padrão, sem lock-in)
2. Traces: Tempo (free) ou SigNoz
3. Métricas: Prometheus + Grafana
4. Logs: Loki (se budget) ou Datadog Logs (se conveniência)
5. Alertas: Alertmanager (Prometheus) + PagerDuty
6. BI/dados: Redash ou Metabase
```

**Por que essa stack?**
- Custo 70-90% menor que Datadog all-in
- OTel = instrumentação de uma vez, backend trocável
- LGTM (Loki+Grafana+Tempo+Mimir) = observabilidade completa open-source
- Redash = dados de negócio acessíveis para todos os times

## Como Implementar Observabilidade do Zero

### Passo 1: Structured Logging (Dia 1)
```
Antes de qualquer ferramenta, logs devem ser úteis.
→ Usar pino (Node.js) ou structlog (Python)
→ Formato JSON com campos padronizados
→ Incluir: service, environment, requestId, userId, event
```

### Passo 2: Métricas Básicas (Semana 1)
```
→ Expor /metrics em todas as apps (prom-client)
→ Subir Prometheus + Grafana (Docker Compose)
→ Criar dashboard: error rate, latência p99, throughput
```

### Passo 3: Traces Distribuídos (Semana 2-3)
```
→ Adicionar OTel SDK (sem mudar código de negócio)
→ Subir SigNoz ou Tempo
→ Correlacionar trace_id nos logs
```

### Passo 4: Alertas (Semana 3-4)
```
→ Definir SLOs: latência p99 < 200ms, error rate < 1%
→ Criar regras de alerta no Prometheus/Alertmanager
→ Conectar com Slack/PagerDuty
→ Criar runbook para cada alerta crítico
```

### Passo 5: Otimizar (Mês 2+)
```
→ ILM para logs (retenção automática)
→ Dashboards por serviço e por negócio
→ Postmortem estruturado após cada incidente
→ Error budget tracking
```

## Erros Comuns na Implementação

| Erro | Consequência | Solução |
|------|-------------|---------|
| Logs não estruturados | Impossível fazer query | Usar pino/structlog desde o início |
| Alertas baseados em thresholds fixos | 90% são falsos positivos | Baseline dinâmico ou percentis |
| Monitorar tudo sem prioridade | Alert fatigue (ignoram alertas) | Focar nos SLOs do negócio primeiro |
| Começar com ferramenta complexa (ELK) | Semanas instalando, nunca usando | Começar com Grafana Cloud free |
| Não definir SLOs | Não sabe o que é "normal" | Definir SLO antes de criar alertas |
| Trace IDs não propagados | Traces quebrados em microserviços | OTel W3C trace context (propagação automática) |

## Links

- [[observabilidade-fundamentos]] — conceitos fundamentais, SLOs, structured logging
- [[elk-stack]] — ELK Stack detalhado
- [[datadog]] — Datadog detalhado
- [[splunk]] — Splunk detalhado
- [[dynatrace]] — Dynatrace detalhado
- [[new-relic]] — New Relic detalhado
- [[prometheus-grafana]] — Prometheus + Grafana detalhado
- [[signoz-opentelemetry]] — SigNoz + OpenTelemetry detalhado
- [[redash]] — Redash detalhado
