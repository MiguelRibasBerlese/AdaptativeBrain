---
type: conhecimento
status: active
projeto: boi-preto
tags: [padrão, react, typescript, tailwind, vite, frontend]
created: 2026-05-20
---

# 📐 Padrão: React + Vite + TypeScript + Tailwind

> Stack frontend padrão dos projetos de Miguel. Usado em boipreto, LinkaSite e outros.

---

## Stack Completa

```json
{
  "framework":  "React 19",
  "linguagem":  "TypeScript 5.8",
  "build":      "Vite 6",
  "estilo":     "Tailwind CSS 4",
  "roteamento": "React Router DOM 7",
  "animações":  "Motion 12 (Framer Motion fork)",
  "ícones":     "Lucide React",
  "deploy":     "Vercel"
}
```

---

## Inicialização

```bash
npm create vite@latest nome-projeto -- --template react-ts
cd nome-projeto
npm install
npm install tailwindcss @tailwindcss/vite
npm install react-router-dom lucide-react motion
```

---

## Padrão de Estrutura

```
src/
  components/     # componentes reutilizáveis
  pages/          # páginas/rotas
  constants/      # site.ts → conteúdo centralizado
  hooks/          # custom hooks
  types/          # tipos TypeScript
  assets/         # imagens, fontes
```

---

## Boas Práticas Observadas no boipreto

- **Conteúdo centralizado** em `src/constants/site.ts` — muda texto sem tocar nos componentes
- **Animações de entrada** com Motion para UX fluida
- **React Router DOM** para SPA multi-página
- Deploy automático no Vercel via push para `main`

---

## Quando Usar

- Sites institucionais/landing pages
- SPAs com múltiplas páginas
- Qualquer projeto frontend novo de Miguel

## Projetos que Usam

- [[../../01-Projetos/boi-preto/README|boipreto]]
- [[../../01-Projetos/linka-ecosystem/README|LinkaSite]]
