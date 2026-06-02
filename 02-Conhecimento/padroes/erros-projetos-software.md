---
type: conhecimento
status: active
tags: [padrão, erros, falhas, scope-creep, estimativas, chaos-report, projetos]
created: 2026-06-01
updated: 2026-06-01
---

# 💀 Erros Mais Comuns em Projetos de Software

> Por que a maioria dos projetos falha — com dados reais do Chaos Report.

---

## Dados Reais: Chaos Report 2024 (Standish Group)

| Métrica | Dado |
|---------|------|
| Projetos cancelados | **31.1%** |
| Projetos que superam orçamento | 52.7% |
| Projetos que terminam com sucesso | **Apenas 16.2%** |
| Sobrecusto médio | 189% |
| Sobreestimação de tempo | 222% |
| Grandes empresas entregam | 42% das funcionalidades propostas |
| Pequenas empresas entregam | 78.4% das funcionalidades |

**Conclusão: 4 de cada 5 projetos falham de alguma forma.**

### Principais Causas de Falha (Chaos Report)
1. Requisitos incompletos ou mudando constantemente
2. Falta de envolvimento do usuário final
3. Expectativas irrealistas de prazo/orçamento
4. Falta de apoio da liderança
5. Technology stack inadequada para o problema

---

## Os 10 Erros Mais Comuns em Projetos Internos de TI

1. **Construir sem validar o problema** — ninguém vai usar (o problema do "ninguém vai usar")
2. **Scope creep** — escopo infinito sem controle
3. **Estimativas otimistas demais** — sempre erradas
4. **Falta de documentação** — conhecimento tribal
5. **Não priorizar dívida técnica** — sistema fica lento e frágil
6. **Times muito grandes** — comunicação cai exponencialmente (Lei de Brooks)
7. **Mudanças frequentes de prioridade** — time perde foco e contexto
8. **Falta de automação** (CI/CD, testes) — tudo manual = gargalo
9. **Não medir resultado** — não sabe se o projeto funcionou
10. **Ignorar UX** — software funcional mas impossível de usar

---

## Por Que Softwares Internos São Construídos e Nunca Adotados

**Causa raiz:** Desenvolvedores constroem para **si mesmos**, não para usuários reais.

**Sintomas:**
- "Nós sabemos o que os usuários precisam" — sem entrevistar ninguém
- Interface técnica demais para não-técnicos
- Feature creep: adicionar features porque "seria legal", não porque resolve dor real
- Deploy interno sem treinamento ou onboarding

**Solução:** Entrevistas com usuários internos **antes** de desenvolver. Problem framing é essencial.

---

## Scope Creep — O Inimigo do Prazo

Scope creep é quando o escopo do projeto cresce além do planejado.

### Causas

- Requisitos não bem definidos inicialmente
- Stakeholders adicionando funcionalidades durante o desenvolvimento
- Falta de processo formal de mudança
- A frase mais perigosa: **"Enquanto estamos nisso, poderia também..."**

### Como Empresas Maduras Evitam

1. **PRD claro** com o que NÃO está no escopo (out of scope explícito)
2. **Mudanças requerem aprovação formal** + atualização de PRD
3. **Novas features vão para o backlog** — não para a sprint em andamento
4. **Shape Up:** apetite definido antes, não durante
5. **Sprint protection:** nada entra na sprint depois de iniciada

---

## Por Que Estimativas São Sempre Erradas

**Lei de Hofstadter:** *"Sempre leva mais tempo do que você espera, mesmo quando você leva em conta a Lei de Hofstadter."*

### Por que Erram

- Imprevistos não são considerados (bugs inesperados, dependências)
- Complexidade técnica subestimada
- Interrupções não contabilizadas
- Desenvolvedor estima em "tempo focado" — trabalho real tem reuniões, contexto switching, review
- **Planning Fallacy:** tendência cognitiva de subestimar tarefas próprias

### Como Mitigar

1. **Estimativas em faixas**, não pontos exatos: "2–3 semanas", não "12 dias"
2. **Baseado em dados históricos** do time, não em intuição
3. **Buffer de 30–50%** para imprevistos (para projetos novos, até 100%)
4. **Plano de 3 níveis:** melhor caso / caso provável / pior caso
5. **Medir velocity do time** e usar para estimativas futuras
6. **Nunca prometer a data, prometer a probabilidade:** "85% de chance de entregar em X"

---

## 10 Erros Mais Letais — Tabela Completa

| # | Categoria | Consequência Sistêmica |
|---|-----------|----------------------|
| 1 | **Má Gestão de Escopo (Scope Creep)** | Requisitos acrescidos sem ajuste de tempo/verba → colapso do prazo |
| 2 | **Desconexão Total do Usuário Real** | Liderança define "o quê" ignorando o workflow doloroso da operação → adoção zero |
| 3 | **Falha na Integração (Lei de Brooks)** | Adicionar pessoas a projetos atrasados gera rampa de treinamento que atrasa ainda mais |
| 4 | **Código Pobre e Dívida Técnica Explosiva** | Acoplamento profundo e duplicação impossibilitam correções futuras |
| 5 | **Arquitetura Over-Engineered** | Infra em hiperescala desnecessária para problemas simples → estagnação do time |
| 6 | **Integração Tardia (Big Bang)** | Adiar integrações sistêmicas → conflitos de interface emergem criticamente na véspera do lançamento |
| 7 | **Dependência de Especificações Vagas** | Assunções errôneas entre linguagem de engenheiros e stakeholders de mercado |
| 8 | **Descaso na Falha Operacional Parcial** | Foco só no "Happy Path" — ignora o que acontece quando banco cai ou rotinas quebram |
| 9 | **Estimativas por Pressão Comercial** | Cronogramas fixados por marketing, não pela capacidade real do código base |
| 10 | **Ignorar Gargalos Transversais Periféricos** | Negligenciar índices de banco, provisionamento de CDN e segurança arruína performance em produção |

### Erros 5–10 em Detalhe

**Over-Engineering (Erro 5):**
Desenvolvedores criam infraestrutura de hiperescala para problemas simples. Regra prática: YAGNI — *You Ain't Gonna Need It*. Comece simples, adicione complexidade somente quando métricas justificarem.

**Integração Tardia / Big Bang (Erro 6):**
Times desenvolvem módulos isolados por meses e só integram no final. Resultado: interfaces incompatíveis, dependências ocultas, crise na véspera do lançamento. Solução: integração contínua (CI) desde o dia 1.

**Happy Path Only (Erro 8):**
Sistema testado apenas no fluxo ideal. Não testam: banco de dados fora do ar, timeout de API externa, submissão duplicada, permissão negada. Solução: testes de falha obrigatórios no DoD.

**Estimativas Políticas (Erro 9):**
Prazo definido pelo marketing antes de qualquer análise técnica. PM anuncia data pública → engenheiros viram "entregadores de promessa impossível". Solução: Shape Up — apetite define o prazo, não a pressão.

**Gargalos Periféricos (Erro 10):**
Índices de banco ausentes, CDN sem provisionamento, chaves de BD sem otimização. Invisíveis no desenvolvimento, devastadores em produção com carga real.

---

## A Lei de Brooks

> "Adding manpower to a late software project makes it later." — Fred Brooks, The Mythical Man-Month (1975)

**Por que adicionar pessoas atrasa mais:**
- Novos membros precisam de tempo de onboarding
- Comunicação cresce como `n(n-1)/2` — 10 pessoas = 45 canais de comunicação
- Quem já está no projeto perde tempo treinando quem chegou

**Implicação prática:** times pequenos (≤ 6 pessoas por squad) entregam mais rápido que times grandes.

---

## Dívida Técnica — O Imposto Invisível

Dívida técnica é o custo acumulado de soluções rápidas/temporárias que viram permanentes.

### Como Empresas Maduras Gerenciam

1. **Tornar visível:** dashboards, métricas de test coverage, build time, code smell count
2. **Alocar capacidade fixa:** 20% de cada sprint para dívida técnica
3. **Priorizar estrategicamente:**
   - Dívida que bloqueia features → alta prioridade
   - Dívida cosmética → baixa prioridade
4. **Prevenir nova dívida:** code review obrigatório + testes desde o início
5. **Shape Up:** 2 semanas de cool-down entre ciclos para reparos

**Anti-pattern:** "Refatoraremos depois" — depois nunca chega.

---

## Referências
- Chaos Report — Standish Group (scribd.com/doc/258764185/chaos-report)
- *The Mythical Man-Month* — Fred Brooks
- [[validacao-pre-desenvolvimento|Validar Antes de Construir →]]
- [[documentacao-prd-rfc-dod|PRD, RFC e Definition of Done →]]
- [[ferramentas-engenharia|Ferramentas e Sistemas →]]
