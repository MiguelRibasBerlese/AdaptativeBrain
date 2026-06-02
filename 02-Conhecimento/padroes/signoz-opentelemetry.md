---
type: conhecimento
status: ativo
tags: [signoz, opentelemetry, otel, observabilidade, open-source, tracing]
created: 2026-06-02
updated: 2026-06-02
---

# SigNoz + OpenTelemetry

## O que é OpenTelemetry

OpenTelemetry (OTel) é um **padrão aberto de instrumentação** mantido pela CNCF (Cloud Native Computing Foundation). É a unificação de OpenTracing + OpenCensus.

**Por que está se tornando o padrão da indústria:**
- Vendor-neutral: instrumente uma vez, envie para qualquer backend (Datadog, New Relic, SigNoz, Jaeger)
- Suportado nativamente por AWS, GCP, Azure, Datadog, New Relic
- Sem lock-in: trocar de Datadog para SigNoz = trocar só o endpoint de exportação
- CNCF Graduated project (o mais alto nível de maturidade)

```
Sua Aplicação
    ↓ (OTel SDK)
OTel Collector  ──→  Datadog
                ──→  New Relic
                ──→  SigNoz
                ──→  Jaeger
                ──→  Prometheus
```

## O que é SigNoz

SigNoz é uma plataforma de observabilidade **open-source** construída sobre OpenTelemetry. É a alternativa self-hosted ao Datadog/New Relic.

**Por que está crescendo:**
- Open-source (Apache 2.0) — sem custo de licença
- Implementa OTel nativamente (não como add-on)
- Logs + Métricas + Traces em uma única UI (como Datadog)
- Backend: ClickHouse (muito mais eficiente que Elasticsearch para telemetria)
- GitHub: 19k+ stars, comunidade ativa

## Instrumentar com OpenTelemetry

### Node.js

```bash
npm install @opentelemetry/sdk-node \
  @opentelemetry/auto-instrumentations-node \
  @opentelemetry/exporter-otlp-http
```

```js
// tracing.js (carregado antes do app)
const { NodeSDK } = require('@opentelemetry/sdk-node');
const { getNodeAutoInstrumentations } = require('@opentelemetry/auto-instrumentations-node');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');
const { Resource } = require('@opentelemetry/resources');
const { SemanticResourceAttributes } = require('@opentelemetry/semantic-conventions');

const sdk = new NodeSDK({
  resource: new Resource({
    [SemanticResourceAttributes.SERVICE_NAME]: 'minha-api',
    [SemanticResourceAttributes.SERVICE_VERSION]: '1.0.0',
    environment: process.env.NODE_ENV,
  }),
  traceExporter: new OTLPTraceExporter({
    url: 'http://signoz:4318/v1/traces',  // SigNoz
    // url: 'https://ingest.signoz.io:443', // SigNoz Cloud
  }),
  instrumentations: [getNodeAutoInstrumentations()],
});

sdk.start();

// Auto-instrumenta: express, http, pg, mysql, redis, mongodb, grpc
```

```bash
# Iniciar com tracing
node -r ./tracing.js app.js
```

**Adicionar spans customizados:**
```js
const { trace, context } = require('@opentelemetry/api');
const tracer = trace.getTracer('minha-api');

async function processOrder(orderId) {
  const span = tracer.startSpan('process-order');
  span.setAttribute('order.id', orderId);
  
  try {
    const result = await db.query('SELECT * FROM orders WHERE id = $1', [orderId]);
    span.setAttribute('order.items_count', result.rows.length);
    return result;
  } catch (err) {
    span.recordException(err);
    span.setStatus({ code: 2, message: err.message });
    throw err;
  } finally {
    span.end();
  }
}
```

### Python (FastAPI)

```bash
pip install opentelemetry-sdk \
  opentelemetry-instrumentation-fastapi \
  opentelemetry-exporter-otlp
```

```python
# tracing.py
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.resources import Resource

resource = Resource(attributes={
    "service.name": "minha-api",
    "service.version": "1.0.0",
    "deployment.environment": "production"
})

provider = TracerProvider(resource=resource)
exporter = OTLPSpanExporter(endpoint="http://signoz:4318/v1/traces")
provider.add_span_processor(BatchSpanProcessor(exporter))
trace.set_tracer_provider(provider)
```

```python
# main.py
from fastapi import FastAPI
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
import tracing  # inicializa o SDK

app = FastAPI()
FastAPIInstrumentor.instrument_app(app)

@app.get("/users/{user_id}")
async def get_user(user_id: str):
    # Automaticamente cria span com:
    # - http.method, http.url, http.status_code
    # - Duração da request
    return {"id": user_id}
```

## OTel Collector

O Collector recebe telemetria e envia para múltiplos backends — útil para:
- Filtrar/transformar dados antes de enviar
- Enviar para múltiplos destinos
- Batching e retry automático

```yaml
# otel-collector-config.yaml
receivers:
  otlp:
    protocols:
      http:
        endpoint: 0.0.0.0:4318
      grpc:
        endpoint: 0.0.0.0:4317

processors:
  batch:
    timeout: 10s
  memory_limiter:
    limit_mib: 512

exporters:
  otlp/signoz:
    endpoint: "signoz:4317"
    tls:
      insecure: true
  # Enviar para múltiplos:
  # otlp/datadog:
  #   endpoint: "https://trace.agent.datadoghq.com"

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [memory_limiter, batch]
      exporters: [otlp/signoz]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/signoz]
    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp/signoz]
```

## Instalar SigNoz com Docker

```yaml
# docker-compose.yml
version: "3.8"

services:
  clickhouse:
    image: clickhouse/clickhouse-server:23.11.1-alpine
    volumes:
      - clickhouse_data:/var/lib/clickhouse
    ulimits:
      nofile:
        soft: 262144
        hard: 262144
    networks:
      - signoz

  signoz:
    image: signoz/signoz:0.36.0
    environment:
      - SIGNOZ_LOCAL_DB_PATH=/var/lib/signoz/signoz.db
      - STORAGE=clickhouse
      - CLICKHOUSE_DSN=tcp://clickhouse:9000
    ports:
      - "3301:3301"   # UI
      - "4317:4317"   # OTLP gRPC
      - "4318:4318"   # OTLP HTTP
    depends_on:
      - clickhouse
    networks:
      - signoz

  otel-collector:
    image: signoz/signoz-otel-collector:0.88.9
    command: ["--config=/etc/otel-collector-config.yaml"]
    volumes:
      - ./otel-collector-config.yaml:/etc/otel-collector-config.yaml
    ports:
      - "4319:4318"  # porta externa do collector
    depends_on:
      - signoz
    networks:
      - signoz

networks:
  signoz:
    driver: bridge

volumes:
  clickhouse_data:
```

```bash
docker-compose up -d
# UI: http://localhost:3301
# OTLP HTTP: http://localhost:4318
# OTLP gRPC: localhost:4317
```

### Kubernetes (Helm)

```bash
helm repo add signoz https://charts.signoz.io
helm repo update
helm install signoz signoz/signoz \
  --namespace signoz \
  --create-namespace \
  --set frontend.service.type=LoadBalancer
```

## SigNoz vs Datadog/New Relic — Custo Real

**Exemplo: 10 serviços, 5GB traces/dia, 3 devs usando dashboard**

| Ferramenta | Custo/mês |
|-----------|-----------|
| **SigNoz self-hosted** | ~$50-100 (só infra) |
| **SigNoz Cloud** | ~$200 |
| **Datadog** | ~$800-1.500 |
| **New Relic** | ~$1.700 (3 full users) |

**Economia real**: SigNoz custa 10-20x menos que Datadog/New Relic em escala.

## Limitações do SigNoz vs Ferramentas Pagas

| Limitação | Impacto |
|-----------|---------|
| **UI menos polida** | Dashboards menos intuitivos que Datadog |
| **Sem AI/anomaly detection** | Sem equivalente ao Davis AI ou Watchdog |
| **Menos integrações nativas** | Precisa de OTel SDK (mas é padrão, não problema real) |
| **Suporte enterprise** | Comunidade/Discord vs suporte 24/7 com SLA |
| **RUM (browser monitoring)** | Suporte básico, menos recursos que Datadog |
| **Manutenção** | ClickHouse precisa de gestão (storage, backups) |

**Quando SigNoz faz sentido:**
- Startup com budget limitado
- Time com expertise DevOps para operar ClickHouse
- Compliance que exige dados on-premise
- Já usando OpenTelemetry (zero mudança de instrumentação)

## Links

- SigNoz: https://signoz.io/docs/
- OpenTelemetry: https://opentelemetry.io/docs/
- [[prometheus-grafana]] — stack open-source para métricas
- [[observabilidade-comparacao-guia]] — comparação completa com guia de decisão
