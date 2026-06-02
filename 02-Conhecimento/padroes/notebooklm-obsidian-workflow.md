---
type: conhecimento
status: ativo
tags: [notebooklm, obsidian, workflow, ia, google-drive]
created: 2026-05-21
updated: 2026-05-21
---

# 🔬 NotebookLM + Obsidian — Fluxo Bidirecional

> NotebookLM não tem API pública. A integração funciona via Google Drive como ponte.

---

## Arquitetura do Fluxo

```
┌─────────────────────────────────────────────────────┐
│                  OBSIDIAN VAULT                     │
│  02-Conhecimento/ ──┐                               │
│  01-Projetos/    ───┼──► Google Drive (sync auto)   │
│  03-Contexto/    ───┘          │                    │
│                                ▼                    │
│                        NOTEBOOKLM                   │
│                    (lê fontes do Drive)              │
│                                │                    │
│                    Insights / Respostas              │
│                                │                    │
│  04-Outputs/notebooklm/ ◄──────┘                    │
│  (template-notebooklm.md)                           │
└─────────────────────────────────────────────────────┘
```

---

## PARTE 1 — Vault → NotebookLM (via Google Drive)

### 1.1 Configurar Remotely Save (uma vez)

1. **Settings → Community Plugins** → ativar **Remotely Save**
2. Abrir as configurações do plugin
3. Selecionar **Google Drive** como serviço
4. Clicar em **Auth** → fazer login com sua conta Google
5. Definir pasta destino no Drive (ex: `ObsidianVault`)
6. Clicar **Test** para verificar conexão
7. Fazer o primeiro sync: ícone na ribbon ou `Ctrl+P` → `Remotely Save: start sync`

### 1.2 Adicionar o Vault como Fonte no NotebookLM

1. Abrir [notebooklm.google.com](https://notebooklm.google.com)
2. Criar ou abrir um notebook
3. **Add source → Google Drive**
4. Navegar até a pasta `ObsidianVault/02-Conhecimento/`
5. Selecionar os arquivos/pastas relevantes
6. Repetir para `01-Projetos/` ou `03-Contexto/`

> **Dica:** Adicione por pasta temática, não o vault inteiro — NotebookLM tem limite de 50 fontes por notebook.

### 1.3 Sync Automático

O Remotely Save sincroniza nas seguintes situações:
- Manual: `Ctrl+P` → `Remotely Save: start sync`
- Ao fechar o Obsidian (configurável)
- A cada X minutos (configurável nas opções do plugin)

Após cada sync, recarregue as fontes no NotebookLM se houve mudanças relevantes.

---

## PARTE 2 — NotebookLM → Obsidian

### 2.1 Capturar Insights

Após uma sessão no NotebookLM:

1. No Obsidian: `Ctrl+P` → `Templates: Insert template` → `template-notebooklm`
2. Salvar em `04-Outputs/notebooklm/YYYY-MM-DD-[topico].md`
3. Preencher as seções: insights, Q&A, citações, próximos passos

### 2.2 Criar Links Bidirecionais

Dentro do output do NotebookLM, linkar para notas do vault:
```markdown
## Conexões com o Vault
- [[02-Conhecimento/padroes/ddd-domain-driven-design|DDD]] — relacionado ao insight X
- [[01-Projetos/radar-linka/README|RadarLinka]] — aplicar no projeto
```

---

## Notebooks Recomendados por Contexto

| Notebook NotebookLM | Fontes do Vault |
|---------------------|-----------------|
| **Engenharia Sênior** | `02-Conhecimento/padroes/` |
| **Segurança** | `02-Conhecimento/solucoes/` |
| **RadarLinka** | `01-Projetos/radar-linka/` + `03-Contexto/` |
| **LINKA Ecosystem** | `01-Projetos/linka-ecosystem/` |
| **Estudos UNAERP** | `03-Contexto/historico-academico.md` |

---

## Limitações do NotebookLM

| Limitação | Workaround |
|-----------|-----------|
| Sem API pública | Google Drive como ponte |
| 50 fontes por notebook | Criar múltiplos notebooks por tema |
| Fontes não atualizam auto | Re-adicionar arquivo após mudanças grandes |
| Sem webhook/export direto | Copiar manualmente com o template |

---

## Referências
- [[rag-obsidian-ia|RAG + Obsidian — abordagem alternativa com API local →]]
- `05-Templates/template-notebooklm.md`
- `04-Outputs/notebooklm/` — outputs de sessões
