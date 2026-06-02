---
type: dashboard
tags: [dashboard]
updated: 2026-05-20
---

# 🧠 Adaptative Brain

---

## 📂 Projetos Ativos

```dataview
TABLE
  status AS "Status",
  updated AS "Atualizado"
FROM "01-Projetos"
WHERE type = "projeto" AND status = "ativo"
SORT updated DESC
```

---

## ✅ Tarefas Abertas

```dataview
TASK
FROM "01-Projetos"
WHERE !completed
GROUP BY file.link
SORT file.mtime DESC
LIMIT 20
```

---

## 📤 Outputs Recentes

```dataview
TABLE
  projeto AS "Projeto",
  file.mtime AS "Criado"
FROM "04-Outputs"
WHERE type = "output"
SORT file.mtime DESC
LIMIT 10
```

---

## 🧩 Conhecimento Recente

```dataview
TABLE
  tags AS "Tags",
  file.mtime AS "Adicionado"
FROM "02-Conhecimento"
WHERE type = "conhecimento"
SORT file.mtime DESC
LIMIT 8
```

---

## 📅 Sessões Recentes

```dataview
TABLE file.mtime AS "Data"
FROM "01-Projetos"
WHERE type = "sessão"
SORT file.mtime DESC
LIMIT 5
```

---

> **Protocolo:** [[PROTOCOLO-CLAUDE]] · **Projetos:** [[01-Projetos/_INDEX]] · **Outputs:** [[04-Outputs/_INDEX]]
