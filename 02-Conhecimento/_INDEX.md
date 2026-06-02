---
type: índice
status: active
tags: [índice, conhecimento]
updated: 2026-06-02
---

# 🧩 Base de Conhecimento

> Padrões, soluções e referências reutilizáveis entre projetos.

---

## 📐 Padrões — Stack de Projetos

| Arquivo | Tópico |
|---------|--------|
| [[padroes/react-vite-tailwind-ts]] | React 19 + Vite + TypeScript + Tailwind CSS |
| [[padroes/python-scraping-groq]] | Pipeline Python: Playwright + BeautifulSoup + Groq AI |
| [[padroes/flutter-firebase-provider]] | Flutter + Firebase + Provider + MVVM |

---

## 📦 Padrões — Gestão de Produto e Engenharia Profissional

| Arquivo | Tópico |
|---------|--------|
| [[padroes/gestao-projetos-internos]] | Como grandes empresas decidem o que construir — discovery, priorização, OKR |
| [[padroes/metodologias-ageis-pratica]] | Scrum, Kanban, Shape Up, Scrumban — o que realmente acontece |
| [[padroes/documentacao-prd-rfc-dod]] | PRD, RFC, Definition of Done, ADRs — documentação que times usam |
| [[padroes/erros-projetos-software]] | Chaos Report, scope creep, estimativas — por que projetos falham |
| [[padroes/validacao-pre-desenvolvimento]] | MVP, problem framing, build vs buy, entrevistas com usuários |
| [[padroes/estrutura-time-produto]] | Squads, Trio TPD Spotify, papéis, cultura de engenharia |
| [[padroes/ferramentas-engenharia]] | Linear, Jira, Git Flow, trunk-based, CI/CD, runbooks, dívida técnica |
| [[padroes/cultura-engenharia-forte]] | Psychological safety, postmortem, code review saudável, feedback |
| [[padroes/metricas-fluxo-kanban]] | Cycle Time, Lead Time, Throughput, WIP, Monte Carlo, Lei de Little |
| [[padroes/okr-kpi-priorizacao]] | OKRs vs KPIs, RICE, WSJF, Cost of Delay, frameworks de priorização |

---

## 🔭 Padrões — Observabilidade e Monitoramento

| Arquivo | Tópico |
|---------|--------|
| [[padroes/observabilidade-fundamentos]] | Três pilares (logs/métricas/traces), SLOs, structured logging, postmortem |
| [[padroes/elk-stack]] | ELK Stack: Elasticsearch + Logstash + Kibana + Beats — setup completo |
| [[padroes/datadog]] | Datadog: APM, agente, dashboards, alertas, preços |
| [[padroes/splunk]] | Splunk: SPL, SIEM, segurança, enterprise, casos de uso |
| [[padroes/dynatrace]] | Dynatrace: OneAgent, Davis AI, full-stack, comparação com Datadog |
| [[padroes/new-relic]] | New Relic: NRQL, APM, preços por dados, comparação com Datadog |
| [[padroes/prometheus-grafana]] | Prometheus + Grafana: métricas, PromQL, Alertmanager, Docker Compose |
| [[padroes/signoz-opentelemetry]] | SigNoz + OpenTelemetry: alternativa open-source, instrumentação OTel |
| [[padroes/redash]] | Redash: BI/SQL dashboards, múltiplas fontes, vs Metabase vs Grafana |
| [[padroes/observabilidade-comparacao-guia]] | Comparação geral + guia de decisão por contexto |

---

## 🏛️ Padrões — Engenharia de Software Avançada

| Arquivo | Tópico | Nível |
|---------|--------|-------|
| [[padroes/sistemas-distribuidos-ddia]] | LSM-Tree vs B-Tree, Replicação, Garantias de Leitura, Quorum | Sênior |
| [[padroes/ddd-domain-driven-design]] | Bounded Context, Aggregate, Event Storming, Context Map | Sênior |
| [[padroes/grasp-principles]] | 9 princípios GRASP — atribuição de responsabilidades em OO | Pleno/Sênior |
| [[padroes/saga-event-sourcing-outbox]] | Saga, Event Sourcing, Outbox Pattern, Transações Distribuídas | Sênior |
| [[padroes/strangler-fig-adr]] | Modernização de legado + Architecture Decision Records | Sênior |
| [[padroes/gitops-deployment]] | GitOps, ArgoCD, Canary, Blue-Green, Feature Flags, RTO/RPO | Sênior |

---

## 🤖 Padrões — IA & Agentes

| Arquivo | Tópico |
|---------|--------|
| [[padroes/rag-obsidian-ia]] | RAG, Chunking Hierárquico, Zettelkasten, MOC, otimização para LLMs |
| [[padroes/claude-md-agentes]] | CLAUDE.md — estrutura, o que incluir/excluir, anti-patterns |
| [[padroes/notebooklm-obsidian-workflow]] | NotebookLM + Obsidian — fluxo bidirecional via Google Drive |

---

## 🔐 Segurança — OWASP 2025

| Arquivo | Tópico |
|---------|--------|
| [[solucoes/owasp-top10-2025]] | Visão geral OWASP Top 10 2025 — todas as categorias |
| [[solucoes/owasp-supply-chain]] | A03: Supply Chain Failures — dependências, CI/CD, SBOM, Cosign |
| [[solucoes/owasp-exceptional-conditions]] | A10: Exceptional Conditions — Fail Secure, CWEs, checklist |

---

## 📚 Referências

| Arquivo | Descrição |
|---------|-----------| 
| [[referencias/livros-engenharia-senior]] | Livros essenciais — DDIA, Philosophy of SD, Staff Engineer's Path, Rust Atomics |

### Links Externos

| Recurso | Descrição |
|---------|-----------|
| [Groq API Docs](https://console.groq.com/docs) | Groq — llama-3.3-70b-versatile |
| [Vercel Docs](https://vercel.com/docs) | Deploy, serverless functions |
| [Vite Docs](https://vitejs.dev) | Build tool padrão frontend |
| [Flutter Docs](https://docs.flutter.dev) | Cross-platform mobile |
| [Tailwind CSS](https://tailwindcss.com/docs) | Estilo utilitário |
| [OWASP Top 10:2025](https://owasp.org/Top10) | Lista oficial atualizada |
| [Argo CD](https://argoproj.github.io/cd) | GitOps operator para Kubernetes |
| [Sigstore/Cosign](https://www.sigstore.dev) | Assinatura de artefatos |
| [OpenTelemetry Docs](https://opentelemetry.io/docs/) | Padrão de instrumentação |
| [SigNoz Docs](https://signoz.io/docs/) | Alternativa open-source ao Datadog |
