---
type: conhecimento
status: active
tags: [padrão, mvp, validação, discovery, problem-framing, build-vs-buy, entrevistas]
created: 2026-06-01
updated: 2026-06-01
---

# 🧪 Como Validar Antes de Construir

> 5 entrevistas com usuários valem mais que 50 horas de código errado.

---

## MVP para Ferramentas Internas

**MVP (Minimum Viable Product)** para ferramentas internas:
- Versão mais simples que **resolve o problema central** do usuário
- Não é "produto meio feito" — é produto com features mínimas necessárias
- Pode ser manual por trás (**Wizard of Oz MVP**)

### Exemplo Prático
```
Problema: "Leva 3 horas gerar relatório semanal de vendas"

MVP ruim: Sistema completo com dashboard, filtros, exports, API...
MVP certo: Script Python que gera relatório em 15 minutos

Validação: 5 usuários internos usam por 1 semana → se funcionar, aí sim construir sistema
```

---

## Problem Framing — Definir o Problema Antes da Solução

Problem Framing é garantir que você está resolvendo **o problema certo** antes de pensar em soluções.

### Template de Problem Framing

```
Problema:        [O que está acontecendo de ruim]
Usuário afetado: [Quem sofre com isso]
Frequência:      [Quantas vezes por dia/semana]
Impacto:         [Quanto tempo/dinheiro é perdido]
Causa raiz:      [Por que isso acontece — não o sintoma]
Critérios de sucesso: [Como sabemos que resolvemos o problema]
```

### Como Aplicar Antes de Qualquer Projeto

1. Time preenche problem framing juntos (1–2 horas)
2. Valida com usuários reais (entrevistas)
3. Só então brainstorm de soluções

**Erro comum:** pular para soluções sem entender o problema. Time gasta semanas construindo a coisa errada.

---

## Entrevistas com Usuários Internos

### Processo Prático

1. Selecionar 5–8 usuários representativos
2. Roteiro de entrevista (30–45 min):
   - *"Conte-me sobre como você faz X hoje"*
   - *"O que mais te frustra nesse processo?"*
   - *"Quanto tempo isso leva por semana?"*
   - *"O que faria sua vida mais fácil?"*
3. Gravar com permissão e transcrever para insights
4. Identificar padrões entre entrevistas
5. **Validar o problema**, não propor solução ainda

### Erros Comuns em Entrevistas

❌ Perguntar: "Você usaria isso?" → todo mundo diz sim  
✅ Perguntar: "Quantas vezes você teve esse problema na última semana?"

❌ Descrever a solução e pedir opinião → enviés de confirmação  
✅ Descrever o problema e pedir experiências passadas

❌ Entrevistar apenas 1–2 pessoas → amostra insuficiente  
✅ Mínimo 5 pessoas para identificar padrões

---

## Técnicas de Validação Sem Escrever Código

| Técnica | O que é | Quando usar |
|---------|---------|-------------|
| **Protótipo no Figma** | Usuários clicam e testam fluxo | Validar UX antes de codar |
| **Concierge MVP** | Você faz manualmente o que o software faria | Validar demanda sem desenvolver |
| **Wizard of Oz** | Usuário acha que é automático, mas você opera por trás | Validar viabilidade de IA/automação |
| **Landing page** | Página com botão "Começar" que mede interesse | Validar produto externo |
| **Google Forms + Planilha** | Simula coleta de dados antes do sistema | Validar processo interno |

### Exemplo Real
```
Ideia:   "Sistema de onboarding automatizado para novos clientes"

Teste:   Google Form + email manual por 2 semanas
         (simula o que o sistema faria, mas manualmente)

Resultado: 8/10 clientes completaram o processo sem dúvidas
           → Vale construir o sistema
```

---

## Build vs. Buy — Como Decidir

"Construir internamente ou comprar uma solução pronta?"

### Matriz de Decisão

| Fator | Build | Buy |
|-------|-------|-----|
| É core business / diferencial competitivo? | Build | — |
| É commodity (RH, payroll, email, auth)? | — | Buy |
| Tempo para mercado: 6+ meses aceitos? | Build | — |
| Precisa agora (< 2 meses)? | — | Buy |
| Time capaz de manter? | Build | — |
| Sem time de manutenção? | — | Buy |

### Categorias

1. **Core + Diferencial → Build** (ex: algoritmo de recomendação da Netflix)
2. **Core + Commodity → Avaliar** (depende de recursos e timing)
3. **Context + Qualquer coisa → Buy** (ex: sistema de RH, CRM genérico, autenticação)

### TCO — Total Cost of Ownership (5 anos)

**Build:**
- Salários de devs para construir + manter
- Infraestrutura
- Bugs, upgrades, segurança

**Buy:**
- Assinatura mensal/anual
- Customizações e integrações
- Treinamento

> **Frequentemente buy é mais barato a longo prazo** — especialmente para ferramentas não-core.

---

## No-Code / Low-Code para Validação Interna

Plataformas No-Code e Low-Code permitem validar lógica de negócio sem onerar engenheiros sênior.

**Ferramentas:** UI Bakery, Knack, Stackby, Retool, Notion databases, Airtable

### Como Usar para Validação

- Analistas corporativos constroem protótipos funcionais em dias, não semanas
- Validam aprovações, gestão de inventário, fluxos de aprovação contábil
- Custo: fração do desenvolvimento tradicional

### Atenção: "Happy Path" não é suficiente

Antes de usar o protótipo no-code no dia a dia, **é obrigatório testar os cenários de falha:**

1. **Erros parciais de API** — o que acontece se a chamada externa falha na metade?
2. **Submissões duplicadas** — o sistema previne duplicate inserts?
3. **Permissões** — cada cargo acessa apenas o que deve acessar?
4. **Auditoria** — há logs imutáveis de quem editou o quê e quando?

> Testar somente o fluxo ideal mascara falhas que só aparecem em produção, com dados reais e usuários não técnicos.

---

## Validação de Ideias em Empresas Maduras

Checklist antes de aprovar qualquer projeto novo:

- [ ] Problem framing preenchido e validado com usuários
- [ ] Entrevistas realizadas (≥ 5 usuários)
- [ ] Problema real confirmado (não hipotético)
- [ ] Build vs Buy avaliado
- [ ] MVP definido (qual é a versão mais simples possível?)
- [ ] Métricas de sucesso definidas
- [ ] Critério de "matar o projeto" definido (quando parar)

---

## Referências
- [[gestao-projetos-internos|Gestão de Projetos Internos →]]
- [[erros-projetos-software|Erros Comuns →]]
- [[documentacao-prd-rfc-dod|PRD e RFC →]]
