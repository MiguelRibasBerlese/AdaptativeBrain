# Adaptative Brain — Vault de Miguel Berlese

Segundo cérebro persistente. Este vault armazena projetos, conhecimento técnico, outputs e contexto do usuário entre conversas.

---

## Ferramentas MCP Disponíveis

Acesso ao vault via servidor Obsidian MCP (`mcp__obsidian__*`):

| Ferramenta | Uso |
|-----------|-----|
| `mcp__obsidian__vault_read` | Ler arquivo por caminho relativo |
| `mcp__obsidian__vault_write` | Criar ou sobrescrever arquivo |
| `mcp__obsidian__vault_append` | Adicionar conteúdo ao fim de arquivo |
| `mcp__obsidian__vault_patch` | Editar seção específica (por heading) |
| `mcp__obsidian__vault_list` | Listar diretório |
| `mcp__obsidian__search_simple` | Busca textual no vault |
| `mcp__obsidian__search_query` | Busca por tag ou frontmatter |

---

## Startup Obrigatório

Ao iniciar qualquer conversa, ler nesta ordem:

```
1. 03-Contexto/perfil-usuario.md     → quem é Miguel, stack, projetos ativos
2. 03-Contexto/preferencias.md       → como trabalhar com ele
3. 00-Dashboard/Home.md              → estado atual do vault
```

Se a conversa envolve um projeto específico, ler também:
```
4. 01-Projetos/[slug]/README.md      → visão geral e stack
5. 01-Projetos/[slug]/contexto.md    → decisões e restrições
```

---

## Quando Escrever o Quê

| Situação | Onde registrar |
|----------|---------------|
| Decisão arquitetural tomada | `01-Projetos/[slug]/contexto.md` |
| Output gerado (código, doc, análise) | `04-Outputs/[tipo]/YYYY-MM-DD-[slug]-[desc].md` |
| Problema resolvido | `02-Conhecimento/solucoes/YYYY-MM-DD-[desc].md` |
| Padrão técnico identificado | `02-Conhecimento/padroes/[nome-padrao].md` |
| Progresso em tarefas | `01-Projetos/[slug]/tarefas.md` |
| Log de sessão (ao finalizar) | `01-Projetos/[slug]/sessoes/YYYY-MM-DD.md` |

Após qualquer escrita: atualizar o `_INDEX.md` da pasta correspondente e o campo `updated:` do frontmatter.

Nunca sobrescrever outputs anteriores — sempre criar arquivo novo com data no nome.

---

## Frontmatter Obrigatório

Todo arquivo novo deve ter:

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

Nomes de arquivo: `kebab-case`, sem espaços, sem acentos.

---

## Preferências do Usuário

- **Idioma:** Português (BR)
- **Respostas:** concisas e diretas — sem floreios, sem sumários no fim
- **Código:** perguntar antes de implementar algo grande
- **Stack preferida:** React 19 + TypeScript + Vite + Tailwind; Groq API (não OpenAI); deploy Vercel

---

## Referências

- Protocolo detalhado e mapa completo do vault: `PROTOCOLO-CLAUDE.md`
- Índice de projetos: `01-Projetos/_INDEX.md`
- Índice de conhecimento: `02-Conhecimento/_INDEX.md`
- Templates para novos arquivos: `05-Templates/`
