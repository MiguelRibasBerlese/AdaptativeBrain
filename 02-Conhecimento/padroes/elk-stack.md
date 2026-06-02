---
type: conhecimento
status: ativo
tags: [elk, elasticsearch, logstash, kibana, filebeat, logs, observabilidade]
created: 2026-06-02
updated: 2026-06-02
---

# ELK Stack — Elasticsearch + Logstash + Kibana

## Arquitetura

```
┌─────────────┐    ┌─────────────┐    ┌──────────────────┐    ┌─────────────┐
│    Beats    │───▶│  Logstash   │───▶│  Elasticsearch   │──▶│   Kibana    │
│ (Filebeat)  │    │ (ETL/Pipeline)│   │ (Index/Storage)  │    │ (Dashboard) │
└─────────────┘    └─────────────┘    └──────────────────┘    └─────────────┘
```

| Componente | Papel |
|-----------|-------|
| **Elasticsearch** | Banco de dados NoSQL distribuído baseado em JSON que indexa e busca logs em tempo real |
| **Logstash** | Ferramenta ETL que coleta e transforma dados de múltiplas fontes |
| **Kibana** | Interface de usuário para visualizar e criar dashboards |
| **Beats** | "Micro agentes" de coleta leve (Filebeat para logs, Metricbeat para métricas) |

## Como o Elasticsearch Indexa

Usa **Inverted Index** (índice invertido):
1. Cada log é convertido em JSON
2. Campos são tokenizados (divididos em termos)
3. Criado um mapa: `termo → lista de documentos`
4. Busca ocorre em milissegundos mesmo com bilhões de documentos

```json
// Exemplo de documento indexado
{
  "@timestamp": "2026-06-02T11:32:00Z",
  "level": "ERROR",
  "message": "Database connection failed",
  "service": "api-users",
  "host": "prod-server-01"
}
```

```
// Query de busca
GET /logs-*/_search
{
  "query": {
    "match": {
      "level": "ERROR"
    }
  }
}
```

## Configuração do Logstash

```ruby
# logstash.conf
input {
  file {
    path => "/var/log/nginx/access.log"
    start_position => "beginning"
  }
  syslog {
    port => 5514
  }
}

filter {
  grok {
    match => { "message" => "^([^ ]+) ([^ ]+) ([^ ]+) \[([^\]]+)\] (.*)$" }
  }
  mutate {
    add_field => { "environment" => "production" }
  }
  date {
    match => [ "[timestamp]", "dd/MMM/yyyy:HH:mm:ss Z" ]
    target => "@timestamp"
  }
}

output {
  elasticsearch {
    hosts => ["localhost:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
}
```

Pipeline: `INPUT → FILTER → OUTPUT`

## Beats Disponíveis

| Beat | Uso |
|------|-----|
| **Filebeat** | Coleta logs de arquivos (nginx, apache, application logs) |
| **Metricbeat** | Coleta métricas de sistema (CPU, memória, disk) |
| **Auditbeat** | Coleta dados de auditoria de segurança |
| **Heartbeat** | Monitora disponibilidade de serviços |

```yaml
# filebeat.yml
filebeat.inputs:
  - type: filestream
    enabled: true
    paths:
      - /var/log/nginx/*.log
      - /var/log/app/*.log

output.logstash:
  hosts: ["logstash:5044"]
```

## Docker Compose Completo

```yaml
# docker-compose.yml
version: '3.8'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.12.0
    container_name: elasticsearch
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.type=single-node
      - xpack.security.enabled=false
    ports:
      - 9200:9200
    volumes:
      - esdata:/usr/share/elasticsearch/data
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:8.12.0
    container_name: logstash
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    ports:
      - 5044:5044
    depends_on:
      - elasticsearch
    networks:
      - elk

  kibana:
    image: docker.elastic.co/kibana/kibana:8.12.0
    container_name: kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - 5601:5601
    depends_on:
      - elasticsearch
    networks:
      - elk

  filebeat:
    image: docker.elastic.co/beats/filebeat:8.12.0
    container_name: filebeat
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/log:/var/log:ro
    depends_on:
      - logstash
    networks:
      - elk

networks:
  elk:
    driver: bridge

volumes:
  esdata:
```

```bash
docker-compose up -d
# Elasticsearch: http://localhost:9200
# Kibana:        http://localhost:5601
```

## Configurando Kibana

1. Acesse `http://localhost:5601`
2. Crie um **Index Pattern** (ex: `logs-*`)
3. Use **Discover** para buscar logs
4. Crie **Visualizations**:
   - Bar chart: erros por hora
   - Pie chart: distribuição por nível (INFO/ERROR/WARN)
   - Data table: top 10 hosts com mais erros
5. Junte em **Dashboard**

## ILM — Index Lifecycle Management

```json
PUT _ilm/policy/logs-policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": { "max_size": "50GB", "max_age": "7d" }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": { "delete": {} }
      }
    }
  }
}
```

Lifecycle dos buckets de dados:
- **Hot**: Dados recentes (RAM + disk), busca rápida
- **Warm**: 7-30 dias, disk apenas
- **Cold**: 30+ dias, disk comprimido
- **Frozen**: Eliminado ou arquivado

## Casos de Uso Reais

| Empresa | Uso | Como |
|---------|-----|------|
| Uber | Logging centralizado | Trilhões de logs/dia no Elasticsearch |
| Netflix | Monitoramento de infraestrutura | Métricas + logs para detectar falhas |
| Reddit | Análise de logs de aplicação | Filebeat → Logstash → Elasticsearch |
| GitHub | Debug de microsserviços | Traces + logs combinados |

## Limitações e Problemas

| Problema | Impacto | Solução |
|----------|---------|---------|
| Consumo alto de RAM | Precisa 8GB+ para cluster pequeno | Usar índices com roteamento |
| Complexidade de manutenção | Upgrades difíceis, sharding manual | Usar Elastic Cloud (hospedado) |
| Custo de storage | Logs viram TBs rapidamente | Implementar ILM |
| Query performance | Buscas complexas lentas | Criar mappings otimizados |
| Sem tracing nativo | Precisa integrar com Jaeger | Usar Elastic Tracing (novo) |

## Quando Usar ELK

✅ Logs centralizados em grande volume  
✅ Full-text search (busca por palavras em logs)  
✅ Dados sensíveis (auto-hospedado, dados ficam na sua infra)  
✅ Compliance HIPAA/PCI-DSS com auditoria forense  
❌ Time pequeno sem expertise para manter cluster  
❌ Budget limitado (prefira Loki para logs simples)  

## Links

- Docs: https://www.elastic.co/guide/en/elk-stack.html
- [[prometheus-grafana]] — alternativa open-source para métricas
- [[observabilidade-comparacao-guia]] — quando escolher ELK vs outras ferramentas
