---
type: conhecimento
status: ativo
tags: [splunk, siem, segurança, logs, observabilidade, enterprise]
created: 2026-06-02
updated: 2026-06-02
---

# Splunk

## O que é

Plataforma de análise de dados de máquina (logs, eventos, métricas) focada em **empresas grandes**. Domina o mercado de SIEM (Security Information and Event Management) e operações de TI corporativas.

**Por que é tão usado:**
- Processa qualquer formato de dado sem esquema fixo
- SPL (Search Processing Language) é extremamente poderosa
- Ecossistema massivo: 2.000+ apps no Splunkbase
- Suporte enterprise com SLAs e compliance

## Como o Splunk Indexa

O Splunk usa um **índice proprietário** diferente do Elasticsearch:

1. **Input**: Dados chegam de qualquer fonte (arquivos, syslog, HTTP, APIs)
2. **Parsing**: Splunk quebra o dado em eventos, detecta timestamps automaticamente
3. **Indexação**: Cria índice invertido + raw data comprimido
4. **Search**: Pesquisa combina índice (rápido) + raw data (completo)

```
Raw Data → Splunk Forwarder → Splunk Indexer → Search Head
```

| Componente | Papel |
|-----------|-------|
| **Forwarder** | Agente leve instalado nos servidores, coleta e envia dados |
| **Indexer** | Armazena e indexa os dados recebidos |
| **Search Head** | Interface de busca e dashboards (pode ser cluster) |
| **Deployment Server** | Gerencia configuração de múltiplos forwarders |

## SPL — Splunk Processing Language

SPL é uma **linguagem de pipeline** onde cada comando transforma o resultado anterior.

```spl
| Comando1 | Comando2 | Comando3
```

### Exemplos Práticos

```spl
-- Buscar todos os erros 500 da última hora
index=web_logs status=500 earliest=-1h

-- Contar erros por host
index=web_logs status=500
| stats count by host
| sort -count

-- Top 10 IPs com mais requests
index=web_logs
| top limit=10 clientip

-- Taxa de erro por minuto (gráfico)
index=web_logs
| timechart span=1m count(eval(status>=500)) as errors, count as total
| eval error_rate=round(errors/total*100, 2)

-- Detectar login bruteforce (5+ falhas em 10min)
index=auth_logs action=failure
| bucket _time span=10m
| stats count by _time, src_ip
| where count > 5

-- Latência média e p95 por endpoint
index=app_logs
| stats avg(response_time) as avg_ms, perc95(response_time) as p95_ms by endpoint
| sort -p95_ms

-- Correlacionar eventos de segurança
index=firewall_logs action=blocked
| join src_ip [search index=auth_logs action=failure]
| table _time, src_ip, user, blocked_port
```

### Comandos SPL Essenciais

| Comando | Uso |
|---------|-----|
| `stats count by field` | Agregação/contagem |
| `timechart span=5m count` | Série temporal |
| `top limit=10 field` | Top N valores |
| `rex field=msg "(?<user>\w+) logged"` | Extração com regex |
| `eval newfield=if(status>=500,"error","ok")` | Campo calculado |
| `transaction session_id` | Agrupar eventos em sessão |
| `join` | Correlacionar duas buscas |
| `lookup` | Enriquecer com dados externos |

## Splunk Enterprise vs Splunk Cloud

| Aspecto | Enterprise | Cloud |
|---------|-----------|-------|
| Hospedagem | Auto-hospedado (on-premise) | Splunk hospeda |
| Controle | Total (hardware, rede) | Limitado |
| Manutenção | Alta (atualizações, sharding) | Zero |
| Custo inicial | Alto (hardware + licença) | Menor CAPEX |
| Custo operacional | Equipe dedicada | Incluído |
| Compliance HIPAA/PCI | Você configura | Certificado pela Splunk |
| Latência | Depende da rede interna | Via internet |

**Quando escolher Enterprise:**
- Dados altamente sensíveis que não podem sair da rede
- Compliance exige controle total (governo, saúde, bancos)
- Volume absurdo onde Cloud fica proibitivo

## Configurar Inputs de Dados

### Syslog (UDP/TCP)
```conf
# inputs.conf
[udp://514]
connection_host = ip
sourcetype = syslog
index = network_logs

[tcp://5514]
connection_host = ip
sourcetype = syslog
```

### Arquivos de Log
```conf
# inputs.conf
[monitor:///var/log/nginx/access.log]
disabled = false
index = web_logs
sourcetype = access_combined

[monitor:///var/log/app/*.log]
disabled = false
index = app_logs
sourcetype = json_no_timestamp
```

### HTTP Event Collector (HEC)
```bash
# Enviar evento via API
curl -k https://splunk:8088/services/collector/event \
  -H "Authorization: Splunk YOUR_HEC_TOKEN" \
  -d '{"event": {"level": "ERROR", "message": "DB timeout", "service": "api"}}'
```

```js
// Node.js
const axios = require('axios');

async function sendToSplunk(event) {
  await axios.post('https://splunk:8088/services/collector/event', {
    event: {
      timestamp: new Date().toISOString(),
      ...event
    }
  }, {
    headers: { Authorization: `Splunk ${process.env.SPLUNK_HEC_TOKEN}` }
  });
}
```

## Casos de Uso Reais

| Empresa | Uso | Detalhes |
|---------|-----|----------|
| **Domino's** | Monitoramento de pedidos | Detecta falhas no fluxo de pedidos em tempo real |
| **Nasdaq** | Segurança e compliance | Monitoramento de transações e detecção de fraudes |
| **US Army** | SIEM/Segurança | Análise de ameaças em redes militares |
| **Coca-Cola** | Operações de TI | Correlação de logs de infraestrutura global |
| **Adobe** | APM + Segurança | Monitoramento de Creative Cloud e detecção de abuso |

## Casos de Uso por Área

### Segurança (SIEM)
- Detecção de intrusão correlacionando firewall + auth + DNS
- Threat hunting com queries ad-hoc
- Compliance reporting (PCI-DSS, HIPAA, SOX)
- User behavior analytics (UBA)

### Operações de TI
- Monitoramento de infraestrutura e aplicações
- Capacity planning com dados históricos
- Correlação de incidentes entre múltiplos sistemas
- SLA reporting automático

### Negócio (Business Analytics)
- Análise de conversão de e-commerce por logs de aplicação
- Detecção de fraude em tempo real
- Monitoramento de KPIs operacionais

## Limitações e Por que Migram

| Limitação | Impacto |
|-----------|---------|
| **Preço**: ~$150/GB/dia indexado | Empresas com alto volume pagam milhões/ano |
| **Curva de aprendizado SPL** | Meses para dominar para análises complexas |
| **Performance em dados não estruturados** | Parsing custom é caro computacionalmente |
| **Vendor lock-in** | Dados ficam no formato proprietário Splunk |
| **Alternativas mais baratas** | Elastic + OpenSearch + Datadog cortam 60-80% do custo |

**Por que migram:**
- Elastic Stack cobre 80% dos casos de uso a 20% do custo
- Datadog é mais fácil para times sem especialistas SPL
- OpenSearch (fork open-source do ES) para casos simples

## Links

- Docs: https://docs.splunk.com/
- [[elk-stack]] — alternativa open-source para logs
- [[datadog]] — alternativa SaaS com setup mais simples
- [[observabilidade-comparacao-guia]] — quando escolher Splunk
