---
type: conhecimento
status: ativo
tags: [dynatrace, apm, full-stack, ai, observabilidade, enterprise]
created: 2026-06-02
updated: 2026-06-02
---

# Dynatrace

## O que é e o que Diferencia

Dynatrace é uma plataforma de observabilidade **full-stack com AI nativa** (Davis AI). A diferença central em relação a Datadog e New Relic:

| Aspecto | Dynatrace | Datadog |
|---------|-----------|---------|
| **Instalação** | OneAgent = 1 arquivo, autodiscovery total | Agente + integrações manuais |
| **AI** | Davis AI detecta causa raiz automaticamente | Anomaly detection básica |
| **Topologia** | Smartscape mapeia relações automaticamente | Precisa configurar service maps |
| **APM** | Instrumentação automática sem código | Requer SDK + configuração |
| **Preço** | Por host (full-stack incluso) | Por host + módulos separados |
| **Público** | Enterprise com infra complexa | Startups a enterprise |

## OneAgent — Instalação Automática

O OneAgent é um **único agente** que:
1. Detecta automaticamente todos os processos (JVM, Node.js, Python, .NET, Go)
2. Injeta instrumentação sem mudar código
3. Coleta métricas de infra + traces de aplicação + logs
4. Mapeia dependências (Smartscape)

```bash
# Instalação Linux (uma linha)
wget -O Dynatrace-OneAgent.sh \
  "https://YOUR_ENV.live.dynatrace.com/api/v1/deployment/installer/agent/unix/default/latest?Api-Token=YOUR_TOKEN"
sudo /bin/sh Dynatrace-OneAgent.sh

# Verificação
systemctl status oneagent
```

Após instalar, **em 5 minutos** o Dynatrace mostra:
- Todos os processos rodando
- Conexões entre serviços
- Métricas de CPU/memória/disco
- Traces de cada request

## Davis AI — Detecção de Anomalias

Davis é o motor de AI do Dynatrace. Diferente de sistemas baseados em thresholds:

**Como thresholds tradicionais funcionam:**
```
SE cpu > 80% → ALERTA
```
Problema: gera muitos falsos positivos (CPU pode ficar em 85% normalmente).

**Como Davis funciona:**
1. Aprende o **baseline dinâmico** de cada métrica (hora do dia, dia da semana)
2. Detecta desvios estatísticos fora do padrão histórico
3. Correlaciona múltiplos sinais para identificar **causa raiz**
4. Prioriza problemas por impacto em usuário real

```
Davis detecta:
- Aumento de latência em /api/checkout
- Correlaciona com: deploy 10min atrás + aumento de CPU no pod payments
- Causa raiz: "Deploy v2.3.1 introduziu query lenta no banco"
- Impacto: 3.2% dos usuários afetados
```

**Resultado:** ao invés de 50 alertas, você recebe 1 "Problema" com causa raiz já identificada.

## Full-Stack Monitoring

Dynatrace cobre toda a stack com um único produto:

```
Usuário Final (RUM)
     ↓
CDN / Load Balancer
     ↓
Frontend (JavaScript)
     ↓
API Gateway
     ↓
Microserviços (APM)
     ↓
Banco de Dados (DB monitoring)
     ↓
Infraestrutura (hosts, containers)
     ↓
Kubernetes (pod health, HPA)
```

**Real User Monitoring (RUM):**
```html
<!-- Script automático injetado pelo OneAgent -->
<!-- Captura: page load time, JS errors, user actions, Core Web Vitals -->
```

**Kubernetes monitoring automático:**
- Pod health, OOMKilled, CrashLoopBackOff
- HPA scaling events correlacionados com performance
- Namespace e workload overview sem configuração

## Configurar Alertas Inteligentes

### Anomaly Detection Automática (recomendado)
Davis gera alertas sem configuração. Você só define:
1. **Alerting profiles**: filtrar quais problemas notificar
2. **Notification integrations**: Slack, PagerDuty, email, webhook

```
Settings → Alerting → Alerting profiles
→ Novo perfil: "Produção Crítico"
  → Severity: Availability + Performance
  → Notification: PagerDuty (on-call)
```

### Alertas Manuais (Metric Events)
```
Settings → Anomaly Detection → Custom metric events
→ Metric: builtin:host.cpu.usage
→ Condition: ABOVE 90% por 5 minutos
→ Alerta: "CPU crítica em {host}"
```

### Auto-adaptive baselines
```
Settings → Anomaly Detection → Services
→ Response time: Auto-adaptive (detecta desvio do baseline)
→ Failure rate: Static (> 5% → alerta)
```

## Dynatrace vs Datadog — Quando Usar Cada

| Situação | Melhor Escolha | Por quê |
|----------|---------------|---------|
| Infra complexa, muitos microserviços | **Dynatrace** | Smartscape + Davis reduz ruído de alertas |
| Time pequeno, setup rápido | **Datadog** | Ecossistema de integrações maior |
| Compliance enterprise (bancos, saúde) | **Dynatrace** | Mais maduro em full-stack enterprise |
| Startups, custo otimizado | **Datadog** | Preço mais previsível em escala pequena |
| APM sem mudança de código | **Dynatrace** | OneAgent faz instrumentação automática |
| Multi-cloud com muitas integrações | **Datadog** | 780+ integrações nativas |
| Foco em segurança + observabilidade | **Dynatrace** | Runtime Application Security integrado |

## Modelo de Preços (2026)

| Produto | Preço | Inclui |
|---------|-------|--------|
| Full-Stack Monitoring | ~$69/host/mês | Infra + APM + Logs + RUM |
| Infrastructure Monitoring | ~$21/host/mês | Só métricas de infra |
| Digital Experience | ~$11/10k sessions | RUM e session replay |
| Log Management | ~$0.20/GB ingerido | — |

**Comparação com Datadog:**
- Dynatrace Full-Stack: $69/host (tudo incluso)
- Datadog equivalente: ~$23 host + $31 APM + logs = ~$60+/host

## Limitações

- **Curva de aprendizado**: Interface complexa, muitas opções
- **Customização limitada**: Davis é caixa-preta, difícil entender decisões
- **Lock-in alto**: Formato proprietário, migrar é custoso
- **Kubernetes overhead**: OneAgent consome mais recursos que agentes menores

## Links

- Docs: https://www.dynatrace.com/support/help/
- [[datadog]] — alternativa mais popular para startups
- [[new-relic]] — alternativa com preço baseado em dados
- [[observabilidade-comparacao-guia]] — comparação completa
