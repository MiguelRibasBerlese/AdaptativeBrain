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
updated: 2026-06-02
github: https://github.com/MiguelRibasBerlese/CRM-SaaS
---

# TaskFlow — CRM SaaS para Agências Digitais

> **Sua equipe entrega. Seu cliente acompanha. Você cresce.**
> Plataforma de gestão de projetos e CRM construída pela NexMint Labs para agências digitais.
> Internamente chamado de **MintBoard** (package.json: `"name": "mintboard"`).

---

## Status Atual (2026-05-29)

- **Backend (NestJS):** ✅ Implementado — 13 módulos completos
- **Frontend (Next.js 14):** ✅ Implementado — 10+ páginas, Kanban com DnD
- **Deploy Web (Vercel):** 🔧 Em estabilização — múltiplos fixes de build recentes
- **Deploy API (Railway):** ✅ Configurado via `railway.toml`
- **Banco (PostgreSQL):** ✅ Prisma + migrations aplicadas

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
| Auth | JWT (access 15min + refresh 7d), bcrypt |
| IA | Anthropic Claude Sonnet 4.6 |
| Email | Resend |
| Storage | Cloudflare R2 (planejado) |
| Pagamentos | Stripe + Pagar.me |
| Monorepo | Turborepo 2 + npm workspaces |
| CI/CD | GitHub Actions |
| Infra | Docker Compose (dev), Vercel (web), Railway (api) |

---

## Backend — 13 Módulos NestJS

| Módulo | Endpoints principais |
|--------|---------------------|
| `auth` | POST /auth/register, /login, /refresh |
| `users` | GET /users/me, /users, POST /users/invite |
| `organizations` | GET/PATCH /organizations/me |
| `projects` | CRUD + ?status= ?limit= |
| `tasks` | CRUD + ?project_id= ?status= ?overdue=true |
| `dashboard` | GET /dashboard/stats (5 métricas via Promise.all) |
| `finance` | GET /finance/overview + CRUD parcelas |
| `portal` | GET /portal/:slug — rota pública sem auth |
| `reports` | GET /reports/pdf (placeholder) |
| `ai` | POST /ai/summary, /risks, /suggest-tasks → Claude Sonnet 4.6 |
| `audit` | Trilha de auditoria por tenant |
| `notifications` | Push + in-app |
| `subscriptions` | GET + POST /subscriptions/checkout → Stripe + Pagar.me |

**Segurança multi-tenant:**
- Todo endpoint (exceto `/portal/:slug` e `/auth/*`) tem `JwtAuthGuard`
- `assertBelongsToTenant(id, tenantId)` antes de mutações
- `ValidationPipe` global com `whitelist: true` + `forbidNonWhitelisted: true`
- Rate limiting: 100 req/min via `@nestjs/throttler`
- Swagger em `/api/docs`

---

## Schema Prisma

**Modelos:** Organization, User, Project, Task, TaskComment, Attachment, ProjectMessage, FinancialInstallment, AiSummary, AuditLog, Notification, Subscription

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
| `/` | Landing page |
| `/login` | Formulário com validação Zod |
| `/register` | Registro com nome da empresa |
| `/dashboard` | Métricas + grid de projetos + tarefas atrasadas |
| `/projects` | Grid com busca + NewProjectModal |
| `/projects/[id]` | Kanban board 5 colunas + DnD Kit |
| `/tasks` | Lista com filtro por status, prioridade, membro |
| `/team` | Membros com avatar, role e status |
| `/finance` | Overview + tabela de parcelas |
| `/portal/[slug]` | Página pública para o cliente (sem auth) |

**Paleta:** teal `#1ABC9C` (primária) · navy `#1A2332`/`#131b27` (backgrounds)

**Kanban (DnD Kit):**
- Optimistic updates com `onMutate` + rollback automático em erro
- Drag overlay com `rotate-1 scale-105`
- `+Adicionar tarefa` por coluna com `initialStatus` correto

**Interceptor de refresh token (`lib/api.ts`):**
- 401 → enfileira requisições em `pendingQueue` enquanto refresh corre
- Após refresh: `drainQueue` resolve tudo com novo token
- Falha no refresh: `rejectQueue` + limpa localStorage + redireciona `/login`

---

## Deploy

### Vercel (Web)
- `vercel.json` na raiz: `{ "framework": "nextjs", "outputDirectory": "apps/web/.next" }`
- Build command: `npm run build`

### Railway (API)
- `railway.toml` configurado
- Start command: `apps/api/dist/apps/api/src/main.js`

### Variáveis de ambiente obrigatórias
```
DATABASE_URL, DIRECT_URL       → PostgreSQL
JWT_SECRET, JWT_REFRESH_SECRET → Auth
NEXT_PUBLIC_API_URL            → Frontend → API
ANTHROPIC_API_KEY              → Módulo AI
```

---

## Git — Commits Recentes (2026-05-29)

```
53c0ddc fix: add root vercel.json with outputDirectory for Next.js monorepo
74e57fa fix: add framer-motion to web deps, fix transpilePackages, add creatives migration
736aa4d fix: add extends key to packages/database/turbo.json (Turbo 2 required)
14f2c4f fix: disable Turbo cache for database build so prisma generate always runs fresh
c0209be fix: add explicit any annotations to fix TS7006 in Vercel build
ae64a5a fix: move @types/cookie-parser to dependencies for Vercel production build
644a04b fix: add prisma generate to database build for Vercel
2154ff5 design: redesign completo para MintBoard/Stitch — tokens de cor, sidebar adaptativa
cd06e9e feat: filtros de status, prioridade e membro em todas as abas de tarefas
967690b deploy: adiciona prisma migrate deploy ao build
```

**Padrão de trabalho recente:** estabilização do pipeline de deploy no Vercel + Railway.

---

## O Que Falta Implementar

### Alta prioridade
- [ ] **Stripe Webhook** — processar `checkout.session.completed`
- [ ] **Convite de membro** — modal em `/team` + e-mail via Resend + link de aceite
- [ ] **Página `/settings`** — editar nome, logo e cor da organização

### Média prioridade
- [ ] Upload de arquivos (Cloudflare R2)
- [ ] Comentários em tarefas
- [ ] Notificações in-app (badge + dropdown)
- [ ] Socket.io — Kanban em tempo real
- [ ] Relatório PDF (`@react-pdf/renderer` ou Puppeteer)
- [ ] Link do portal copiável na view do projeto

### Qualidade / Infra
- [ ] Testes unitários — Jest nos services NestJS
- [ ] Testes E2E — Playwright
- [ ] Pagar.me checkout
- [ ] Auditoria automática — interceptor NestJS → AuditLog
- [ ] Dark mode — next-themes
- [ ] PWA — manifest + service worker

### Roadmap / Integrações
- [ ] OAuth Google (NextAuth v5)
- [ ] Slack — notificar canal em mudança de status
- [ ] Zapier/Make — webhooks de saída
- [ ] Google Calendar — sincronizar deadlines

---

## Decisões Técnicas Relevantes

- **`turbo.json` do database** — cache desabilitado para `prisma generate` sempre rodar no deploy
- **`packages/database/turbo.json`** — precisa ter `"extends": ["//"]` (Turbo 2 obrigatório)
- **`@types/cookie-parser`** — movido para `dependencies` (não devDependencies) por causa do build Vercel
- **`vercel.json` na raiz** — necessário porque o monorepo não usa `rootDirectory` de projeto específico
- **Portal do cliente** — usa `axios` direto (sem o interceptor autenticado de `api.ts`)
- **IA** — módulo usa Claude Sonnet 4.6 diretamente via Anthropic SDK

---

_Salvo automaticamente por Claude Code em 2026-05-29._
