---
type: projeto
status: ativo
projeto: radar-linka
tags: [projeto, python, ai, scraping, vercel, linka]
created: 2026-05-20
updated: 2026-05-20
---

# 🎯 RadarLinka — Agregador Inteligente de Eventos

> Agrega, normaliza, deduplica e classifica eventos de Ribeirão Preto e região via scraping + IA, entregando via interface web estática no Vercel.

- **GitHub:** https://github.com/MiguelRibasBerlese/RadarLinka
- **Live:** https://radar-olive-five.vercel.app
- **Última atualização:** Mai 2026
- **Licença:** MIT

---

## 🎯 Objetivo

Ser o agregador central de eventos de Ribeirão Preto e +24 cidades num raio de 200km, sem depender de banco de dados.

---

## 📐 Arquitetura / Stack

| Camada | Tecnologia |
|--------|-----------|
| Scraping | Python 3.12, Playwright, BeautifulSoup4, Requests |
| AI/Classificação | Groq API — llama-3.3-70b-versatile |
| Armazenamento | JSON estático (sem banco de dados) |
| Frontend | Vanilla HTML/CSS/JS, Three.js, Google Fonts |
| Chatbot | ORBIT — Python serverless + Groq |
| Deploy | Vercel (estático + Python serverless functions) |

---

## 🏗️ Pipeline de Processamento

```
Coleta (8 fontes) → Normalização → Classificação → JSON → Frontend
```

**Fontes:** Sympla, EmRibeirão, RibeirãoShopping, Eventoon, Sindtur, Songkick, Varal, SearchAPI

**Classificação híbrida:**
- Keywords rules → maioria dos eventos
- Groq API (llama-3.3-70b) → entradas ambíguas

**13 categorias:** shows, festivais, teatro, esportes, etc.

---

## ⚡ Features-Chave

- Zero banco de dados — geração 100% estática
- Progressive rendering (40 cards por vez)
- Filtros colapsáveis interativos
- Tela de loading com globo 3D (Three.js)
- Dark/light mode
- Chatbot ORBIT integrado (Groq)
- Cobertura: 24+ cidades, 200km de raio

---

## 🔑 Fatos Críticos

- Classificação usa **Groq** (não OpenAI) — modelo llama-3.3-70b-versatile
- Output final é um arquivo JSON consumido pelo frontend
- Serverless functions no Vercel para o chatbot ORBIT
- Playwright necessário para scraping de fontes com JS dinâmico
