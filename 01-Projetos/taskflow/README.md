---
type: projeto
status: concluído
projeto: taskflow
tags: [projeto, flutter, dart, firebase, mobile, acadêmico, unaerp]
created: 2026-05-20
updated: 2026-05-20
---

# 📱 TaskFlow — App de Gerenciamento de Tarefas

> App mobile Flutter para gerenciamento de tarefas com autenticação e categorias. Projeto acadêmico AC622 (Mobile Programming II) — UNAERP 2024/1.

- **GitHub:** https://github.com/MiguelRibasBerlese/taskflow
- **Co-autor:** Enzo Shimada Daun
- **Última atualização:** Abr 2026
- **Tipo:** Acadêmico

---

## 📐 Stack

| Camada | Tecnologia |
|--------|-----------|
| Framework | Flutter |
| Linguagem | Dart (70%), C++, Swift |
| State Management | Provider |
| Design System | Material Design 3 |
| Auth | Firebase Authentication |
| Banco de dados | Firebase Firestore |
| Plataformas | Android, iOS, Web, Windows, macOS, Linux |

---

## ✅ Requisitos Implementados

| RF | Feature | Peso |
|----|---------|------|
| RF001 | Login com validação | 25% |
| RF002 | Cadastro de usuário | 25% |
| RF003 | Recuperação de senha | 25% |
| RF004 | Tela Sobre | 5% |
| RF005 | CRUD completo de tarefas + categorias | 50% |
| RF006 | AlertDialog e SnackBar | 5% |
| RF007 | ListView de tarefas | 15% |

---

## 🏗️ Arquitetura

- **MVVM** + Repository Pattern
- **Dual-mode auth:** funciona online (Firebase) e offline (in-memory)
- **Reactive UI:** StateFlow para atualizações sem bloqueio
- **ViewBinding** para UI type-safe
