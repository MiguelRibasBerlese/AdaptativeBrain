---
type: conhecimento
status: active
projeto: taskflow
tags: [padrão, flutter, dart, firebase, mobile, provider]
created: 2026-05-20
---

# 📐 Padrão: Flutter + Firebase + Provider

> Stack mobile cross-platform usado no TaskFlow (AC622 — UNAERP).

---

## Stack

```
Flutter (Dart)
├── Provider              # state management
├── Material Design 3     # design system
├── Firebase Auth         # autenticação
├── Firebase Firestore    # banco de dados cloud
├── Kotlin Coroutines     # operações async
└── StateFlow / LiveData  # reactive UI
```

---

## Arquitetura MVVM + Repository Pattern

```
View (Widget)
  └── ViewModel (Provider)
        └── Repository
              ├── FirebaseRepository  (online)
              └── LocalRepository     (offline/in-memory)
```

O Repository abstrai a fonte de dados — permite dual-mode (online/offline) sem mudar a View.

---

## Dual-Mode Auth

```dart
// Detecta modo e usa repositório correto
final repo = isOnline 
    ? FirebaseRepository() 
    : LocalRepository();
```

---

## Estrutura de Pastas Flutter

```
lib/
  models/         # entidades de dados
  viewmodels/     # lógica de negócio (ChangeNotifier)
  views/          # widgets/telas
  repositories/   # abstração de dados
  services/       # Firebase, auth
  widgets/        # componentes reutilizáveis
```

---

## Quando Usar

- Apps mobile cross-platform (Android + iOS + Web)
- Quando precisa funcionar offline
- Projetos acadêmicos ou MVPs rápidos

## Projetos que Usam

- [[../../01-Projetos/taskflow/README|TaskFlow]]
