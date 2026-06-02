---
type: projeto
status: ativo
projeto: boi-preto
tags: [projeto, react, typescript, tailwind, freelance, vercel]
created: 2026-05-20
updated: 2026-05-20
---

# 🐂 boipreto — Site Institucional Boi Preto Consultoria

> Site institucional para a Boi Preto Consultoria Agropecuária — gestão de alta performance para produtores pecuaristas.

- **GitHub:** https://github.com/MiguelRibasBerlese/boipreto
- **Deploy:** Vercel
- **Última atualização:** Mai 2026
- **Tipo:** Freelance / Cliente

---

## 🎯 Objetivo

Vitrine digital da consultoria com páginas de serviços, cases de resultado, sobre e contato com integração WhatsApp.

---

## 📐 Stack

| Camada | Tecnologia |
|--------|-----------|
| Framework | React 19 |
| Linguagem | TypeScript 5.8 |
| Build | Vite 6 |
| Estilo | Tailwind CSS 4 |
| Roteamento | React Router DOM 7 |
| Animações | Motion 12 (Framer Motion fork) |
| Ícones | Lucide React |
| Mapas | react-simple-maps 3 |
| Deploy | Vercel |

---

## 📄 Páginas

- **Home** — hero, pain points, metodologia, CTA
- **Serviços** — cards dos 3 produtos de consultoria
- **Cases** — resultados auditáveis (Fazenda Nova Era)
- **Sobre** — narrativa de marca + mapa SVG interativo do Brasil
- **Contato** — formulário com integração WhatsApp + Google Maps embed

---

## 🔑 Fatos Críticos

- Conteúdo centralizado em `src/constants/site.ts` — editar aqui para mudar texto do site
- Mapa do Brasil: SVG interativo com estados em dourado e efeito glow
- Deploy automático via Vercel no push para main
- Formulário vai direto para WhatsApp (sem backend)
- Google Maps embed com filtro grayscale
