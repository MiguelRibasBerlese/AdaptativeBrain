---
type: conhecimento
status: active
tags: [padrão, saga, event-sourcing, outbox, microsserviços, transações-distribuídas]
created: 2026-05-20
---

# 🔄 Saga + Event Sourcing + Outbox Pattern

> Padrões para consistência em sistemas distribuídos onde transações ACID locais não são viáveis entre microsserviços.

---

## O Problema: Transações Distribuídas

Em microsserviços, cada serviço tem seu próprio banco. Uma operação que envolve múltiplos serviços (ex: Pedido → Pagamento → Estoque → Envio) não pode usar uma transação SQL única.

**2PC (Two-Phase Commit) — por que evitar:**
- Bloqueio de recursos por tempo indeterminado
- Single point of failure no coordenador
- Baixa disponibilidade em sistemas distribuídos

---

## Padrão Saga

Sequência de **transações locais independentes** coordenadas. Se uma falha, executa **Transações Compensatórias** na ordem inversa.

### Tipos de Ações em um Saga

| Tipo | Descrição |
|------|-----------|
| **Compensável** | Pode ser revertida por uma transação compensatória |
| **Pivô** | Ponto sem retorno — se passar aqui, o fluxo confirma |
| **Repetível** | Idempotente — pode ser tentada infinitamente até sucesso |

```
Fluxo de Pedido:
1. Criar Pedido       → compensatória: Cancelar Pedido
2. Reservar Estoque   → compensatória: Liberar Estoque
3. [PIVÔ] Cobrar Cartão → compensatória: Estornar Pagamento
4. Confirmar Pedido   → repetível (idempotente)
5. Enviar Pedido      → repetível
```

### Orquestração vs Coreografia

| Aspecto | Orquestração | Coreografia |
|---------|-------------|-------------|
| Coordenação | Serviço central (Saga Orchestrator) | Cada serviço reage a eventos |
| Visibilidade | Alta — estado centralizado | Baixa — difícil rastrear fluxo completo |
| Acoplamento | Orquestrador conhece todos os serviços | Serviços só conhecem eventos |
| Falha | Single point of failure | Malhas cíclicas difíceis de debugar |
| Ideal para | Fluxos lineares e complexos | Fluxos simples e bem definidos |

```python
# Orquestração — Saga Orchestrator
class OrderSagaOrchestrator:
    def execute(self, order_id: str):
        try:
            self.inventory_svc.reserve(order_id)
            self.payment_svc.charge(order_id)       # PIVÔ
            self.order_svc.confirm(order_id)
        except PaymentFailed:
            self.inventory_svc.release(order_id)    # compensação
            self.order_svc.cancel(order_id)         # compensação
```

---

## Event Sourcing

**Princípio:** armazenar todas as transições de estado como **fatos imutáveis** num Event Log. O estado atual é reconstruído reproduzindo os eventos.

```
# Ao invés de:
UPDATE orders SET status='confirmed', total=150 WHERE id=42

# Event Store armazena:
{ orderId: 42, event: "OrderCreated",   data: {...}, timestamp: T1 }
{ orderId: 42, event: "ItemAdded",      data: {...}, timestamp: T2 }
{ orderId: 42, event: "OrderConfirmed", data: {...}, timestamp: T3 }

# Estado atual = replay de T1 → T2 → T3
```

### Vantagens
- **Auditoria nativa** — log completo e imutável de tudo que aconteceu
- **Time travel** — reconstruir estado em qualquer ponto do passado
- **Event-driven** — outros serviços podem reagir a eventos publicados
- **Debug facilitado** — reproduzir exatamente o que ocorreu

### Desvantagens
- **Complexidade** — rebuild de estado pode ser lento (mitigado com Snapshots)
- **Espaço em disco** — cresce indefinidamente
- **Eventual consistency** — read models sempre defasados

### Snapshots
Para evitar replay longo de histórico:
```
A cada 100 eventos, salvar snapshot do estado atual
Replay = último snapshot + eventos após o snapshot
```

---

## Outbox Pattern

**Problema:** como garantir atomicidade ao salvar uma mudança de estado E publicar um evento para o message broker?

```
# Sem Outbox — problema de dual write:
BEGIN TRANSACTION
  UPDATE orders SET status='confirmed'   # OK
COMMIT
publish_to_kafka(OrderConfirmed)         # FALHA → estado inconsistente!
```

**Solução Outbox:** salvar evento na mesma transação do banco, um processo separado publica.

```sql
-- Mesma transação local:
BEGIN TRANSACTION;
  UPDATE orders SET status = 'confirmed' WHERE id = 42;
  INSERT INTO outbox (aggregate_id, event_type, payload)
    VALUES (42, 'OrderConfirmed', '{"orderId":42,...}');
COMMIT;

-- Processo Outbox Relay (separado, assíncrono):
SELECT * FROM outbox WHERE published = false;
-- Para cada evento: publish_to_kafka() → mark as published
```

**Garantia:** pelo menos uma entrega (at-least-once). Consumidores devem ser **idempotentes**.

---

## Composição: Saga + Event Sourcing + Outbox

```
Comando entra →
  Aggregate processa →
  Gera Domain Events →
  Outbox salva eventos na mesma transação →
  Outbox Relay publica no Kafka/RabbitMQ →
  Outros serviços consomem eventos →
  Saga Orchestrator avança ou compensa
```

---

## Referências
- [[ddd-domain-driven-design|DDD — contexto onde Aggregates emitem eventos →]]
- [[sistemas-distribuidos-ddia|DDIA — garantias de replicação e consistência →]]
- Microsoft Azure Architecture Center — Saga Pattern
