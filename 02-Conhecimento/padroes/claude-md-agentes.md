---
type: conhecimento
status: active
tags: [padrão, claude, agentes, ia, claude-md, contexto, prompt-engineering]
created: 2026-05-20
---

# 🤖 CLAUDE.md — Diretrizes para Agentes de IA em Repositórios

> O arquivo CLAUDE.md na raiz do repositório é um contrato comportamental lido automaticamente pelo Claude Code no início de cada sessão.

---

## O Que é CLAUDE.md

Arquivo de instruções sistêmicas que define como o agente deve se comportar ao trabalhar em um repositório específico. Equivalente a um onboarding instantâneo para o agente a cada conversa.

**Localização:**
- `CLAUDE.md` na raiz → protocolo global do projeto
- `src/CLAUDE.md` → regras específicas para o diretório `src/`
- `api/CLAUDE.md` → regras específicas para a API

O agente carrega apenas os arquivos do contexto onde está atuando — **modularidade geográfica**.

---

## O Que INCLUIR

### 1. Informações não óbvias do ambiente
```markdown
## Ambiente de Desenvolvimento
- Node.js 22 (gerenciado por nvm — use `nvm use` antes de rodar)
- PostgreSQL 16 local na porta 5433 (não 5432)
- Redis obrigatório para testes de integração: `docker compose up redis`
```

### 2. Comandos essenciais com flags específicas
```markdown
## Comandos
- Build: `npm run build -- --mode staging`
- Testes: `pytest -x --cov=src --cov-fail-under=80`
- Lint: `npm run lint:fix` (não apenas `lint`)
- DB migrate: `alembic upgrade head` (NUNCA `downgrade` em produção)
```

### 3. Padrões de nomenclatura
```markdown
## Nomenclatura
- Branches: `feat/`, `fix/`, `chore/`, `refactor/` + kebab-case
- Commits: Conventional Commits (feat, fix, docs, chore, refactor)
- Arquivos Python: snake_case; TypeScript: camelCase para vars, PascalCase para classes
```

### 4. Decisões arquiteturais históricas
```markdown
## Decisões de Arquitetura
- Usamos Saga Orquestrada (Temporal.io) — ver ADR-007
- Evitar transações entre serviços — cada serviço tem seu banco
- Outbox Pattern obrigatório para publicação de eventos
```

### 5. Ponteiros para arquivos, não conteúdo inline
```markdown
## Guias de Estilo
- Python: ver `.python-style.md`
- TypeScript: ver `docs/ts-conventions.md`
- Infraestrutura: ver `infra/README.md`
```

### 6. Metodologia de trabalho obrigatória
```markdown
## Fluxo de Trabalho para o Agente
1. Explorar arquivos relevantes ANTES de planejar
2. Apresentar plano explícito ANTES de gerar código
3. Escrever testes ANTES da implementação (TDD)
4. Validar alterações com `npm test` antes de marcar como concluído
```

---

## O Que NÃO INCLUIR

| Evitar | Por quê |
|--------|---------|
| Tutoriais básicos ("como fazer um loop em Python") | Óbvio para o modelo, desperdiça contexto |
| "Escreva código limpo / legível" | Genérico demais, sem impacto real |
| Dados altamente mutáveis (URLs de staging, IPs) | Ficam desatualizados rapidamente |
| Código completo inline | Use ponteiros para arquivos |
| Todo o histórico de PRs e decisões | Use ADRs linkados |

---

## CLAUDE.md vs Arquivos de Memória

| Armazenamento | Escopo | Persiste entre |
|--------------|--------|---------------|
| `CLAUDE.md` | Projeto/repositório | Sessões (sempre lido) |
| `~/.claude/memory/` | Global do usuário | Todos os projetos |
| Variáveis de sessão | Conversa atual | Apenas a conversa |

---

## Anti-Pattern: Arquivo Monolítico

```
# ❌ CLAUDE.md de 2000 linhas
# - Degrada precisão da IA
# - Consome janela de contexto inteira
# - Difícil de manter

# ✅ Estrutura modular
CLAUDE.md          → protocolo global (< 100 linhas)
src/CLAUDE.md      → regras de frontend
api/CLAUDE.md      → regras da API
infra/CLAUDE.md    → regras de infraestrutura
```

---

## Template Prático CLAUDE.md

```markdown
# [Nome do Projeto] — Protocolo Claude

## Stack
[uma tabela simples]

## Comandos Essenciais
- `[comando de build]`
- `[comando de testes]`
- `[comando de lint]`

## Ambiente
[quirks do ambiente local — o que não é óbvio]

## Arquitetura
[3-5 decisões arquiteturais chave com link para ADR]

## Fluxo de Trabalho
1. Explorar → Planejar → Codificar → Testar → Verificar

## O Que Evitar
[lista de anti-patterns específicos deste projeto]

## Referências
- [link para docs] 
- [link para ADRs]
```

---

## Referências
- Claude Code Docs (code.claude.com/best-practices)
- [[rag-obsidian-ia|RAG + Obsidian — estruturação de notas para LLMs →]]
