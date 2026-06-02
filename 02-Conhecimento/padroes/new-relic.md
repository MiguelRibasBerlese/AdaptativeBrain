---
type: conhecimento
status: ativo
tags: [new-relic, apm, nrql, observabilidade, saas]
created: 2026-06-02
updated: 2026-06-02
---

# New Relic

## O que é e Evolução

New Relic foi fundada em 2008 como uma das primeiras ferramentas de APM (Application Performance Monitoring) SaaS. Em 2021 passou por uma **reformulação completa**:

- **Antes**: Produto por módulo (APM, Infrastructure, Browser — cada um pago separado)
- **Depois (New Relic One)**: Plataforma unificada com preço baseado em **data ingest + usuários**

Hoje compete diretamente com Datadog e Dynatrace no espaço de observabilidade full-stack.

## New Relic APM na Prática

O APM captura automaticamente:
- Tempo de cada transaction (HTTP request, background job)
- Queries de banco de dados (SQL, MongoDB, Redis)
- Chamadas externas (HTTP, gRPC)
- Erros e stack traces completos
- Distributed traces entre serviços

### Instalação — Node.js

```bash
npm install newrelic
```

```js
// DEVE ser o primeiro require do app
require('newrelic');

const express = require('express');
const app = express();

// O agente captura automaticamente:
// - Cada request HTTP
// - Queries ao banco
// - Calls externos
// - Erros não tratados
```

```ini
# newrelic.js (config)
exports.config = {
  app_name: ['minha-api'],
  license_key: process.env.NEW_RELIC_LICENSE_KEY,
  logging: {
    level: 'info'
  },
  allow_all_headers: true,
  attributes: {
    exclude: ['request.headers.cookie', 'request.headers.authorization']
  }
};
```

### Instalação — Python

```bash
pip install newrelic
```

```bash
# Iniciar app com o agente
NEW_RELIC_LICENSE_KEY=xxx \
NEW_RELIC_APP_NAME="minha-api" \
newrelic-admin run-program python app.py
```

```python
# Ou no código (Django/FastAPI/Flask)
import newrelic.agent
newrelic.agent.initialize('newrelic.ini')

@newrelic.agent.background_task()
def process_payment(order_id):
    # Transaction instrumentada
    pass
```

### Instalação — Java

```xml
<!-- pom.xml -->
<dependency>
  <groupId>com.newrelic.agent.java</groupId>
  <artifactId>newrelic-api</artifactId>
  <version>8.9.0</version>
</dependency>
```

```bash
# JVM flags
java -javaagent:/opt/newrelic/newrelic.jar \
     -Dnewrelic.config.license_key=xxx \
     -Dnewrelic.config.app_name="minha-api" \
     -jar app.jar
```

## NRQL — New Relic Query Language

NRQL é similar a SQL mas para dados de telemetria. Estrutura:

```sql
SELECT [função] FROM [fonte] WHERE [filtro] SINCE [tempo] FACET [agrupamento]
```

### Queries Essenciais

```sql
-- Taxa de erro da última hora
SELECT percentage(count(*), WHERE error IS TRUE) as 'Error Rate'
FROM Transaction
WHERE appName = 'minha-api'
SINCE 1 hour ago

-- Latência p50/p95/p99
SELECT percentile(duration, 50, 95, 99) as 'Latency (ms)'
FROM Transaction
WHERE appName = 'minha-api'
SINCE 30 minutes ago
TIMESERIES AUTO

-- Top 10 endpoints mais lentos
SELECT average(duration) as 'Avg Duration'
FROM Transaction
WHERE appName = 'minha-api'
FACET name
SINCE 1 day ago
LIMIT 10

-- Erros agrupados por tipo
SELECT count(*) as 'Error Count'
FROM TransactionError
WHERE appName = 'minha-api'
FACET error.class
SINCE 24 hours ago

-- Throughput por endpoint
SELECT rate(count(*), 1 minute) as 'RPM'
FROM Transaction
WHERE appName = 'minha-api'
FACET name
TIMESERIES 5 minutes
SINCE 1 hour ago

-- Apdex score (satisfação do usuário)
SELECT apdex(duration, t: 0.5) as 'Apdex'
FROM Transaction
WHERE appName = 'minha-api'
SINCE 1 hour ago

-- Queries SQL lentas
SELECT max(databaseDuration) as 'Max DB Time', count(*) as 'Count'
FROM Transaction
WHERE appName = 'minha-api'
FACET databaseCallCount
SINCE 1 hour ago
```

### Fontes de Dados NRQL

| Fonte | Conteúdo |
|-------|---------|
| `Transaction` | Cada request HTTP ou job em background |
| `TransactionError` | Erros dentro de transactions |
| `Span` | Spans de distributed tracing |
| `Metric` | Métricas numéricas (infrastructure, custom) |
| `Log` | Logs do New Relic Logs |
| `SyntheticRequest` | Resultados de testes sintéticos |
| `PageView` | Eventos de browser (RUM) |

## Dashboards e Alertas

### Criar Dashboard
```
New Relic UI → Dashboards → Create a dashboard
→ Add widget → NRQL query
→ Escolher visualização: Line chart, Billboard, Table, etc.
```

Dashboard recomendado para Node.js API:
1. **Error rate** (últimas 24h) — `percentage(count(*), WHERE error IS TRUE)`
2. **Latência p99** — `percentile(duration, 99) TIMESERIES`
3. **Throughput** — `rate(count(*), 1 minute) TIMESERIES`
4. **Top endpoints por erro** — `count(*) WHERE error IS TRUE FACET name`

### Criar Alerta (Alert Policy)
```
Alerts → Alert policies → Create a new policy
→ Add condition → NRQL
→ Query: SELECT count(*) FROM TransactionError WHERE appName = 'minha-api'
→ Threshold: > 50 erros em 5 minutos
→ Notification: Slack, PagerDuty, email
```

## Modelo de Preços (2026)

New Relic mudou para preço baseado em **data ingest + usuários** — mais previsível que Datadog.

| Item | Preço |
|------|-------|
| **Data ingest** | $0.30/GB (primeiros 100GB/mês grátis) |
| **Full platform user** | $549/usuário/mês (acesso completo) |
| **Core user** | $49/usuário/mês (acesso limitado) |
| **Free tier** | 100GB/mês + 1 usuário full |

**Exemplo real:**
- 10 servidores enviando 50GB/mês de dados
- 3 usuários full platform
- Custo: (50GB - 100GB free = $0) + (3 × $549) = **~$1.647/mês**

**Vantagem**: preço não explode com número de hosts (diferente de Datadog).
**Desvantagem**: usuários full platform são caros; times grandes pagam muito.

## New Relic vs Datadog — Prós e Contras Reais

| Aspecto | New Relic | Datadog |
|---------|-----------|---------|
| **Preço grande escala** | Melhor (por GB, não por host) | Mais caro em muitos hosts |
| **Integrações** | 500+ | 780+ (maior ecossistema) |
| **NRQL vs DQL** | NRQL mais SQL-like, fácil | DQL mais poderosa para correlações |
| **Setup inicial** | Simples, agente único | Simples, mas mais integrações manuais |
| **APM qualidade** | Excelente, origem da empresa | Excelente, mais recente |
| **Logs** | Bom | Excelente (mais recursos) |
| **AI/ML** | Básico | Mais avançado (Watchdog) |
| **Suporte free tier** | Generoso (100GB/mês) | Limitado (14 dias retenção) |

**Escolha New Relic quando:**
- Time com muitos usuários que precisam de acesso (preço não escala por usuário como Datadog)
- Prefere preço previsível baseado em dados
- Já usa New Relic (lock-in do histórico)

**Escolha Datadog quando:**
- Mais integrações nativas necessárias
- Precisa de dashboards mais flexíveis
- Multi-cloud com AWS/GCP/Azure (integrações nativas mais ricas)

## Links

- Docs: https://docs.newrelic.com/
- [[datadog]] — principal concorrente
- [[dynatrace]] — alternativa enterprise com AI mais avançada
- [[observabilidade-comparacao-guia]] — comparação completa
