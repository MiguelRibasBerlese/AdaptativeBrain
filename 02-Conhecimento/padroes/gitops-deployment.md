---
type: conhecimento
status: active
tags: [padrão, gitops, devops, kubernetes, argocd, cicd, deployment, plataforma]
created: 2026-05-20
---

# 🚀 GitOps — Padrão-Ouro de Deployment (2026)

> GitOps cristaliza o repositório Git como a única e irrefutável fonte da verdade sistêmica. Padrão dominante para Kubernetes em 2026.

---

## GitOps vs IaC Tradicional

| Aspecto | IaC Imperativo Tradicional | GitOps Declarativo |
|---------|--------------------------|-------------------|
| Modelo | Push-based (pipeline aplica) | Pull-based (agente lê e reconcilia) |
| Privilégios CI/CD | Alto (acesso direto ao cluster) | Mínimo (agente interno ao cluster) |
| Fonte da verdade | Estado atual do cluster | Repositório Git |
| Detecção de drift | Manual / alertas ad-hoc | Automática e contínua |
| Rollback | Ação manual ou script | `git revert` — versionado |
| Auditoria | Logs de CI | Git history completo |

### Por que Pull-based é mais seguro

Em CI push-based, a pipeline externa precisa de credenciais com acesso profundo ao Kubernetes API. Isso cria uma superfície de ataque enorme — se o CI for comprometido, o cluster também é.

No GitOps, o agente **vive dentro do cluster** e puxa as definições do Git. O Git não precisa de acesso ao cluster.

---

## Ferramentas Principais

### Argo CD
- Reconciliação contínua: compara estado desejado (Git) vs estado atual (cluster)
- Dashboard visual com status de cada recurso
- Suporta Helm, Kustomize, plain YAML
- Multi-cluster, multi-tenant

### Flux
- Mais minimalista e componentizável que Argo CD
- Integração nativa com GitHub/GitLab webhooks
- Image Automation: atualiza automaticamente tags de imagem no Git

---

## Boas Práticas GitOps

### 1. Separar repositórios de app e infra
```
app-repo/          # código da aplicação
  src/
  Dockerfile
  → CI builda imagem e faz push para registry
  → CI atualiza imagem no infra-repo

infra-repo/        # manifests de deploy
  environments/
    dev/
    staging/
    prod/
  → GitOps agent lê daqui e aplica no cluster
```

**Por quê:** evitar loop de pipeline — commit de código não re-trigga o deploy acidentalmente.

### 2. Ambientes por pastas, não por branches
```
# ✅ Correto
infra-repo/
  environments/
    dev/        kustomization.yaml
    staging/    kustomization.yaml
    prod/       kustomization.yaml

# ❌ Evitar
branch: dev     → manifests dev
branch: staging → manifests staging
branch: prod    → manifests prod
```
Branches por ambiente levam a merge conflicts complexos e divergência de configuração.

### 3. Tags de imagem: SHA, nunca `:latest`
```yaml
# ❌ Evitar — não determinístico
image: myapp:latest

# ✅ Correto — rastreável e imutável
image: myapp:sha256-a1b2c3d4...
# ou
image: myapp:1.2.3-abc1234
```

### 4. Secrets — nunca no Git
- Usar External Secrets Operator + HashiCorp Vault / AWS Secrets Manager
- Ou Sealed Secrets (criptografados, seguros para commitar)

---

## Progressive Delivery

### Blue-Green Deployment
```
Blue (v1 — 100% do tráfego)
Green (v2 — sem tráfego, testando)

→ Switch: Blue 0% / Green 100%
→ Se falhar: switch instantâneo de volta
```

### Canary Rollout
```
v1: 90% do tráfego
v2: 10% do tráfego → monitorar métricas
v2: 50% do tráfego → se OK
v2: 100% do tráfego → promoção completa
```

Ferramentas: **Argo Rollouts**, **Flagger** com Istio/Nginx.

### Feature Flags
Desacoplar deploy de release:
```python
if feature_flags.is_enabled("new-checkout", user_id):
    return new_checkout_flow()
else:
    return legacy_checkout_flow()
```

**Benefício:** código novo chega à produção dark (sem tráfego), validado gradualmente, revertível sem re-deploy.

---

## Pirâmide de Testes para CD

```
         /\
        /  \  E2E (5%)
       /----\
      /      \  Integração (15%)
     /--------\
    /          \  Unitários (80%)
   /____________\
```

**Gate de qualidade antes de merge:** mínimo 80% de cobertura nos unitários.

---

## Métricas de Recuperação

| Métrica | Descrição | Target |
|---------|-----------|--------|
| **RTO** (Recovery Time Objective) | Tempo máximo aceitável de indisponibilidade | Ex: < 15 min |
| **RPO** (Recovery Point Objective) | Perda máxima aceitável de dados | Ex: < 5 min de dados |
| **MTTR** (Mean Time to Recovery) | Tempo médio para recuperar de falhas | Monitorar tendência |
| **Change Failure Rate** | % de deploys que causam incidentes | < 15% (elite: < 5%) |

---

## Referências
- Pulumi Blog — GitOps Best Practices
- Argo CD Docs (argoproj.github.io)
- DORA Metrics (Google DevOps Research)
- [[owasp-supply-chain|OWASP A03 — Supply Chain → relevante para pipelines CI/CD →]]
