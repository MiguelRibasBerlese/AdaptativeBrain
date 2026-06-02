---
type: conhecimento
status: active
projeto: radar-linka
tags: [padrão, python, scraping, groq, ai, automação, vercel]
created: 2026-05-20
---

# 📐 Padrão: Pipeline Python — Scraping + Groq AI + Vercel

> Pipeline de automação usado no RadarLinka. Padrão para projetos de coleta + classificação com AI.

---

## Stack

```
Python 3.12
├── Playwright          # scraping de páginas com JS dinâmico
├── BeautifulSoup4      # parsing HTML
├── Requests            # HTTP simples
├── Groq API            # AI para classificação (llama-3.3-70b-versatile)
└── Output: JSON estático
```

**Deploy:** Vercel (estático + Python serverless functions)

---

## Arquitetura do Pipeline

```
Fontes (N) → Scraper → Normalizador → Classificador → JSON → Frontend
                                           │
                                    Keywords Rules
                                    + Groq (ambíguos)
```

---

## Groq API — Uso

```python
from groq import Groq

client = Groq(api_key=os.environ["GROQ_API_KEY"])

response = client.chat.completions.create(
    model="llama-3.3-70b-versatile",
    messages=[
        {"role": "system", "content": "Classifique o evento..."},
        {"role": "user",   "content": evento_texto}
    ]
)
categoria = response.choices[0].message.content
```

---

## Playwright — Scraping Dinâmico

```python
from playwright.async_api import async_playwright

async with async_playwright() as p:
    browser = await p.chromium.launch(headless=True)
    page = await browser.new_page()
    await page.goto(url)
    await page.wait_for_selector(".evento")
    content = await page.content()
    await browser.close()
```

---

## Vercel Serverless (Python)

Criar `api/[função].py`:
```python
from http.server import BaseHTTPRequestHandler

class handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b'{"status": "ok"}')
```

---

## Quando Usar

- Projetos de scraping de dados de múltiplas fontes
- Classificação/categorização com AI onde regras fixas não bastam
- APIs serverless simples no Vercel

## Projetos que Usam

- [[../../01-Projetos/radar-linka/README|RadarLinka]]
