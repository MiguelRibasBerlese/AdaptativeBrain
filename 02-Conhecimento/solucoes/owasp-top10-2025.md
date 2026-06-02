---
type: conhecimento
status: active
tags: [segurança, owasp, vulnerabilidades, 2025, web]
created: 2026-05-20
---

# 🔐 OWASP Top 10 — 2025

> Lista oficial das vulnerabilidades mais críticas em aplicações web. Atualizada em 2025 com mudanças substanciais.

---

## Lista Completa 2025

| Posição | Categoria | Mudança em relação a 2021 |
|---------|-----------|--------------------------|
| A01 | **Broken Access Control** | Mantida no topo — agora inclui SSRF |
| A02 | **Security Misconfiguration** | Subiu |
| A03 | **Software Supply Chain Failures** | ⭐ Nova entrada |
| A04 | **Insecure Design** | Mantida |
| A05 | **Cryptographic Failures** | Mantida |
| A06 | **Vulnerable & Outdated Components** | Absorvida parcialmente pelo A03 |
| A07 | **Identification & Authentication Failures** | Mantida |
| A08 | **Software & Data Integrity Failures** | Renomeada |
| A09 | **Security Logging & Monitoring Failures** | Mantida |
| A10 | **Mishandling of Exceptional Conditions** | ⭐ Nova entrada |

---

## A01 — Broken Access Control

**Inclui em 2025:** SSRF (Server-Side Request Forgery) incorporado a esta categoria.

**Vetores principais:**
- Acesso a recursos de outros usuários modificando ID na URL
- Elevação de privilégio horizontal (acessar conta de outro) e vertical (user → admin)
- SSRF: servidor faz requisição para URL controlada pelo atacante (acesso a metadata cloud, serviços internos)
- CORS mal configurado

**Mitigação:**
```python
# Sempre validar propriedade do recurso
def get_order(order_id: int, current_user: User):
    order = Order.find(order_id)
    if order.user_id != current_user.id:
        raise ForbiddenError()  # não apenas 404
    return order
```

---

## A02 — Security Misconfiguration

**Exemplos críticos:**
- Debug mode ativo em produção (`DEBUG=True` Django/Flask)
- `<customErrors mode="Off">` em ASP.NET → stack traces expostas
- Credenciais padrão de banco/admin não alteradas
- Serviços de cloud com permissões excessivas (S3 público, IAM overpermissioned)
- Headers de segurança ausentes (HSTS, CSP, X-Frame-Options)

**Checklist básico:**
```
✓ DEBUG=False em produção
✓ Remover endpoints de debug/health com info sensível
✓ Configurar Security Headers (use securityheaders.com para validar)
✓ Principle of Least Privilege em IAM/Service Accounts
✓ Desabilitar directory listing
```

---

## A03 — Software Supply Chain Failures ⭐ Nova

→ [[owasp-supply-chain|Ver nota detalhada: A03 Supply Chain →]]

---

## A05 — Cryptographic Failures

**Erros comuns:**
- MD5/SHA1 para senhas (usar bcrypt/argon2 mínimo)
- ECB mode em AES (usar GCM ou CBC com IV aleatório)
- Chaves hardcoded no código-fonte
- TLS 1.0/1.1 ainda habilitados
- Dados sensíveis em logs

**Regra mínima para senhas:**
```python
# ✅ bcrypt com work factor adequado
from bcrypt import hashpw, gensalt
hashed = hashpw(password.encode(), gensalt(rounds=12))
```

---

## A07 — Authentication Failures

**Vetores:**
- Brute force sem rate limiting
- Tokens JWT com `alg: none`
- Session ID não regenerado após login (session fixation)
- "Lembrar senha" em cookie sem httpOnly e Secure

**JWT — validações obrigatórias:**
```python
# Sempre validar: assinatura, exp, iss, aud
jwt.decode(token, SECRET, algorithms=["HS256"],
           options={"require": ["exp", "iss"]})
```

---

## A10 — Mishandling of Exceptional Conditions ⭐ Nova

→ [[owasp-exceptional-conditions|Ver nota detalhada: A10 Exceptional Conditions →]]

---

## OWASP API Security Top 10 (bônus)

Para APIs (REST/GraphQL):

| # | Vulnerabilidade |
|---|----------------|
| API1 | Broken Object Level Authorization (BOLA/IDOR) |
| API2 | Broken Authentication |
| API3 | Broken Object Property Level Authorization |
| API4 | Unrestricted Resource Consumption |
| API5 | Broken Function Level Authorization |
| API6 | Unrestricted Access to Sensitive Business Flows |
| API7 | Server Side Request Forgery |
| API8 | Security Misconfiguration |
| API9 | Improper Inventory Management |
| API10 | Unsafe Consumption of APIs |

---

## Referências
- owasp.org/Top10
- [[owasp-supply-chain|A03: Supply Chain →]]
- [[owasp-exceptional-conditions|A10: Exceptional Conditions →]]
