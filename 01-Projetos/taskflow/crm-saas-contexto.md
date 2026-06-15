---
type: projeto
status: em-desenvolvimento
projeto: crm-saas
tags:
  - projeto
  - nestjs
  - nextjs
  - typescript
  - prisma
  - postgresql
  - saas
  - crm
  - monorepo
  - turborepo
  - vercel
  - railway
created: 2026-05-29
updated: 2026-06-15
github: https://github.com/MiguelRibasBerlese/CRM-SaaS
---

# TaskFlow — CRM SaaS para Agências Digitais

> **Sua equipe entrega. Seu cliente acompanha. Você cresce.**
> Plataforma de gestão de projetos e CRM construída pela NexMint Labs para agências digitais.
> Internamente chamado de **MintBoard** (package.json: `"name": "mintboard"`).

---

## Status Atual (2026-06-15)

- **Backend (NestJS):** ✅ 18 módulos completos
- **Frontend (Next.js 14):** ✅ 15+ páginas
- **Deploy Web (Vercel):** ✅ Estável
- **Deploy API (Railway):** ✅ Configurado via `railway.toml`
- **Banco (PostgreSQL):** ✅ Prisma + migrations aplicadas
- **Total de commits:** 118

---

## Arquitetura

Monorepo Turborepo com dois apps e um pacote compartilhado:

```
taskflow/                 ← diretório local: C:\Users\migue\taskflow
├── apps/
│   ├── api/              # NestJS — REST API (porta 3001)
│   └── web/              # Next.js 14 App Router (porta 3000)
├── packages/
│   └── database/         # Prisma + PostgreSQL
├── docker-compose.yml    # PostgreSQL 16 + Redis (dev local)
├── turbo.json
├── vercel.json           # framework: nextjs, outputDirectory: apps/web/.next
└── railway.toml          # deploy da API
```

---

## Stack Completa

| Camada | Tecnologia |
|--------|-----------|
| Frontend | Next.js 14 App Router, React 18, TypeScript |
| Estilo | Tailwind CSS, tailwindcss-animate, Radix UI, Lucide React, Framer Motion |
| Estado/Data | TanStack Query v5, React Context |
| Forms | React Hook Form + Zod |
| DnD | DnD Kit (core + sortable + utilities) |
| Backend | NestJS, TypeScript |
| ORM | Prisma 5 |
| Banco | PostgreSQL 16 |
| Auth | JWT (access 15min + refresh 7d), bcrypt, refresh token rotation, Redis blacklist (JTI) |
| IA | Groq — llama-3.3-70b-versatile (quota mensal por plano) |
| Email | Resend — `noreply@nexmintlab.com` (domínio verificado) |
| Storage | Cloudflare R2 + signed URLs (via `R2_SIGNED_URLS=true`) |
| Pagamentos | Stripe + Pagar.me (ambos funcionais com webhook + dedupe Redis) |
| Monitoramento | Sentry (frontend + backend) |
| Monorepo | Turborepo 2 + npm workspaces |
| CI/CD | GitHub Actions |
| Infra | Docker Compose (dev), Vercel (web), Railway (api) |

---

## Backend — 18 Módulos NestJS

| Módulo | Descrição |
|--------|-----------|
| `auth` | POST /auth/register, /login, /refresh, /logout; refresh rotation + Redis blacklist |
| `users` | GET /users/me, CRUD membros, convite por e-mail, reenvio de convite, upload de avatar (R2) |
| `organizations` | GET/PATCH /organizations/me, API key que autentica como admin |
| `projects` | CRUD + filtros ?status= ?limit= |
| `tasks` | CRUD + filtros + view semanal/calendário + subtarefas (ChecklistItem) |
| `dashboard` | GET /dashboard/stats (5 métricas via Promise.all) |
| `finance` | Overview + CRUD parcelas (FinancialInstallment) + CRUD lançamentos (FinancialEntry) + export CSV |
| `portal` | GET /portal/:slug — rota pública; PATCH /portal/:slug/creatives/:id/review |
| `reports` | GET /reports/pdf (placeholder) |
| `ai` | POST /ai/summary, /risks, /suggest-tasks → Groq llama-3.3-70b |
| `audit` | Trilha de auditoria por tenant (AuditLog) |
| `notifications` | Push + in-app + página /notifications |
| `subscriptions` | Stripe + Pagar.me; checkout, webhooks, cancel, billing page; dedupe Redis; 13 testes |
| `pipeline` | Pipeline de vendas: PipelineStage, Lead, LeadActivity, FollowUp; Kanban de leads |
| `creatives` | Gestão de criativos por projeto; review via portal |
| `devspace` | DevResource — repositórios, links e recursos do projeto |
| `vault` | VaultEntry com criptografia AES-GCM — credenciais e segredos por tenant |
| `meta-ads` | AdAccount, AdSpend, AdMetric, MetaConnection — dashboard de Meta Ads por cliente |

**Segurança multi-tenant:**
- Todo endpoint (exceto `/portal/:slug` e `/auth/*`) tem `JwtAuthGuard`
- API key de org (`org_...`) também autentica via `JwtAuthGuard` (age como admin)
- `assertBelongsToTenant(id, tenantId)` antes de mutações
- `ValidationPipe` global com `whitelist: true` + `forbidNonWhitelisted: true`
- Rate limiting: 100 req/min geral, 10 req/min em `/auth/refresh`
- Swagger em `/api/docs` (desabilitado em produção)

---

## Schema Prisma — Modelos

Organization, User, Project, Task, TaskComment, **ChecklistItem**, Attachment, ProjectMessage, FinancialInstallment, **FinancialEntry**, **TimeEntry**, AuditLog, AiSummary, Notification, **Creative**, **DevResource**, **VaultEntry**, **KanbanColumn**, Subscription, **AdAccount**, **AdSpend**, **AdMetric**, **MetaConnection**, **PipelineStage**, **Lead**, **LeadActivity**, **FollowUp**

**Enums:**
- `ProjectStatus`: PLANNING → ACTIVE → PAUSED → COMPLETED → CANCELLED
- `TaskStatus`: BACKLOG → TODO → IN_PROGRESS → IN_REVIEW → DONE
- `Priority`: P0, P1, P2, P3
- `Role`: ADMIN, MANAGER, MEMBER, CLIENT
- `DeliverableStatus`: PENDING → AWAITING_APPROVAL → APPROVED → REJECTED
- `InstallmentStatus`: PENDING → PAID → OVERDUE → CANCELLED

`@@index([tenant_id])` em todas as tabelas.

---

## Frontend — Páginas

| Rota | Descrição |
|------|-----------|
| `/` | Landing page Aurora (GSAP + Lenis, scrollytelling) |
| `/login` | Formulário com validação Zod |
| `/register` | Registro com nome da empresa |
| `/welcome` | Onboarding pós-registro |
| `/dashboard` | Métricas + grid de projetos + tarefas atrasadas |
| `/projects` | Grid com busca + NewProjectModal |
| `/projects/[id]` | Kanban board 5 colunas + DnD Kit + task-detail-sheet |
| `/tasks` | Lista com filtro colapsável (status, prioridade, membro) + calendário mensal |
| `/team` | Membros com avatar, role, status e botão reenviar convite |
| `/finance` | Overview + tabela de parcelas + lançamentos + export CSV |
| `/pipeline` | Kanban de leads (PipelineStage) com drag-and-drop |
| `/meta-ads` | Dashboard de Meta Ads por cliente (AdAccount, gráfico diário, CTR, CPL) |
| `/notifications` | Central de notificações in-app |
| `/settings` | Editar nome, logo, cor e API key da organização |
| `/billing` | Planos (Starter R$197 / Professional R$497 / Business R$997) + checkout Stripe real |
| `/dev-tools` | Docs de integração para devs (API REST, MCP, webhooks, WordPress/n8n) |
| `/portal/[slug]` | Página pública para o cliente (sem auth) |

**Paleta:** teal `#1ABC9C` (primária) · preto `#0A0A0A`/`#050505` (dark mode backgrounds)

**Task Detail Sheet:** abre ao clicar no card Kanban; contém subtarefas (add/toggle/rename/delete) e comentários (CRUD). Ícone de lápis abre modal de edição.

**Kanban responsivo mobile:** tabs por coluna em telas < 768px.

**Interceptor de refresh token (`lib/api.ts`):**
- 401 → enfileira requisições em `pendingQueue` enquanto refresh corre
- Após refresh: `drainQueue` resolve tudo com novo token
- Falha no refresh: `rejectQueue` + limpa localStorage + redireciona `/login`

---

## MCP Server

Pacote `mintboard-mcp` publicado no npm (v0.1.0, stdio). 14 tools: projetos, tarefas, pipeline, financeiro, resumo IA e ponte Meta Ads. Documentado em `/dev-tools`.

---

## Integrações Externas (estado real em 2026-06-15)

| Integração | Status | Notas |
|-----------|--------|-------|
| Stripe | ✅ Funcional | Webhooks, dedupe Redis, 13 testes |
| Pagar.me | ✅ Funcional | HMAC obrigatória em produção |
| Groq | ✅ Funcional | llama-3.3-70b-versatile |
| Resend | ✅ Funcional | Domínio nexmintlab.com verificado |
| Cloudflare R2 | ✅ Funcional | Signed URLs disponíveis |
| Socket.io | ✅ Funcional | Backoff exponencial na reconexão |
| Sentry | ✅ Funcional | Frontend (Next.js) + Backend (NestJS) |
| Meta Ads | ✅ Funcional | App próprio, Marketing API direta |
| Anthropic Claude API | ❌ Removida | Migrado para Groq em 2026-06-10 |
| Google Ads | ❌ Não implementado | Roadmap |
| Google Calendar | ❌ Não implementado | Roadmap |
| Slack | ❌ Não implementado | Roadmap |

---

## Deploy

### Vercel (Web)
```json
{ "framework": "nextjs", "outputDirectory": "apps/web/.next", "buildCommand": "npm run build", "installCommand": "npm install" }
```

### Railway (API)
- Build: `npm install && prisma generate && prisma migrate deploy && cd apps/api && npm run build`
- Start: `node apps/api/dist/apps/api/src/main.js`
- Health check: `GET /health`

### Variáveis de ambiente obrigatórias
```
DATABASE_URL, DIRECT_URL            → PostgreSQL
JWT_SECRET, JWT_REFRESH_SECRET      → Auth
NEXT_PUBLIC_API_URL                 → Frontend → API
GROQ_API_KEY                        → Módulo AI (não mais ANTHROPIC_API_KEY)
STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET, STRIPE_PRICE_*
PAGARME_SECRET_KEY, PAGARME_WEBHOOK_SECRET, PAGARME_PLAN_*
RESEND_API_KEY, EMAIL_FROM          → noreply@nexmintlab.com
R2_ACCOUNT_ID, R2_ACCESS_KEY_ID, R2_SECRET_ACCESS_KEY, R2_BUCKET
REDIS_URL                           → blacklist + dedupe webhooks
SENTRY_DSN (api), NEXT_PUBLIC_SENTRY_DSN (web)
META_APP_ID, META_APP_SECRET        → Meta Ads
```

---

## O Que Falta Implementar

### Média prioridade
- [ ] Relatório PDF (`@react-pdf/renderer` ou Puppeteer)
- [ ] Socket.io — Kanban em tempo real (módulo existe, falta integrar no board)
- [ ] Link do portal copiável na view do projeto
- [ ] OAuth Google (NextAuth v5)
- [ ] Pagar.me checkout (fluxo frontend ainda incompleto)

### Qualidade / Infra
- [ ] Testes unitários — Jest nos services NestJS (fora de subscriptions)
- [ ] Testes E2E — Playwright
- [ ] PWA — manifest + service worker
- [ ] Dark mode consistente — next-themes (parcialmente feito via CSS vars)

### Roadmap
- [ ] Google Ads integração
- [ ] Slack — notificar canal em mudança de status
- [ ] Zapier/Make — webhooks de saída
- [ ] Google Calendar — sincronizar deadlines

---

## Decisões Técnicas Relevantes

- **IA migrada para Groq** — `ANTHROPIC_API_KEY` não é mais necessária; pode remover do Railway
- **`turbo.json` do database** — cache desabilitado para `prisma generate` sempre rodar no deploy
- **`packages/database/turbo.json`** — precisa ter `"extends": ["//"]` (Turbo 2 obrigatório)
- **`@types/cookie-parser`** — em `dependencies` (não devDependencies) por causa do build Vercel
- **Portal do cliente** — usa `axios` direto (sem o interceptor autenticado de `api.ts`)
- **API key de org** — Bearer `org_...` é aceita pelo `JwtAuthGuard` como admin da organização
- **VaultEntry** — criptografia AES-GCM no service (não apenas no banco)
- **Webhook dedupe** — `RedisService.acquireOnce` (SET NX, TTL 24h); fail-open sem Redis
- **Preços flat** — sem limite de usuários: Starter R$197 / Professional R$497 / Business R$997

---

_Atualizado por Claude Code em 2026-06-15. Baseado em leitura direta do repositório (118 commits)._
