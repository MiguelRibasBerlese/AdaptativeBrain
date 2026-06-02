---
type: conhecimento
status: active
tags: [padrão, rag, obsidian, ia, llm, embeddings, chunking, segundo-cérebro]
created: 2026-05-20
---

# 🤖 RAG + Obsidian — Otimização para Agentes de IA

> Como estruturar um vault do Obsidian para consumo eficiente por LLMs via RAG (Retrieval-Augmented Generation) ou contexto direto.

---

## O Problema do Chunking Ingênuo

A divisão de texto **puramente por contagem de tokens** destrói o contexto semântico:
- Particiona blocos de código ao meio
- Separa um parágrafo conceitual de seu cabeçalho
- O modelo recebe fragmentos sem saber "de onde" são

---

## Fragmentação Hierárquica (Recomendada)

### Divisão baseada em cabeçalhos Markdown

```python
from langchain.text_splitter import MarkdownHeaderTextSplitter

headers_to_split_on = [
    ("#",  "h1"),
    ("##", "h2"),
    ("###","h3"),
]

splitter = MarkdownHeaderTextSplitter(
    headers_to_split_on=headers_to_split_on
)
chunks = splitter.split_text(markdown_content)
```

**Resultado:** cada chunk preserva a hierarquia de cabeçalhos como metadados.

### Metadados de Hierarquia

Injetar o caminho de cabeçalhos no metadata de cada chunk:

```python
{
  "content": "O Saga Pattern resolve consistência distribuída...",
  "metadata": {
    "h1": "Saga + Event Sourcing",
    "h2": "Tipos de Ações",
    "source": "padroes/saga-event-sourcing-outbox.md"
  }
}
```

**Por que importa:** o modelo recebe o chunk e **sabe onde ele está** na estrutura do documento.

---

## Estratégia de Ingestão em VectorDB

```
1. Normalizar links (remover wikilinks [[]] → texto plano)
2. Remover conteúdo de navegação (navbars, índices repetitivos)
3. Dividir por cabeçalhos (preservar hierarquia como metadata)
4. Garantir chunks > 100 tokens e < 512 tokens
5. Gerar embeddings
6. Armazenar em VectorDB (Chroma, Pinecone, Qdrant, pgvector)
```

**Ferramenta:** `llm-docs-builder` (github: mensfeld/llm-docs-builder)
- Normaliza links, remove navegação, gera arquivos otimizados
- Redução de tokens: **67% a 95%**

---

## Estratégia Híbrida: Resumo + Original

Para RAG sofisticado com dois níveis de granularidade:

```
Consulta ampla → busca nos resumos de seção
Consulta detalhada → busca no texto original completo
```

```python
# Ao indexar:
for section in document.sections:
    summary = llm.summarize(section.content)
    store_with_both(
        content=section.content,
        summary=summary,
        metadata=section.metadata
    )

# Ao buscar:
broad_results   = search(query, field="summary")
precise_results = search(query, field="content")
```

---

## Estrutura Ideal de Nota para LLMs

### 1. Frontmatter YAML rico
```yaml
---
type: conhecimento
status: active
tags: [padrão, saga, microsserviços]
projeto: radar-linka
created: 2026-05-20
---
```
Permite filtragem por tipo/status/projeto antes de vetorizar.

### 2. Nota Atômica (Zettelkasten)
- **Uma ideia principal por nota**
- Título como afirmação completa: `Saga Orquestrada tem melhor visibilidade que Coreografada`
- Links bidirecionais para contexto relacionado

### 3. Hierarquia clara
- H1: título da nota
- H2: seções principais
- H3: subseções
- Evitar H4+ (excessivo para maioria dos casos)

### 4. Blocos de código intactos
- Nunca dividir blocos de código entre chunks
- Incluir linguagem no fenced code block: ` ```python `
- Adicionar comentário explicativo dentro do bloco

---

## Maps of Content (MOC)

Notas índice que conectam notas atômicas relacionadas:

```markdown
# MOC — Arquitetura de Microsserviços

## Fundamentos
- [[ddd-domain-driven-design]]
- [[grasp-principles]]

## Consistência Distribuída
- [[saga-event-sourcing-outbox]]
- [[sistemas-distribuidos-ddia]]

## Infraestrutura
- [[gitops-deployment]]
- [[strangler-fig-adr]]
```

**Para LLMs:** o MOC é um "mapa de navegação" que o agente pode ler primeiro para decidir quais notas detalhar.

---

## Armadilha: Super-Automação

> Relato de 2026: uso excessivo de agentes para escrever e categorizar notas **sem curadoria humana** resultou em perda de familiaridade do autor com o próprio repositório → descarte de toda a base.

**Regra:** automação como **exoesqueleto cognitivo**, não substituto da curadoria.

- ✅ Agente organiza, humano valida
- ✅ Agente sugere links, humano aceita/rejeita
- ❌ Agente escreve notas completamente sem revisão humana
- ❌ Agente reclassifica todo o vault automaticamente

---

## Referências
- github.com/mensfeld/llm-docs-builder
- LangChain — MarkdownHeaderTextSplitter
- [[claude-md-agentes|CLAUDE.md — Comportamento de Agentes →]]
