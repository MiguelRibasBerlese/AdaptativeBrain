---
type: conhecimento
status: active
tags: [padrão, sistemas-distribuídos, banco-de-dados, replicação, ddia, kleppmann]
created: 2026-05-20
---

# 📚 Sistemas Distribuídos — DDIA (Kleppmann)

> Síntese de "Designing Data-Intensive Applications" — Martin Kleppmann. Considerado o alicerce fundamental para compreensão de sistemas distribuídos.

---

## Motores de Armazenamento: LSM-Tree vs B-Tree

| Critério | LSM-Tree | B-Tree |
|----------|----------|--------|
| Escrita | Alta performance (append-only) | Moderada (in-place update) |
| Leitura | Mais lenta (múltiplos SSTables) | Alta performance |
| Espaço em disco | Compactação periódica necessária | Fragmentação interna |
| Uso típico | RocksDB, Cassandra, LevelDB | PostgreSQL, MySQL, SQLite |
| Write amplification | Menor | Maior |

**Regra prática:** LSM para workloads write-heavy; B-Tree para read-heavy com acesso por chave.

---

## Problemas de Replicação e Garantias de Leitura

O **replication lag** (atraso de replicação) é inerente a topologias com réplicas de leitura. A aplicação **deve codificar explicitamente** as garantias corretas para ocultar essas latências do usuário.

### 1. Read-After-Write (Leitura após Gravação)
**Problema:** Usuário atualiza dado → é roteado para réplica atrasada → vê estado antigo → parece que a ação falhou.

**Solução:**
- Após escrita, redirecionar leituras do mesmo usuário sempre para o líder por N segundos
- Ou: leituras de dados "editáveis pelo usuário" sempre vão ao líder

### 2. Monotonic Reads (Leituras Monotônicas)
**Problema:** Usuário consulta duas réplicas diferentes em sequência → segunda réplica está mais atrasada → usuário "volta no tempo".

**Solução:**
- Rotear todas as leituras de uma sessão para a mesma réplica
- Usar read token com versão lógica

### 3. Consistent Prefix Reads (Leitura de Prefixo Consistente)
**Problema:** Em topologias multi-líder ou sem líder, um observador pode ver eventos na ordem errada, violando causalidade.

**Solução:**
- Garantir que eventos causalmente relacionados sejam escritos na mesma partição
- Usar vector clocks para detectar dependências

---

## Particionamento (Sharding)

**Por range (intervalo):** consultas por range eficientes, mas hot spots em writes sequenciais (ex: timestamps).

**Por hash:** distribui writes uniformemente, mas range queries exigem scatter-gather para todas as partições.

**Particionamento secundário:** índices locais (mais rápido para writes, scatter para reads) vs índices globais (read eficiente, write mais complexo com consistência eventual).

---

## Níveis de Isolamento de Transações

| Nível | Dirty Read | Non-Repeatable Read | Phantom Read |
|-------|-----------|--------------------|----|
| Read Uncommitted | ✅ possível | ✅ | ✅ |
| Read Committed | ❌ | ✅ possível | ✅ |
| Repeatable Read | ❌ | ❌ | ✅ possível |
| Serializable | ❌ | ❌ | ❌ |

**MVCC (Multi-Version Concurrency Control):** cada transação vê um snapshot consistente do banco no momento do início — sem locks de leitura. Usado por PostgreSQL, MySQL InnoDB.

---

## Consensus e Replicação

**Algoritmo Raft:** líder eleito recebe todos os writes → replica em maioria antes de confirmar → garante durabilidade mesmo com falhas de minoria.

**Quorum:** em sistemas sem líder (Dynamo-style): `W + R > N` garante que leitura sempre intersecta escrita. Típico: N=3, W=2, R=2.

---

## Referência
- Livro: *Designing Data-Intensive Applications* — Martin Kleppmann (O'Reilly)
- [[livros-engenharia-senior|Ver lista completa de livros recomendados →]]
