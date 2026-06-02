---
type: protocolo
status: active
tags: [protocolo, instruções, claude]
updated: 2026-05-20
---

# 📋 Protocolo Claude — Como Usar Este Vault

> Este arquivo define **quando e como** Claude deve ler e escrever neste vault.
> Leia este arquivo no início de qualquer conversa que envolva projetos ou trabalho contínuo.

---

## 🔄 Fluxo por Tipo de Conversa

### Início de Conversa Nova
1. Ler [[03-Contexto/perfil-usuario]] — quem é o usuário, preferências
2. Ler [[03-Contexto/preferencias]] — como trabalhar
3. Ler [[00-Dashboard/Home]] — projetos ativos e recentes
4. Se há projeto mencionado: ler `01-Projetos/[nome]/README` e `contexto`

### Durante a Conversa
- **Decisão importante tomada** → registrar em `01-Projetos/[nome]/contexto.md`
- **Output gerado** (código, análise, doc) → salvar em `04-Outputs/[projeto]/`
- **Solução de problema** → registrar em `02-Conhecimento/solucoes/`
- **Padrão identificado** → registrar em `02-Conhecimento/padroes/`

### Fim de Conversa com Trabalho Realizado
1. Atualizar `01-Projetos/[nome]/tarefas.md` com progresso
2. Criar log em `01-Projetos/[nome]/sessoes/YYYY-MM-DD.md`
3. Atualizar `00-Dashboard/Home.md` com data e status
4. Limpar `03-Contexto/sessao-ativa.md`

---

## 📁 Mapa do Vault

```
Adaptative Brain/
│
├── 00-Dashboard/          → Navegação central & status
│   ├── Home.md            → HUB PRINCIPAL (atualizar sempre)
│   └── Entrada-Rapida.md  → Inbox para captura rápida
│
├── 01-Projetos/           → Um diretório por projeto
│   ├── _INDEX.md          → Registro de todos os projetos
│   ├── _TEMPLATE/         → Template para novos projetos
│   └── [nome-projeto]/
│       ├── README.md      → Visão geral, objetivos, stack
│       ├── contexto.md    → Decisões, restrições, fatos-chave
│       ├── tarefas.md     → Backlog e progresso
│       ├── sessoes/       → Log por conversa
│       └── outputs/       → Artefatos do projeto
│
├── 02-Conhecimento/       → Base de conhecimento reutilizável
│   ├── _INDEX.md
│   ├── padroes/           → Padrões de código & arquitetura
│   ├── solucoes/          → Problemas resolvidos + como
│   └── referencias/       → Links e docs externos
│
├── 03-Contexto/           → Informações persistentes sobre o usuário
│   ├── perfil-usuario.md  → Quem é, papel, nível técnico
│   ├── preferencias.md    → Como quer que Claude trabalhe
│   └── sessao-ativa.md    → Estado da conversa atual
│
├── 04-Outputs/            → Todos os artefatos gerados
│   ├── _INDEX.md
│   ├── codigo/
│   ├── documentos/
│   ├── analises/
│   └── planos/
│
├── 05-Templates/          → Templates para criar notas
│   ├── template-projeto.md
│   ├── template-sessao.md
│   ├── template-output.md
│   └── template-conhecimento.md
│
├── 99-Arquivo/            → Projetos e outputs concluídos
│   └── README.md
│
└── PROTOCOLO-CLAUDE.md    → ESTE ARQUIVO
```

---

## 📝 Schema Padrão de Frontmatter

Todo arquivo deve ter este frontmatter:

```yaml
---
type: projeto | conhecimento | output | contexto | sessão | template
status: ativo | rascunho | concluído | arquivado
projeto: slug-do-projeto        # se aplicável
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

---

## 🔍 Como Buscar Informação

1. **Por projeto** → `01-Projetos/[nome]/`
2. **Por tipo de output** → `04-Outputs/[tipo]/`
3. **Por problema/solução** → `02-Conhecimento/solucoes/`
4. **Por padrão técnico** → `02-Conhecimento/padroes/`
5. **Por tag** → usar `search_query` com `#tag`
6. **Busca textual** → usar `search_simple` com termo-chave

---

## ⚠️ Regras

- **Nunca sobrescrever** outputs anteriores — sempre criar arquivo novo com data
- **Sempre atualizar** `updated:` no frontmatter ao editar
- **Linkar** novas notas a partir do `Home.md` ou `_INDEX.md` correspondente
- **Tags consistentes** — usar as tags definidas no Home.md
- **Nomes de arquivo** → `kebab-case`, sem espaços, sem acentos em nomes de arquivo
