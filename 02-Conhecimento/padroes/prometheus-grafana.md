---
type: conhecimento
status: ativo
tags: [prometheus, grafana, promql, alertmanager, métricas, open-source, observabilidade]
created: 2026-06-02
updated: 2026-06-02
---

# Prometheus + Grafana

## O que é Prometheus

Sistema de métricas open-source com:
- **Modelo pull**: Prometheus busca métricas periodicamente (não push)
- **Multi-dimensional**: Métricas com labels (ex: `cpu{host="web01", zone="us"}`)
- **PromQL**: Language de query poderosa
- **Alertmanager**: Gerencia alertas

### Pull vs Push

```
PULL (Prometheus):
Prometheus ────[request]────▶ App /metrics
                responds
                ────────────▶ {cpu: 75%, memory: 2GB}

PUSH (Datadog):
App ────[post metrics]────▶ Datadog
```

**Por que pull é melhor:**
- Prometheus controla frequência (não sobrecarrega app)
- Detecta automaticamente quando app morre (não recebe mais dados)
- Não precisa de queue (menos complexidade)

## Tipos de Métricas

| Tipo | Uso | Exemplo |
|------|-----|---------|
| **Counter** | Valor que só aumenta (requests, errors) | `http_requests_total{method="GET"}` |
| **Gauge** | Valor que pode subir/descer (cpu, memory) | `cpu_usage{host="web01"}` |
| **Histogram** | Distribuição de valores (latency, sizes) | `http_latency_seconds_bucket{le="0.1"}` |
| **Summary** | Similar ao histogram, com quantiles | `http_latency_seconds{quantile="0.99"}` |

## Expor /metrics — Node.js

```js
const express = require('express');
const promClient = require('prom-client');
const app = express();

const httpRequests = new promClient.Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['method', 'handler']
});

const httpLatency = new promClient.Histogram({
  name: 'http_latency_seconds',
  help: 'HTTP latency',
  labelNames: ['handler'],
  buckets: [0.1, 0.5, 1, 2, 5]
});

app.use((req, res, next) => {
  const start = Date.now();
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    httpRequests.inc({ method: req.method, handler: req.path });
    httpLatency.observe({ handler: req.path }, duration);
  });
  next();
});

app.get('/metrics', async (req, res) => {
  res.send(await promClient.register.metrics());
});
```

## Expor /metrics — Python (FastAPI)

```python
from fastapi import FastAPI
from prometheus_client import Counter, Gauge, generate_latest, REGISTRY

app = FastAPI()

http_requests = Counter('http_requests_total', 'Total HTTP requests', ['method'])
cpu_usage = Gauge('cpu_usage_percent', 'CPU usage')

@app.middleware('http')
async def count_requests(request, call_next):
    http_requests.labels(method=request.method).inc()
    response = await call_next(request)
    return response

@app.get('/metrics')
def metrics():
    return generate_latest(REGISTRY)
```

## PromQL — Queries Essenciais

```promql
# Valor atual
cpu_usage{host="web01"}

# Rate de requests por segundo (últimos 5min)
rate(http_requests_total[5m])

# Error rate (%)
sum(rate(http_requests_total{status="500"}[5m])) /
sum(rate(http_requests_total[5m])) * 100

# Latência p99
histogram_quantile(0.99, rate(http_latency_seconds_bucket[5m]))

# Top 5 hosts por CPU
topk(5, cpu_usage)

# Aggregation por label
sum(rate(http_requests_total[5m])) by (handler)

# Comparar com 1h atrás
cpu_usage offset 1h

# Increase last 1h
increase(http_requests_total[1h])
```

## Alertmanager

### Configuração

```yaml
# alertmanager.yml
global:
  slack_api_url: 'https://hooks.slack.com/services/xxx'

route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  receiver: 'slack-notifications'

receivers:
  - name: 'slack-notifications'
    slack_configs:
      - channel: '#alerts'
        api_url: 'https://hooks.slack.com/services/xxx'
```

### Regras de alerta

```yaml
# alerts.yml
groups:
  - name: infrastructure
    rules:
      - alert: HighCPU
        expr: cpu_usage > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "CPU alto em {{ $labels.host }}"

      - alert: HighErrorRate
        expr: >
          sum(rate(http_requests_total{status="500"}[5m])) /
          sum(rate(http_requests_total[5m])) * 100 > 5
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Error rate > 5%"
```

No `prometheus.yml`:
```yaml
rule_files:
  - "alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']
```

## Docker Compose Completo (Prometheus + Grafana)

```yaml
# docker-compose.yml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:v2.45.0
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - ./alerts.yml:/etc/prometheus/alerts.yml
      - prom_data:/prometheus
    ports:
      - 9090:9090
    networks:
      - monitor

  grafana:
    image: grafana/grafana:10.0.0
    container_name: grafana
    volumes:
      - grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin123
    ports:
      - 3000:3000
    depends_on:
      - prometheus
    networks:
      - monitor

  alertmanager:
    image: prom/alertmanager:v0.25.0
    container_name: alertmanager
    volumes:
      - ./alertmanager.yml:/etc/alertmanager/alertmanager.yml
    ports:
      - 9093:9093
    networks:
      - monitor

  node-exporter:
    image: prom/node-exporter:v1.6.0
    container_name: node-exporter
    ports:
      - 9100:9100
    networks:
      - monitor

networks:
  monitor:
    driver: bridge

volumes:
  prom_data:
  grafana_data:
```

```yaml
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
```

```bash
docker-compose up -d
# Prometheus:    http://localhost:9090
# Grafana:       http://localhost:3000 (admin/admin123)
# Alertmanager:  http://localhost:9093
```

## Grafana — Conectar ao Prometheus

1. Grafana UI → **Connections > Data Sources**
2. Add data source → **Prometheus**
3. URL: `http://prometheus:9090`
4. Save & Test

Criar dashboard:
1. Dashboards → New → Add visualization
2. Query: `sum(rate(http_requests_total[5m])) by (handler)`
3. Visualização: Time series
4. Título: "Requests por Handler"

## Stack Completa (Prometheus + Grafana + Loki + Tempo)

Para observabilidade completa open-source, use a **LGTM stack**:

| Componente | Papel |
|-----------|-------|
| **Loki** | Logs (indexa só labels, muito mais barato que ELK) |
| **Grafana** | Visualização unificada de tudo |
| **Tempo** | Traces distribuídos (OTel nativo) |
| **Mimir** | Métricas Prometheus em escala |

## Quando Usar Prometheus vs Outras

| Cenário | Melhor opção |
|---------|-------------|
| Startups, orçamento limitado | **Prometheus + Grafana** (open-source, zero custo) |
| Microsserviços, containers | **Prometheus** (modelo pull ideal) |
| Logs centralizados | ELK ou Loki |
| Multi-cloud, rápido setup | Datadog |
| Enterprise, compliance | Datadog ou Dynatrace |

## Links

- Prometheus Docs: https://prometheus.io/docs/
- Grafana Docs: https://grafana.com/docs/
- [[signoz-opentelemetry]] — alternativa open-source com OTel nativo
- [[observabilidade-comparacao-guia]] — comparação completa
