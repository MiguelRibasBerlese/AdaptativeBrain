---
type: conhecimento
status: ativo
tags: [redash, bi, dashboard, sql, dados, visualização]
created: 2026-06-02
updated: 2026-06-02
---

# Redash

## O que é e Qual Problema Resolve

Redash é uma ferramenta **open-source de BI e dashboards** focada em SQL. Resolve o problema de transformar dados de múltiplas fontes em visualizações e dashboards acessíveis para times não-técnicos.

**Problema que resolve:**
- Analista de produto quer ver métricas do banco de dados sem pedir pro dev
- Time de negócio quer dashboards atualizados automaticamente
- Dados espalhados em PostgreSQL + BigQuery + Sheets precisam estar em um lugar

## Conectar a Múltiplas Fontes

Redash suporta 35+ data sources nativamente:

| Categoria | Fontes |
|-----------|--------|
| **SQL** | PostgreSQL, MySQL, SQLite, SQL Server, Oracle |
| **Cloud** | BigQuery, Redshift, Snowflake, Athena |
| **NoSQL** | MongoDB, Elasticsearch, DynamoDB |
| **APIs** | REST APIs, GraphQL |
| **Outros** | Google Sheets, CSV upload, InfluxDB |

### Configurar PostgreSQL
```
Settings → Data Sources → New Data Source
→ Tipo: PostgreSQL
→ Host: seu-postgres.railway.app
→ Port: 5432
→ Database: prod_db
→ Username: readonly_user
→ Password: ****
→ Test Connection → Save
```

**Boas práticas de segurança:**
- Criar usuário **read-only** específico para Redash
- Nunca usar usuário admin do banco
- Limitar acesso às tabelas necessárias

```sql
-- Criar usuário read-only no PostgreSQL
CREATE USER redash_readonly WITH PASSWORD 'senha_segura';
GRANT CONNECT ON DATABASE prod_db TO redash_readonly;
GRANT USAGE ON SCHEMA public TO redash_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO redash_readonly;
ALTER DEFAULT PRIVILEGES IN SCHEMA public 
  GRANT SELECT ON TABLES TO redash_readonly;
```

## Criar Queries e Visualizações

### Query básica com parâmetros
```sql
-- Query com parâmetro de data (filtro dinâmico no dashboard)
SELECT
  DATE_TRUNC('day', created_at) as data,
  COUNT(*) as total_pedidos,
  SUM(valor) as receita,
  AVG(valor) as ticket_medio
FROM orders
WHERE
  created_at >= '{{ start_date }}'
  AND created_at <= '{{ end_date }}'
  AND status = '{{ status }}'
GROUP BY 1
ORDER BY 1
```

Os `{{ parâmetros }}` viram filtros interativos no dashboard.

### Exemplos de queries úteis

```sql
-- Crescimento de usuários por semana
SELECT
  DATE_TRUNC('week', created_at) as semana,
  COUNT(*) as novos_usuarios,
  SUM(COUNT(*)) OVER (ORDER BY DATE_TRUNC('week', created_at)) as total_acumulado
FROM users
WHERE created_at >= NOW() - INTERVAL '90 days'
GROUP BY 1
ORDER BY 1;

-- Funil de conversão
SELECT
  COUNT(DISTINCT CASE WHEN etapa = 'visita' THEN user_id END) as visitas,
  COUNT(DISTINCT CASE WHEN etapa = 'cadastro' THEN user_id END) as cadastros,
  COUNT(DISTINCT CASE WHEN etapa = 'primeiro_pedido' THEN user_id END) as compras,
  ROUND(
    COUNT(DISTINCT CASE WHEN etapa = 'cadastro' THEN user_id END)::numeric /
    NULLIF(COUNT(DISTINCT CASE WHEN etapa = 'visita' THEN user_id END), 0) * 100, 1
  ) as conv_visita_cadastro_pct
FROM eventos;

-- Churn mensal
SELECT
  DATE_TRUNC('month', cancelled_at) as mes,
  COUNT(*) as cancelamentos,
  COUNT(*) * 100.0 / LAG(COUNT(*)) OVER (ORDER BY DATE_TRUNC('month', cancelled_at)) as churn_rate
FROM subscriptions
WHERE cancelled_at IS NOT NULL
GROUP BY 1
ORDER BY 1;
```

### Visualizações Disponíveis
| Tipo | Quando usar |
|------|-------------|
| **Table** | Dados detalhados, rankings |
| **Line Chart** | Métricas ao longo do tempo |
| **Bar Chart** | Comparação entre categorias |
| **Counter** | KPI único (ex: "Total de usuários: 12.450") |
| **Pie/Donut** | Distribuição percentual |
| **Cohort** | Análise de retenção |
| **Map** | Dados geográficos |
| **Funnel** | Funil de conversão |

## Construir Dashboards

1. Crie múltiplas queries (cada uma = um widget)
2. Dashboards → Create → Add Widget → Selecionar query
3. Escolher visualização para o widget
4. Arrastar/redimensionar widgets
5. Adicionar parâmetros globais (filtros que afetam todos os widgets)
6. Configurar refresh automático (a cada 24h, 1h, etc.)

**Compartilhar:**
- Dashboard público (link sem login)
- Embed em Notion/Confluence via iframe
- Enviar email com snapshot automático (Schedule)

## Redash vs Metabase vs Grafana

| Aspecto | Redash | Metabase | Grafana |
|---------|--------|----------|---------|
| **Foco** | Analistas SQL | Times não-técnicos | DevOps/métricas |
| **SQL** | Primeiro cidadão | Abstraído (GUI) | Via datasources |
| **Curva de aprendizado** | Baixa (precisa saber SQL) | Muito baixa | Média |
| **Dados de negócio** | Excelente | Excelente | Não ideal |
| **Dados de infraestrutura** | Não ideal | Não ideal | Excelente |
| **Open-source** | Sim (SSPL) | Sim (AGPL) | Sim (AGPL) |
| **Self-hosted** | Sim | Sim | Sim |
| **Alertas** | Básico | Sim | Excelente |

**Use Redash quando:**
- Time de produto/negócio precisa de acesso aos dados via SQL
- Múltiplas fontes de dados SQL que precisam estar juntas
- Queries complexas que ferramentas GUI não suportam

**Use Metabase quando:**
- Stakeholders não-técnicos que não sabem SQL
- Dashboards de negócio com filtros simples
- Onboarding rápido sem treinamento

**Use Grafana quando:**
- Métricas de infraestrutura e aplicação (Prometheus, InfluxDB)
- Alertas baseados em métricas em tempo real
- Stack DevOps (não BI de negócio)

## Instalar Redash com Docker

```yaml
# docker-compose.yml
version: "2"
services:
  server:
    image: redash/redash:10.1.0.b50633
    command: server
    depends_on:
      - postgres
      - redis
    ports:
      - "5000:5000"
    environment:
      PYTHONUNBUFFERED: 0
      REDASH_LOG_LEVEL: "INFO"
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgresql://postgres:password@postgres/postgres"
      REDASH_COOKIE_SECRET: "troque-por-segredo-aleatorio"
      REDASH_SECRET_KEY: "troque-por-segredo-aleatorio"
    restart: always

  scheduler:
    image: redash/redash:10.1.0.b50633
    command: scheduler
    depends_on:
      - server
    environment:
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgresql://postgres:password@postgres/postgres"
    restart: always

  worker:
    image: redash/redash:10.1.0.b50633
    command: worker
    depends_on:
      - server
    environment:
      REDASH_REDIS_URL: "redis://redis:6379/0"
      REDASH_DATABASE_URL: "postgresql://postgres:password@postgres/postgres"
    restart: always

  redis:
    image: redis:7-alpine
    restart: always

  postgres:
    image: postgres:14-alpine
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: always

volumes:
  postgres_data:
```

```bash
# Primeiro setup (criar tabelas)
docker-compose run --rm server create_db

# Iniciar
docker-compose up -d

# Acesso: http://localhost:5000
# Criar conta admin na primeira visita
```

## Casos de Uso Reais

| Caso | Query | Frequência |
|------|-------|-----------|
| Dashboard de receita diária | SUM(valor) GROUP BY dia | Refresh diário |
| Relatório de churn mensal | Cancelamentos / total assinantes | Mensal, enviado por email |
| Análise de produto | Funil de conversão por plano | Semanal |
| Suporte: buscar pedido por cliente | SELECT * FROM orders WHERE customer_id = '{{id}}' | Ad-hoc |
| Monitoramento de fraude | Pedidos com valor > $1000 nas últimas 24h | Refresh a cada hora |

## Links

- Docs: https://redash.io/help/
- [[prometheus-grafana]] — para métricas de infra (não BI)
- [[observabilidade-comparacao-guia]] — quando usar Redash vs outras ferramentas
