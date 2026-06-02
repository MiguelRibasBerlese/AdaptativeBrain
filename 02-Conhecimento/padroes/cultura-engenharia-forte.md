---
type: conhecimento
status: active
tags: [padrão, cultura, psychological-safety, postmortem, feedback, code-review, engenharia]
created: 2026-06-01
updated: 2026-06-01
---

# 🧠 Cultura e Mentalidade de Empresas com Tecnologia Forte

> O que diferencia empresas que têm tecnologia como vantagem competitiva das que têm como problema crônico.

---

## O Que Diferencia os Dois Mundos

| Tecnologia como Vantagem | Tecnologia como Problema |
|-------------------------|-------------------------|
| Deploy várias vezes por dia | Deploy uma vez por mês |
| Code review obrigatório | Código sem review |
| Testes automatizados | Testes manuais |
| Dívida técnica gerenciada | Dívida técnica crescendo |
| Postmortem sem culpa | Postmortem com busca de culpado |
| Documentação atualizada | Documentação inexistente |
| Time pequeno e autônomo | Time grande e burocrático |
| Métricas monitoradas | Métricas desconhecidas |

**Fator decisivo:** Cultura de **aprendizado contínuo** vs. cultura de **culpa e estagnação**.

---

## Psychological Safety — Segurança Psicológica

**Projeto Aristóteles do Google** (2016): estudo para entender o que fazia equipes eficazes.

**Resultado:** Segurança psicológica foi o **fator #1** para sucesso da equipe — mais importante que habilidade técnica, experiência ou estrutura.

Segurança psicológica = crença compartilhada de que é **seguro assumir riscos interpessoais** — perguntar, discordar, admitir erros.

### Como se Manifesta em Times de Engenharia

- Devs fazem "perguntas estúpidas" sem medo de julgamento
- Admitir erros abertamente, sem esconder bugs
- Sugerir ideias ousadas sem receio de ridicularização
- Júnior pode dar feedback honesto para sênior

### Como Construir

- Líderes **modelam vulnerabilidade** — "eu também erro, aqui está o que aprendi"
- Erro é oportunidade de aprendizado, não punição
- Feedback é bidirecional — hierarquia não bloqueia feedback
- Reconhecimento de quem admite erros publicamente
- "Blameless culture" — foco no sistema, não na pessoa

### Impacto Direto na Qualidade do Código

- Devs com psychological safety reportam bugs mais cedo (antes de virar incidente)
- Propõem refatorações sem medo de "crítica ao trabalho passado"
- Fazem code review honesto sem receio de conflito
- Resultado: menos dívida técnica escondida, código mais limpo

---

## Code Review Saudável

### Boas Práticas

- **Foco no código, não na pessoa:**
  - ✅ "Este trecho pode ser simplificado usando X"
  - ❌ "Você escreveu isso errado"
- **Comentários construtivos com explicação do porquê**
- **Reconhecer bom trabalho:** "Ótima solução para este problema!"
- **Tempo de resposta rápido:** < 1 hora para reviews urgentes, < 1 dia para normais
- **Distinguir nit-picks de blockers:** use prefixos como `nit:`, `blocker:`, `suggestion:`

### Estrutura de Comentário de Code Review

```
[nit] Variável poderia ter nome mais descritivo — `userList` → `activeUsers`
[suggestion] Considerar extrair essa lógica para um helper reutilizável
[blocker] Isso pode causar race condition sob alta concorrência — veja RFC-023
```

---

## Postmortem Sem Culpa (Blameless Postmortem)

### Princípio
- **Pergunta certa:** "O que falhou no **sistema**?" — não "Quem errou?"
- Foco em **processo**, não em pessoa
- Ações preventivas **específicas** (não apenas "cuidado mais")
- Compartilhado **publicamente** na empresa — todos aprendem

### Estrutura de Postmortem

```markdown
# Postmortem: Downtime de Pagamento (2026-05-15)

## O que aconteceu
Pagamentos falharam por 23 minutos às 14:30.
Impacto: ~R$ 45.000 em transações afetadas.

## Linha do Tempo
14:30 — Alertas de erro 500 em /api/payments
14:35 — On-call identificou falha no connection pool
14:53 — Rollback realizado, sistema restaurado

## Causa Raiz
Deploy de nova versão sem teste de carga em staging.
Connection pool sem limite máximo esgotou as conexões.

## O que NÃO fazer de novo
- Deploy sem teste de carga em staging
- Connection pool sem configuração de limite máximo

## Ações Preventivas
1. Teste de carga obrigatório antes de deploy em prod
   Responsável: João | Deadline: 2026-06-15
2. Adicionar limite máximo de connection pool em todos os serviços
   Responsável: Maria | Deadline: 2026-06-08
3. Alerta quando connection pool > 80%
   Responsável: DevOps | Deadline: 2026-06-10
```

**Regra:** Cada ação preventiva precisa de responsável, deadline e critério de sucesso. "Ser mais cuidadoso" não é ação.

---

## Cultura de Feedback Técnico Saudável

### Feedback em Daily

Daily não é relatório de status — é **resolução colaborativa de bloqueios**.

❌ "Ontem fiz X. Hoje vou fazer Y. Sem bloqueios."  
✅ "Estou travado em Z há 2 horas. Alguém tem experiência com isso?"

### Feedback em Retrospectiva

- **Dados primeiro:** "Cycle Time subiu 30% esta sprint" — não "achei que foi lenta"
- **1 ação de melhoria por retrospectiva** — não 10 ações que nunca são executadas
- Revisar ação da última retro no início de cada retro

### Feedback Individual (1:1)

- **SBI Framework:** Situação → Comportamento → Impacto
  - "Na planning de ontem (S), quando interrompeste o design (C), perdemos o contexto de UX antes de estimar (I)"
- **Feedback imediato** — não acumular para avaliação semestral
- Bidirecional — líderes solicitam feedback da equipe ativamente

---

## Como Empresas Desenvolvem Devs Internamente

**Stripe:**
- Stripe 101: onboarding de 2 semanas — 100% dos novos entendem produtos completos
- /dev/start: onboarding técnico com projeto prático real
- Rotações entre equipes — dev pode rodar em outro time por 6 meses
- Hackathons internos — 80 pessoas, 7 projetos lançados

**Práticas comuns em empresas maduras:**
1. Code review como aprendizado — sênior explica o "por quê" no review
2. Plano de desenvolvimento individual (IDP) — todo dev tem um roadmap de crescimento
3. Orçamento dedicado para conferências e cursos
4. Internal tech talks — devs ensinam outros devs mensalmente
5. Pair programming para transferência de conhecimento

---

## Burnout — O Inimigo Invisível da Engenharia

Ritmos frenéticos sem pausas deliberadas geram **burnout** (esgotamento profissional cognitivo) que paralisa a rotina produtiva de times e empresas.

### Por que o Burnout é um Problema de Processo, não de Pessoa

- Times sem proteção de ritmo (sprint protection, cooldown) acumulam dívida cognitiva
- "Crunch culture" — pressão comercial constante — produz código de baixa qualidade
- Developers sobrecarregados cometem mais erros, criam mais dívida técnica, geram mais incidentes
- É um ciclo que se retroalimenta: mais bugs → mais pressão → mais burnout

### Como Empresas Maduras Previnem

1. **Cooldown obrigatório** (Shape Up): 2 semanas entre ciclos de 6 semanas para estabilização e recuperação
2. **Sprint protection**: nada entra na sprint depois de iniciada — time não é interrompido
3. **WIP limits** (Kanban): impede que o time abranja mais do que consegue completar
4. **Autonomia real**: times que controlam seu processo têm menos burnout que times em modo "executor de ordens"
5. **Reuniões mínimas e assíncronas**: comunicação eficiente reduz overhead cognitivo

> "As engrenagens da produtividade não funcionam sem intervalos deliberados. O Cooldown não é luxo — é imunidade operacional." — Shape Up, Basecamp

---

## O que Basecamp Escreve Sobre Software Sustentável

### "Getting Real" (37signals)
1. **Comece pequeno** — MVP real, não "meio feito"
2. **Construa menos** — features excluídas são features que não precisa manter
3. **Itere rápido** — lançar, medir, aprender
4. **Interface é o produto** — UX não é depois, é antes
5. **Real > Perfeito** — lançar é melhor que perfeito

### "Shape Up" (Basecamp)
1. Ciclos de 6 semanas — tempo suficiente para entregar algo significativo
2. Apetite, não estimativa — "vale 2 semanas?" em vez de "quantas horas?"
3. Low-hierarchy — equipes têm autonomia real
4. Cool-down de 2 semanas — reparos e aprendizado
5. Betting, not planning — escolher no que apostar, não planejar tudo

---

## Referências
- Shape Up (gratuito): basecamp.com/shapeup
- Getting Real (gratuito): gettingreal.37signals.com
- Projeto Aristóteles Google: re:Work — Google
- [[estrutura-time-produto|Estrutura de Time de Produto →]]
- [[ferramentas-engenharia|Ferramentas e Sistemas →]]
- [[erros-projetos-software|Erros Comuns em Projetos →]]
