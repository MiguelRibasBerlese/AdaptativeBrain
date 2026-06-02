---
type: conhecimento
status: active
tags: [padrão, prd, rfc, dod, documentação, especificação, alinhamento]
created: 2026-06-01
updated: 2026-06-01
---

# 📋 Documentação de Produto: PRD, RFC e Definition of Done

> Os documentos que separam times amadores de times profissionais.

---

## PRD — Product Requirements Document

PRD detalha o comportamento, propósito e funcionalidades do produto para **alinhar stakeholders antes do desenvolvimento**.

### Estrutura de um PRD Eficaz (Google/Meta)

```markdown
# PRD: [Nome da Feature]
**Autor:** | **Data:** | **Status:** Draft / Aprovado

## 1. Problema e Contexto
- Qual problema estamos resolvendo?
- Por que agora?
- Quem é afetado e com que frequência?

## 2. Objetivos e Métricas de Sucesso
- O que queremos alcançar?
- Como medimos sucesso? (ex: +20% retenção em 30 dias)

## 3. Requisitos Funcionais
- Lista de features com descrição clara
- Prioridade: P0 (crítico), P1 (importante), P2 (nice-to-have)

## 4. Requisitos Não-Funcionais
- Performance, segurança, escalabilidade, acessibilidade

## 5. Restrições e Dependências
- O que NÃO fazemos (out of scope)
- Dependências de outros times

## 6. Timeline Estimada
- Fases de entrega e marcos

## 7. Riscos e Mitigação
- Principais riscos técnicos e de negócio
```

**Regra prática:** o PRD define o que NÃO está no escopo com a mesma clareza do que está — isso é o principal antídoto contra scope creep.

---

## RFC — Request for Comments

RFC é um documento técnico para **propor mudanças significativas e coletar feedback antes da implementação**.

> Meta usa RFCs para **todo** projeto significativo de engenharia.

### Quando Usar RFC

- Mudanças de arquitetura significativas
- Adoção de nova biblioteca ou framework
- Mudanças em APIs públicas
- Decisões que afetam múltiplos times
- Qualquer coisa que demore mais de 1 sprint

### Estrutura de RFC (Meta/Google)

```markdown
# RFC-XXX: [Título]
Status: Draft | Em Revisão | Aprovado | Fechado
Deadline: YYYY-MM-DD

## 1. Objetivo (resumo em 1 tweet)
"O que se trata em uma frase"

## 2. Contexto e Motivação
- Qual problema está sendo resolvido?
- Por que agora é importante?
- Impacto atual no usuário/negócio

## 3. Proposta de Solução
- Como funcionará tecnicamente
- Diagramas de arquitetura (se aplicável)
- API contracts (OpenAPI/Swagger se aplicável)

## 4. Soluções Alternativas
- Opção A: [descrição] — REJEITADA porque...
- Opção B: [descrição] — REJEITADA porque...
- Não fazer nada: consequências...

## 5. Fora do Escopo
- O que NÃO será discutido
- O que NÃO será feito
(Tão importante quanto o escopo)

## 6. RACI
- R = Responsible (quem executa)
- A = Accountable (quem decide/assina)
- C = Consulted (quem é consultado)
- I = Informed (quem é informado)

## 7. Impactos e Riscos
- Performance, segurança, custo

## 8. Plano de Implementação
- Fase 1 / Fase 2 + Rollback plan

## 9. Revisão e Aprovação
- Revisores: Nome | Time | Status
- Aprovado por: Nome | Data
```

### Por que Times Maduros Usam RFC

1. **Visibilidade total** — qualquer pessoa pode ver e comentar
2. **Diálogo registrado** — histórico completo da decisão
3. **Decisões de maior qualidade** — mais perspectivas
4. **Conhecimento não tribal** — não fica na cabeça de 1 pessoa
5. **Proprietário toma decisão final** — comentários ajudam, não transferem responsabilidade

**RFC não é só para software.** Pode ser usado para: estrutura de times, Key Results, formato de daily, processo de atendimento a incidentes.

**Ferramentas:** GitHub Gist, Google Docs, Notion page, arquivo `.md` no repo.

---

## Especificações Técnicas que Devs Realmente Usam

Boas práticas para specs que não viram papel esquecido:

- **Exemplos de código inclusos** (não apenas texto)
- **Diagramas** (arquitetura, fluxos de dados, sequências)
- **API contracts** em OpenAPI/Swagger antes de implementar
- **Route diagrams** para sistemas distribuídos
- **Revisão por pares** antes de implementar
- **Curta e objetiva** — máximo 2 páginas para features menores

---

## Alinhamento com Stakeholders Não-Técnicos

Técnicas práticas antes de desenvolver:

1. **Protótipos visuais (Figma)** antes de codar — stakeholders clicam e validam fluxo
2. **User stories em linguagem simples** — "Como [persona], eu quero [ação] para [benefício]"
3. **Demo early e frequentemente** — não espere o produto "pronto"
4. **Documentação visual** — diagramas de fluxo, não UML denso
5. **Definição compartilhada de "pronto"** — todos sabem o critério de aceite

---

## Definition of Done (DoD)

DoD é uma lista de critérios que **todos os itens devem cumprir para serem considerados completos**.

### DoD Típico de Time Maduro

- [ ] Código escrito e revisado (code review com ≥ 1 aprovador)
- [ ] Testes unitários passando (cobertura > 80%)
- [ ] Testes de integração passando
- [ ] Documentação atualizada (README, swagger, comentários)
- [ ] Deploy em staging funcionando
- [ ] Aceitação do PM confirmada
- [ ] Monitoramento configurado (logs, alertas)
- [ ] Runbook atualizado (se sistema crítico)

### Como o DoD Evita Retrabalho

- **Todo mundo sabe o que significa "pronto" desde o início**
- Nada é considerado entregue sem todos os critérios
- Elimina o "estou quase terminando" que dura 3 sprints
- Força conversas difíceis no início, não no final

---

## ADRs — Architecture Decision Records

ADRs registram decisões arquiteturais importantes e **por que foram tomadas**.

```markdown
# ADR-001: Escolha de Banco de Dados

## Status
Aprovado (2026-01)

## Contexto
Precisamos de banco para dados de usuários com 1M+ registros.
Queries de relatório são complexas e lentas no MySQL atual.

## Decisão
Escolhemos PostgreSQL em vez de MongoDB.

## Consequências
+ Queries complexas mais fáceis com CTEs e window functions
+ Ecossistema maduro e suporte a JSONB
- Horizontal scaling mais difícil que MongoDB

## Alternativas Consideradas
- MongoDB: rejeitado — queries relacionais complexas são mais difíceis
- MySQL: rejeitado — já testado, limitações de performance para OLAP
```

**Por que times maduros documentam ADRs:**
- Novos integrantes entendem por que decisões foram tomadas
- Evita repetir erros do passado
- Contexto permanece quando pessoas saem

---

## Referências
- [[gestao-projetos-internos|Gestão de Projetos Internos →]]
- [[erros-projetos-software|Erros Comuns em Projetos →]]
- [[ferramentas-engenharia|Ferramentas e Sistemas →]]
- Template de PRD: monday.com/blog/pt/desenvolvimento/template-de-prd/
