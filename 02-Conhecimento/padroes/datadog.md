---
type: conhecimento
status: ativo
tags: [datadog, apm, monitoramento, saas, observabilidade]
created: 2026-06-02
updated: 2026-06-02
---

# Datadog

## O que é

Plataforma de observabilidade **SaaS unificada** que resolve:

- **Monitoramento unificado**: Infraestrutura + APM + Logs + Segurança em um só lugar
- **Setup rápido**: Agente instala em 5 minutos, dashboards pré-construídos
- **Integrações nativas**: 780+ integrações (AWS, Kubernetes, Docker, etc.)
- **Detecção de anomalias**: IA identifica problemas sem thresholds manuais

## Datadog vs ELK Stack

| Aspecto | ELK Stack | Datadog |
|---------|-----------|---------|
| Tipo | Open-source (auto-hospedado) | SaaS (hospedado) |
| Setup | Dias para configurar | 5 minutos |
| Manutenção | Alto (você gerencia cluster) | Zero (Datadog gerencia) |
| Tracing | Precisa integrar Jaeger | Nativo (APM incluído) |
| Custo | Baixo (só infra) | Alto ($5-15/host/mês) |
| Escalabilidade | Você escala | Datadog escala automaticamente |
| Privacidade | Dados na sua infra | Dados no Datadog |

## Instalação do Agente

O Datadog Agent é um serviço Linux/Windows que:
1. Coleta métricas do sistema (CPU, memória, disk)
2. Lê logs de arquivos
3. Captura traces de aplicações instrumentadas
4. Envia tudo para Datadog via HTTPS (port 443)

```bash
# Instalação no Linux
sudo -E DATADOG_API_KEY=YOUR_API_KEY \
  DATADOG_SITE="datadoghq.eu" \
  bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script.sh)"

# Verificação
systemctl status datadog-agent
datadog-agent status
```

## APM (Application Performance Monitoring)

### Node.js
```bash
npm install @datadog/datadog-api-client
```

```js
require('datadog').init({
  service: 'my-api',
  env: process.env.NODE_ENV,
  url: 'http://localhost:8126'
});
```

No Datadog UI:
1. Acesse **APM > Services**
2. Veja latência p99, taxa de erro, requisições/segundo
3. Clique em um serviço para ver trace detalhado
4. Identifique span mais lento (ex: query SQL)

### Python
```bash
pip install datadog-api-client
```

```python
from datadog import initialize, api
initialize(api_key='YOUR_API_KEY')
```

## Dashboards e Alertas

### Dashboard
```
1. Lists > Dashboards > New Dashboard
2. Adicione widgets:
   - Query: avg(avg:system.cpu.user{*}) by {host}
   - Visualização: Time series
   - Título: "CPU por Host"
```

### Monitor/Alerta
```
1. Alerts > Monitors > New Monitor
2. Tipo: Metric
3. Query: avg(last_5m):avg:system.disk.used_pct{host:prod-*} > 90
4. Mensagem: "Disk uso > 90% em {{host.name}}"
5. Notificação: Slack, Email, PagerDuty
```

### Matriz de alertas recomendada

| Métrica | Threshold | Duração | Ação |
|---------|-----------|---------|------|
| CPU usage | > 80% | 5 min | Alerta nível 2 |
| Disk usage | > 90% | 5 min | Alerta nível 1 + PagerDuty |
| Error rate | > 5% | 2 min | Alerta nível 1 |
| Latency p99 | > 500ms | 10 min | Alerta nível 2 |

## Integrações Disponíveis

| Categoria | Integrações |
|-----------|------------|
| Cloud | AWS, GCP, Azure (nativa) |
| Containers | Kubernetes, Docker, ECS, EKS |
| Banco de dados | PostgreSQL, MySQL, MongoDB, Redis |
| APM | Node.js, Python, Java, Go, Ruby |
| Logs | nginx, apache, syslog, cloudwatch |
| CI/CD | GitHub Actions, GitLab, Jenkins |

### Integração AWS automática
```
# No Datadog UI: Integration > AWS
# 1. Adicione IAM role com permissões
# 2. Datadog coleta automaticamente:
#    - EC2 métricas
#    - RDS métricas
#    - Lambda traces
#    - CloudWatch logs
```

## Configuração em Servidor Real

```bash
# 1. Criar API Key no Datadog UI (Organization Settings > API Keys)
# 2. Instalação
sudo DATADOG_API_KEY="xxx" DATADOG_SITE="datadoghq.eu" \
  bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script.sh)"

# 3. Configurar integração específica (ex: nginx)
sudo mkdir -p /etc/datadog-agent/confd
cat > /etc/datadog-agent/confd/nginx.conf <<EOF
instances:
  - urls: ['http://localhost:8080/status']
EOF

# 4. Reiniciar
sudo datadog-agent restart

# 5. Verificar em Datadog UI > Infrastructure
datadog-agent status
```

## Modelo de Preços (2026)

| Plano | Preço | Inclui |
|-------|-------|--------|
| Free | $0 | 5 hosts, 14 dias retenção |
| Pro | $15/host/mês | APM, Logs (1GB/dia), 30 dias |
| Enterprise | $23/host/mês | Segurança, SLA 99.9%, suporte 24/7 |

**Custos adicionais:**
- Logs: $2.50/milhão de eventos/mês (acima de 1GB/dia)
- Containers: $1/container/mês (acima do limite do host)

## Quando Vale a Pena

✅ Startups com 5-50 hosts: Setup rápido, sem manutenção  
✅ Times sem expertise ELK: Dashboards pré-construídos  
✅ Multi-cloud: Integração nativa AWS/GCP/Azure  
❌ 100+ hosts com budget limitado: ELK + Grafana mais barato  
❌ Dados sensíveis (healthcare): Auto-hospedado necessário  

## Links

- Docs: https://docs.datadoghq.com/
- [[dynatrace]] — alternativa enterprise com AI automática
- [[new-relic]] — alternativa com preço mais previsível
- [[observabilidade-comparacao-guia]] — comparação completa
