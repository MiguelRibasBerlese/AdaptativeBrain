---
type: conhecimento
status: ativo
tags: [observabilidade, monitoramento, logs, métricas, traces, sre]
created: 2026-06-02
updated: 2026-06-02
---

# Observabilidade — Fundamentos

## O que é Observabilidade

**Observabilidade** é a capacidade de entender o estado interno de um sistema apenas observando seus sinais externos (logs, métricas e traces). Na engenharia moderna, isso significa:

- Diagnosticar problemas **sem precisar adicionar novo código**
- Entender **o que aconteceu, quando e por quê**
- Identificar causas raiz em sistemas distribuídos complexos

## Monitoramento Tradicional vs Observabilidade

| Monitoramento Tradicional | Observabilidade |
|--------------------------|-----------------|
| Focado em métricas pré-definidas | Focado em explorar qualquer questão |
| "O sistema está fora do esperado?" | "Por que o sistema está fora do esperado?" |
| Alertas baseados em thresholds fixos | Detecção de anomalias baseada em IA |
| Visualização de dados conhecidos | Capacidade de responder perguntas não previstas |
| Reativo (alerta quando algo age) | Preditivo (antecipa problemas) |

## Os Três Pilares

| Pilar | O que é | Quando usar |
|-------|---------|-------------|
| **Logs** | Registros detalhados de eventos. Ex: `"ERROR: Database connection failed at 14:32"` | Diagnosticar **o que aconteceu** em um momento específico |
| **Métricas** | Medidas numéricas ao longo do tempo. Ex: `CPU=75%, latência_p99=230ms` | Monitorar **saúde do sistema** e identificar tendências |
| **Traces** | Rastreamento do caminho de uma requisição através de múltiplos serviços | Entender **fluxo completo** em sistemas distribuídos |

## Por que Empresas Investem

- **Netflix**: Processa 10+ bilhões de eventos/dia para detectar falhas antes de afetar usuários
- **Uber**: Usa observabilidade para manter latência <100ms em 100+ microsserviços
- **Google**: Implementa SRE com SLOs rigorosos

Essas empresas têm sistemas distribuídos com centenas de serviços — sem observabilidade, um simples problema de banco pode levar **horas** para ser diagnosticado.

## Incidentes Reais por Falta de Observabilidade

| Incidente | Causa | Tempo de Diagnóstico | Impacto |
|-----------|-------|---------------------|---------|
| AWS S3 outage (2017) | Comando de diagnóstico errado | 4 horas | US$ 150M+ em perdas |
| Uber plataforma crash (2021) | Falha em serviço de autenticação | 2+ horas | Milhões de usuários afetados |
| GitHub outage (2018) | Problema de banco de dados | 6 horas | Toda plataforma offline |

Sem traces distribuídos → não sabe **qual serviço** causou o problema.
Sem logs estruturados → não sabe **o que exatamente** falhou.

## SLIs, SLOs e SLAs

| Conceito | Descrição | Exemplo |
|----------|-----------|---------|
| **SLI** | Indicador de nível de serviço (métrica) | `latency_p99 < 200ms` |
| **SLO** | Objetivo do SLI (meta interna) | "99.9% das requests < 200ms" |
| **SLA** | Acordo contratual com penalidades | "99.9% uptime ou $10K de crédito" |

### Implementando SLOs no Prometheus

```yaml
# alert_rules.yml
groups:
  - name: slo_alerts
    rules:
      - alert: SLOBurnRateHigh
        expr: |
          sum(rate(http_requests_total{status=~"5.."}[1h])) /
          sum(rate(http_requests_total[1h])) > 0.001
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "SLO de disponibilidade em risco"

      - alert: SLALatencyHigh
        expr: |
          histogram_quantile(0.99,
            sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
          ) > 0.2
        for: 10m
        labels:
          severity: warning
```

### Calcular Error Budget

```promql
# SLO: 99.9% availability = 0.1% error rate permitido
# Error budget remaining
1 - (
  sum(rate(http_requests_total{status=~"5.."}[30d])) /
  sum(rate(http_requests_total[30d]))
) / 0.001
# Resultado: 0.8 = 80% do error budget restante
```

## Structured Logging (Logs Úteis)

❌ **Ruim:**
```js
console.log('User logged in');
```

✅ **Bom:**
```js
const logger = require('pino');
logger.info({
  event: 'user_login',
  userId: '123',
  timestamp: new Date().toISOString(),
  ip: '192.168.1.1'
});
```

**Padrão recomendado de log:**
```json
{
  "timestamp": "2026-06-02T11:32:00.123Z",
  "level": "ERROR",
  "service": "api-users",
  "environment": "production",
  "event": "database_connection_failed",
  "userId": "123",
  "error": {
    "message": "Connection timeout",
    "code": "ETIMEDOUT",
    "stack": "..."
  },
  "context": {
    "host": "prod-server-01",
    "region": "us-east-1",
    "requestId": "abc-123"
  }
}
```

**Vantagens do structured logging:**
- Query por campo: `error.code = "ETIMEDOUT"`
- Agregação automática: `count by event`
- Dashboards automáticos sem parsing manual

## Runbooks

Um runbook é um documento que define os passos para responder a um alerta específico. Para cada alerta crítico, deve conter:
1. O que o alerta significa
2. Passos de diagnóstico
3. Ações de mitigação
4. Escalação (se necessário)

## Postmortem de Incidente

Estrutura padrão usando dados de observabilidade:
1. **Timeline**: Reconstruir eventos com timestamps de logs/traces
2. **Causa raiz**: Identificar o span/log que iniciou a falha
3. **Impacto**: Quantificar usuários afetados via métricas
4. **Ações corretivas**: O que implementar para evitar recorrência
5. **Itens de ação**: Tarefas concretas com owners e prazos

## Links

- [[elk-stack]] — ELK Stack (Elasticsearch + Logstash + Kibana)
- [[prometheus-grafana]] — Stack open-source de métricas
- [[signoz-opentelemetry]] — OpenTelemetry + SigNoz
- [[observabilidade-comparacao-guia]] — Comparação geral e guia de decisão
