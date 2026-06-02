---
type: conhecimento
status: active
tags: [referência, livros, engenharia, sênior, leitura]
created: 2026-05-20
---

# 📚 Literatura Essencial — Engenheiro Sênior

> Livros frequentemente adotados por engenheiros de nível sênior e staff engineers. Foco em escalabilidade, arquitetura e pensamento sistêmico.

---

## Sistemas Distribuídos e Dados

### Designing Data-Intensive Applications
**Autor:** Martin Kleppmann | **Editora:** O'Reilly

> "O alicerce fundamental para compreensão de sistemas distribuídos."

**O que cobre:**
- Motores de armazenamento (LSM-Tree vs B-Tree)
- Replicação e garantias de leitura (read-after-write, monotonic reads)
- Particionamento e sharding
- Transações distribuídas e níveis de isolamento
- Sistemas de mensagens e stream processing

**Nível:** Sênior / Staff
→ [[../padroes/sistemas-distribuidos-ddia|Síntese dos conceitos →]]

---

## Design de Código e Arquitetura

### A Philosophy of Software Design
**Autor:** John Ousterhout | **Editora:** Yaknyam Press

> Desmistifica a falsa dicotomia entre performance e código limpo.

**Conceitos centrais:**
- **Módulos devem ser profundos:** interface simples, implementação densa — não o inverso
- **Information Hiding:** ocultar detalhes de implementação é mais importante do que parece
- **Define errors out of existence:** design que torna erros impossíveis > handling de erros
- **Módulos rasos** (shallow): muita interface, pouca funcionalidade → acoplamento alto e carga cognitiva

```
Módulo Profundo (✅):
Interface: simples
Implementação: complexa, encapsulada

Módulo Raso (❌):
Interface: expõe detalhes internos
Implementação: trivial
```

**Nível:** Pleno / Sênior

---

### Clean Architecture
**Autor:** Robert C. Martin (Uncle Bob) | **Editora:** Pearson

**Conceitos:**
- Dependency Rule: dependências apontam para dentro (domínio)
- Camadas: Entities → Use Cases → Interface Adapters → Frameworks
- Entidades e casos de uso devem ser independentes de frameworks

**Nível:** Pleno / Sênior

---

### Domain-Driven Design
**Autor:** Eric Evans | **Editora:** Addison-Wesley

> O livro azul. Define a linguagem ubíqua do DDD.

→ [[../padroes/ddd-domain-driven-design|Síntese DDD →]]

**Nível:** Sênior

---

## Liderança Técnica

### The Staff Engineer's Path
**Autor:** Tanya Reilly | **Editora:** O'Reilly

> Navegação da liderança técnica **sem autoridade gerencial direta**.

**O que cobre:**
- Diferença entre Staff, Principal e Distinguished Engineer
- Influência técnica sem poder de comando
- Comunicação com stakeholders não-técnicos
- Como fazer trabalho de alto impacto sem gerenciar pessoas
- "Glue work" — o trabalho invisible que mantém times funcionando

**Nível:** Sênior → Staff

---

## Concorrência e Sistemas de Baixo Nível

### Rust Atomics and Locks
**Autor:** Mara Bos | **Editora:** O'Reilly (Open Access)

> Rigor necessário sobre ordenação de memória e concorrência em baixo nível.

**O que cobre:**
- Memory ordering (Relaxed, Acquire, Release, SeqCst)
- Diferenças entre x86_64 (TSO) e ARM (weakly ordered) 
- Atomics: quando usar vs Mutex
- Lock-free programming
- Unsafe Rust para abstrações de baixo nível

**Nível:** Sênior / Systems engineer
**Gratuito online:** marabos.nl/atomics

---

## Práticas de Engenharia

### The Pragmatic Programmer
**Autor:** David Thomas & Andrew Hunt | **Editora:** Pragmatic Bookshelf

**Conceitos clássicos:**
- DRY — Don't Repeat Yourself
- Orthogonality — mudanças em A não devem afetar B
- Tracer Bullets — implementação end-to-end mínima antes de detalhar
- "Boiled frog" — detectar degradação incremental antes que seja tarde

---

### Accelerate: The Science of Lean Software and DevOps
**Autor:** Nicole Forsgren, Jez Humble, Gene Kim | **Editora:** IT Revolution

> Pesquisa científica sobre o que diferencia times de alta performance (DORA Metrics).

**4 métricas-chave:**
1. **Deployment Frequency** — com que frequência fazem deploy
2. **Lead Time for Changes** — tempo de commit até produção
3. **Change Failure Rate** — % de deploys que causam incidentes
4. **Time to Restore Service** — MTTR

**Elite performers (2026):**
- Deploy múltiplas vezes por dia
- Lead time < 1 hora
- Change failure rate < 5%
- MTTR < 1 hora

---

## Segurança

### The Web Application Hacker's Handbook
**Autores:** Stuttard & Pinto | **Editora:** Wiley

> Cobre metodicamente as vulnerabilidades web com exemplos de exploração e mitigação.

---

## Ordem de Leitura Recomendada

```
1º. The Pragmatic Programmer          → fundamentos de mentalidade
2º. A Philosophy of Software Design   → design de módulos e código
3º. Clean Architecture                → arquitetura em camadas
4º. Designing Data-Intensive Apps     → sistemas distribuídos
5º. Domain-Driven Design              → modelagem de domínio complexo
6º. Accelerate                        → métricas e práticas DevOps
7º. The Staff Engineer's Path         → liderança técnica
8º. Rust Atomics and Locks            → concorrência de baixo nível
```
