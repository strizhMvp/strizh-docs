# strizh-docs

Общая документация проекта СТРИЖ — ADR, API-контракты, глоссарий.

---

## Структура

```
strizh-docs/
├── adr/          # Architectural Decision Records — зафиксированные решения
├── contracts/    # API-контракты между сервисами и фронтом
└── glossary.md   # Общий глоссарий проекта
```

## Как пользоваться

**ADR** — документируем каждое нетривиальное техническое решение. Формат: контекст → решение → последствия. Не редактируем принятые ADR задним числом — если решение меняется, создаём новый ADR со статусом «Заменяет ADR-NNN».

**Contracts** — временные договорённости по API для синхронизации фронта и бэка. Когда backend-сервис поднят, контракт заменяется живой документацией FastAPI (`/docs`, `/openapi.json`).

**Glossary** — если вводишь новый термин в коде или PR — добавляй сюда.

## Участники

| Кто | Репозитории |
|-----|-------------|
| Руслан | strizh-auth-service, strizh-generation-service, strizh-integration-service, strizh-admin-service, strizh-infra |
| Андрей | strizh-frontend |

Правила веток и коммитов: [CONTRIBUTING.md](https://github.com/strizhMvp/.github/blob/main/CONTRIBUTING.md)
