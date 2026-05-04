# ADR-005: Чистый httpx вместо atlassian-python-api

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

integration-service должен обращаться к Confluence DC, Jira DC и Xray DC. Существует библиотека `atlassian-python-api`, которая оборачивает эти API.

## Решение

Использовать только `httpx` (async) для всех внешних вызовов в integration-service.

## Обоснование

`atlassian-python-api` — synchronous библиотека. Обёртка через `asyncio.to_thread()` работает, но создаёт скрытый thread overhead и ломает читаемость async кода.

Нам нужен ровно один Confluence-эндпоинт: `GET /wiki/rest/api/content/{id}?expand=body.storage`. Для Xray — bulk-import и tree folders. Написать 30–50 строк httpx проще и надёжнее, чем тащить sync-зависимость.

Плюс: httpx уже используется для межсервисного трафика (generation → integration, admin → generation). Единый стиль, единый timeout-конфиг, единый error handling.

## Последствия

- `atlassian-python-api` отсутствует в зависимостях integration-service
- Все Atlassian-вызовы — явные httpx-клиенты в `app/clients/`
- `tenacity` на все внешние вызовы: 3 попытки, exponential backoff
