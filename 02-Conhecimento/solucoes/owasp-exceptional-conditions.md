---
type: conhecimento
status: active
tags: [segurança, owasp, exceções, error-handling, fail-secure, 2025]
created: 2026-05-20
---

# ⚠️ OWASP A10:2025 — Mishandling of Exceptional Conditions

> Nova entrada no Top 10 em 2025. 24 vulnerabilidades onde sistemas falham ao lidar com condições anômalas — colapsando sob concorrência, restrições imprevistas ou escassez de recursos.

---

## Princípio Fundamental: Fail Secure vs Fail Open

### Fail Secure (✅ Correto)
Sistema nega acesso ou para quando falha:
```python
def authenticate(token: str) -> User:
    try:
        return jwt.decode(token, SECRET, algorithms=["HS256"])
    except Exception:
        raise Unauthorized("Token inválido")  # NEGA — fail secure
```

### Fail Open (❌ Perigoso — CWE-636)
Sistema **permite acesso** quando falha — "a qualquer custo, continuar operando":
```python
def authenticate(token: str) -> User:
    try:
        return jwt.decode(token, SECRET, algorithms=["HS256"])
    except Exception:
        return AnonymousUser(admin=True)  # PERMITE — fail OPEN ← vulnerabilidade crítica
```

**Cenários reais de Fail Open:**
- Firewall com tabela cheia → reverte para política "allow all"
- Gateway de auth com servidor de validação indisponível → aprova por padrão
- Cipher negotiation falha → usa cipher mais fraco disponível em vez de rejeitar

---

## Categorias de Vulnerabilidades

### 1. Vazamento de Informação por Exceção (CWE-209, CWE-550, CWE-756)

**Problema:** exceções expostas revelam stack traces com paths internos, queries SQL, versões de framework.

```python
# ❌ Stack trace exposto em resposta HTTP
@app.errorhandler(Exception)
def handle_error(e):
    return str(e), 500  # expõe: "psycopg2.errors.UndefinedTable: relation 'users' does not exist at /app/db/models.py:42"

# ✅ Log interno, resposta genérica
@app.errorhandler(Exception)
def handle_error(e):
    logger.exception("Internal error", exc_info=e)
    return {"error": "Internal server error"}, 500
```

**ASP.NET:** garantir `<customErrors mode="On">` para ocultar stack traces em produção.

**JSP:** remover `debugEnabled=true` pós-testes.

### 2. Divisão por Zero e Null Pointers (CWE-369, CWE-476, CWE-754)

```python
# ❌ CWE-369 — Divisão por Zero
def average(items):
    return sum(items) / len(items)  # ZeroDivisionError se lista vazia

# ✅
def average(items):
    if not items:
        return 0
    return sum(items) / len(items)
```

```c
// ❌ CWE-476 — Null Pointer Dereference
char *data = malloc(1024);
strcpy(data, input);  // Se malloc falhou, data == NULL → crash

// ✅
char *data = malloc(1024);
if (!data) {
    log_error("malloc failed");
    return ERROR_OUT_OF_MEMORY;
}
```

### 3. Ignorar Valor de Retorno (CWE-252, CWE-390, CWE-391)

```c
// ❌ CWE-252 — Ignorar NULL de malloc
void *buf = malloc(size);
memcpy(buf, src, size);  // Se buf == NULL → undefined behavior

// ❌ CWE-390/391 — catch vazio
try {
    process_payment();
} catch (Exception e) {
    // silenciado — sistema continua corrompido internamente
}

// ✅
try {
    process_payment();
} catch (PaymentException e) {
    logger.error("Payment failed", e);
    throw new ServiceUnavailableException("Payment service error");
}
```

### 4. Tipos de Exceção Demasiado Genéricos (CWE-396, CWE-397)

```java
// ❌ CWE-396 — catch genérico destrói diagnóstico
public void processOrder() throws Exception {  // não informa o que pode falhar
    // ...
}

// ✅ Exceções específicas
public void processOrder() throws OrderNotFoundException, PaymentException, InventoryException {
    // ...
}
```

### 5. Switch sem Default e Fall-through (CWE-478, CWE-484)

```javascript
// ❌ CWE-478 — sem default
switch (userRole) {
    case 'admin':  grantFullAccess(); break;
    case 'user':   grantReadAccess(); break;
    // sem default → role desconhecida passa sem tratamento
}

// ✅
switch (userRole) {
    case 'admin':  grantFullAccess(); break;
    case 'user':   grantReadAccess(); break;
    default:       denyAccess(); throw new UnknownRoleError(userRole);
}

// ❌ CWE-484 — fall-through acidental em C/Java
switch (level) {
    case 3: doLevel3();  // sem break → cai para case 2 também!
    case 2: doLevel2();  // sem break → cai para case 1 também!
    case 1: doLevel1(); break;
}
```

### 6. Falha na Desalocação de Recursos (CWE-460, CWE-248, CWE-703)

```python
# ❌ Se exception ocorre, connection nunca é fechada
conn = db.connect()
result = conn.execute(query)
conn.close()  # nunca alcançado se exception acima

# ✅ Context manager garante cleanup
with db.connect() as conn:
    result = conn.execute(query)
# conn.close() chamado automaticamente, mesmo com exception
```

```java
// ❌ Thread pool vaza se exception não for capturada
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(task);
// executor.shutdown() nunca chamado se exception acima

// ✅
ExecutorService executor = Executors.newFixedThreadPool(10);
try {
    executor.submit(task).get();
} finally {
    executor.shutdown();  // sempre executado
}
```

### 7. Descalada de Privilégios Ignorada (CWE-274, CWE-280)

```python
# ❌ CWE-280 — ignorar falha na descalada
def process_data():
    elevate_privileges()       # sobe para admin
    sensitive_operation()
    drop_privileges()          # se falhar aqui...
    continue_as_user()         # executa como admin sem saber! (fail open)

# ✅ Verificar resultado da descalada
def process_data():
    elevate_privileges()
    try:
        sensitive_operation()
    finally:
        result = drop_privileges()
        if not result:
            terminate_session()  # fail secure — encerra sessão
            raise SecurityError("Privilege drop failed")
```

---

## Resumo das CWEs por Categoria

| Categoria | CWEs Principais |
|-----------|----------------|
| Fail Open | CWE-636 |
| Descalada de Privilégios | CWE-274, CWE-280 |
| Vazamento em Exceções | CWE-209, CWE-550, CWE-756 |
| Null/Zero/Overflow | CWE-369, CWE-476, CWE-754 |
| Ignorar Retorno | CWE-252, CWE-390, CWE-391, CWE-394 |
| Exceções Genéricas | CWE-396, CWE-397 |
| Switch sem Default | CWE-478, CWE-484 |
| Cleanup de Recursos | CWE-460, CWE-248, CWE-703, CWE-755 |

---

## Checklist de Revisão de Código

```
✓ Nenhum catch vazio (silenciado) — sempre logar ou relançar
✓ malloc()/calloc() com verificação de NULL em C/C++
✓ Context managers (with/using/try-with-resources) para recursos
✓ Switch/case tem cláusula default com ação explícita
✓ Exceções específicas, não `throws Exception` genérico
✓ Stack traces nunca expostos em respostas HTTP de produção
✓ Falha de auth → nega (fail secure), nunca permite
✓ Descalada de privilégios com verificação de resultado
✓ Divisão por zero verificada antes de operações aritméticas
```

---

## Referências
- owasp.org/Top10/A10_2025
- RavenTek — Mishandling of Exceptional Conditions (2025)
- [[owasp-top10-2025|OWASP Top 10 2025 — visão geral →]]
