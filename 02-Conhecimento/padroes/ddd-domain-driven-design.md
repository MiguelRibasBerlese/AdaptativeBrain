---
type: conhecimento
status: active
tags: [padrão, ddd, arquitetura, microsserviços, bounded-context, aggregate]
created: 2026-05-20
---

# 🏛️ Domain-Driven Design (DDD)

> Arcabouço tático e estratégico para modelar software diretamente sobre as complexidades do negócio.

---

## Camada Estratégica

### Event Storming
Técnica colaborativa para mapear o domínio cronologicamente — "o que acontece no sistema". Revelam-se processos ocultos e dependências cruzadas entre equipes.

**Notação de post-its:**
- 🟠 **Evento de Domínio** — algo que aconteceu (passado)
- 🔵 **Comando** — intenção de fazer algo
- 🟡 **Agregado** — onde o comando aterra
- 🟣 **Política** — reação automática a um evento
- 🔴 **Hot spot** — área de dúvida ou conflito

### Bounded Context (Contexto Delimitado)
Fronteira estrita onde uma **Linguagem Ubíqua** faz sentido total e inequívoco para desenvolvedores e especialistas do negócio.

```
Sistema de E-commerce
├── Contexto: Catálogo        → "Produto" = item com preço e descrição
├── Contexto: Estoque         → "Produto" = SKU com quantidade disponível
├── Contexto: Faturamento     → "Produto" = linha de nota fiscal
└── Contexto: Entrega         → "Produto" = item físico com peso/dimensão
```

**Regra:** o mesmo termo pode ter significados distintos em contextos diferentes — isso é intencional e correto.

### Context Map (Mapa de Contextos)
Padrões de relacionamento entre bounded contexts:

| Padrão | Descrição |
|--------|-----------|
| **Shared Kernel** | Dois contextos compartilham um subdomínio comum |
| **Customer-Supplier** | Downstream depende do upstream; upstream tem obrigação de atender |
| **Conformist** | Downstream aceita o modelo do upstream sem negociação |
| **Anti-Corruption Layer (ACL)** | Tradução entre modelos para não "contaminar" o domínio interno |
| **Open Host Service** | API pública bem documentada para integração |
| **Published Language** | Linguagem de troca padronizada (ex: eventos JSON, Protobuf) |

---

## Camada Tática

### Aggregate (Agregado)
Cluster de objetos de domínio tratados como uma **única unidade transacional**.

**Regras absolutas:**
1. Cada Aggregate possui uma **Aggregate Root** (Entidade Raiz)
2. Objetos externos só podem referenciar a **Aggregate Root**, jamais objetos internos
3. A consistência transacional e as invariantes de negócio são responsabilidade da Aggregate Root
4. Um Aggregate = uma transação (não atravesse fronteiras de Aggregate em uma transação)

```
Order (Aggregate Root)
  ├── OrderLine (Entity interno)
  ├── ShippingAddress (Value Object)
  └── PaymentInfo (Value Object)

# Externo só acessa Order, nunca OrderLine diretamente
```

### Entities vs Value Objects

| Tipo | Identidade | Mutabilidade | Exemplo |
|------|-----------|-------------|---------|
| **Entity** | Tem ID único | Mutável | `User(id=42)` |
| **Value Object** | Definido por atributos | Imutável | `Money(100, "BRL")` |
| **Aggregate Root** | Entity com responsabilidade de fronteira | Mutável | `Order(id=X)` |

### Domain Events
Fatos imutáveis que ocorreram no domínio. Publicados pela Aggregate Root após mudanças de estado.

```python
# Após confirmar pedido:
order.confirm()
# Emite: OrderConfirmed { orderId, customerId, totalAmount, confirmedAt }
```

### Domain Services
Operações de domínio que não pertencem naturalmente a uma Entity ou Value Object.

```python
# Não pertence nem a Order nem a Customer, mas usa ambos:
class PricingService:
    def calculate_discount(self, order: Order, customer: Customer) -> Money:
        ...
```

### Repositories
Abstração para persistência — a aplicação trabalha com objetos de domínio, não com SQL.

```python
class OrderRepository(Protocol):
    def find_by_id(self, id: OrderId) -> Order: ...
    def save(self, order: Order) -> None: ...
```

---

## Subdomínios

| Tipo | Descrição | Investimento |
|------|-----------|-------------|
| **Core Domain** | Diferencial competitivo do negócio | Máximo — desenvolver internamente |
| **Supporting Domain** | Suporta o core, não é diferencial | Moderado — pode terceirizar |
| **Generic Domain** | Commodity (auth, email, pagamento) | Mínimo — usar SaaS/libs prontas |

---

## Anti-Patterns DDD

- **Anemic Domain Model** — entidades apenas com getters/setters, lógica toda em Services → viola encapsulamento
- **God Aggregate** — um Aggregate com dezenas de sub-entidades → quebre em Aggregates menores
- **Cross-Aggregate Transaction** — uma transação modifica dois Aggregates → use Saga ou eventual consistency

---

## Referências
- *Domain-Driven Design* — Eric Evans
- *Implementing Domain-Driven Design* — Vaughn Vernon
- [[saga-event-sourcing-outbox|Saga + Event Sourcing + Outbox →]]
- [[grasp-principles|GRASP Principles →]]
