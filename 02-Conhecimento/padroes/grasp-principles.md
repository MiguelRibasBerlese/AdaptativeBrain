---
type: conhecimento
status: active
tags: [padrão, grasp, ooad, design, orientação-a-objetos]
created: 2026-05-20
---

# 🧭 GRASP — General Responsibility Assignment Software Patterns

> 9 princípios de Craig Larman para OOAD (Object-Oriented Analysis and Design). Respondem: **qual classe deve ser responsável por quê?**

---

## Os 9 Princípios

### 1. Information Expert
**Atribuir responsabilidade à classe que detém os dados necessários para executá-la.**

```python
# ✅ Order sabe calcular seu total (tem os OrderLines)
class Order:
    def calculate_total(self) -> Money:
        return sum(line.subtotal for line in self.lines)

# ❌ Evitar: extrair dados para calcular fora da classe
class OrderService:
    def calculate_total(self, order):  # vaza lógica para fora
        return sum(line.price * line.qty for line in order.lines)
```

**Impacto:** maximiza encapsulamento, evita getters massivos, alta coesão.

---

### 2. Creator
**A classe B deve criar a classe A se B: contiver, agregar, registrar, ou possuir os dados de inicialização de A.**

```python
# ✅ Order cria OrderLine (Order agrega OrderLines)
class Order:
    def add_item(self, product: Product, qty: int):
        line = OrderLine(product, qty)  # Order é o Creator
        self.lines.append(line)
```

**Impacto:** reduz acoplamento — instanciação vai para quem já tem conexão natural.

---

### 3. Controller
**Delegar eventos de sistema a uma classe não ligada à interface gráfica (camada de aplicação/serviço).**

```python
# ✅ Controller de aplicação — não é o componente de UI
class OrderController:
    def place_order(self, command: PlaceOrderCommand):
        order = self.order_repo.find(command.cart_id)
        order.confirm()
        self.order_repo.save(order)
        self.event_bus.publish(order.events)
```

**Impacto:** isola lógica de negócio da UI — múltiplos canais (REST, CLI, MQ) reutilizam o mesmo controller.

---

### 4. Low Coupling (Baixo Acoplamento)
**Manter o mínimo de dependências diretas entre componentes não relacionados.**

- Preferir injeção de dependência a instanciação direta
- Depender de interfaces, não de implementações concretas
- Mudança em módulo A não deve propagar quebras em cascata

---

### 5. High Cohesion (Alta Coesão)
**Responsabilidades de uma classe devem ser estritamente relacionadas e focadas.**

```python
# ❌ Baixa coesão — faz coisas demais
class UserService:
    def authenticate(self): ...
    def send_email(self): ...
    def generate_report(self): ...
    def process_payment(self): ...

# ✅ Alta coesão — responsabilidade única
class AuthService:
    def authenticate(self): ...
    def validate_token(self): ...
```

---

### 6. Polymorphism
**Lidar com variações de comportamento baseadas em tipo usando operações polimórficas.**

```python
# ❌ Evitar if/elif/switch por tipo
def process_payment(payment):
    if payment.type == "credit":  ...
    elif payment.type == "pix":   ...
    elif payment.type == "boleto": ...

# ✅ Polimorfismo — novos tipos sem modificar código existente
class Payment(Protocol):
    def process(self) -> PaymentResult: ...

class CreditCardPayment:
    def process(self): ...
class PixPayment:
    def process(self): ...
```

**Impacto:** elimina árvores de decisão, respeita Open/Closed Principle.

---

### 7. Pure Fabrication
**Criar uma classe sintética (não existente no modelo de domínio) para agrupar comportamentos.**

Usado quando atribuir a uma Entity violaria Alta Coesão ou Baixo Acoplamento.

```python
# OrderRepository não existe no domínio real, mas encapsula persistência
class OrderRepository:
    def save(self, order: Order): ...
    def find_by_id(self, id): ...
```

---

### 8. Indirection
**Atribuir responsabilidades a intermediários para desacoplar origem e destino.**

- **Facade** — interface simplificada sobre subsistema complexo
- **Adapter** — blinda domínio interno de APIs externas voláteis
- **Mediator** — centraliza comunicação entre componentes

```python
# Adapter: isola domínio da API externa do Stripe
class StripePaymentAdapter:
    def process(self, payment: Payment) -> PaymentResult:
        stripe_response = stripe_client.charge(payment.amount)
        return PaymentResult.from_stripe(stripe_response)
```

---

### 9. Protected Variations
**Envolver pontos de variação prevista com interfaces rígidas.**

Identificar onde o sistema VAI mudar e criar abstrações que protejam o núcleo.

```python
# Interface estável — implementação pode mudar (Redis, Postgres, Memcache)
class CachePort(Protocol):
    def get(self, key: str) -> Optional[str]: ...
    def set(self, key: str, value: str, ttl: int): ...
```

---

## Resumo Tabular

| Princípio | Pergunta | Anti-pattern Evitado |
|-----------|----------|---------------------|
| Information Expert | Quem sabe fazer? | Feature envy, getters excessivos |
| Creator | Quem deve instanciar? | Acoplamento por instanciação direta |
| Controller | Quem coordena o caso de uso? | Lógica na camada de UI |
| Low Coupling | Quantas dependências? | Ripple effect em mudanças |
| High Cohesion | Quantas responsabilidades? | God class |
| Polymorphism | Como variar comportamento por tipo? | Switch/if por tipo |
| Pure Fabrication | Onde colocar lógica sem dono natural? | Poluir classes de domínio |
| Indirection | Como desacoplar? | Dependência direta de externo |
| Protected Variations | O que vai mudar? | Código frágil a mudanças externas |

---

## Referências
- *Applying UML and Patterns* — Craig Larman
- [[ddd-domain-driven-design|DDD — complemento natural ao GRASP →]]
