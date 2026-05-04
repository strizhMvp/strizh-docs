# ADR-010: nginx как API Gateway с auth_request

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

4 сервиса на разных портах. Фронт должен ходить в единую точку. Авторизация должна применяться централизованно, без дублирования JWT-валидации в каждом сервисе.

## Решение

nginx на порту 80 как единственная точка входа.

- Роутинг по URL-префиксу: `/api/auth/` → auth:8001, `/api/generate/` → generation:8002 и т.д.
- `auth_request /_auth` на всех защищённых маршрутах — nginx делает суб-запрос к `auth-service:8001/internal/auth/validate`
- auth-service возвращает `X-User-ID` и `X-User-Role` в заголовках ответа
- nginx пробрасывает эти заголовки в upstream через `auth_request_set` + `proxy_set_header`
- `/api/auth/` — публичный маршрут без auth_request (login/logout)

`/internal/*` маршруты **не проксируются** nginx наружу — только прямой трафик внутри Docker-сети `strizh`. В K8s заменяется NetworkPolicy.

## Последствия

- Каждый backend-сервис доверяет заголовкам `X-User-ID` и `X-User-Role` — не валидирует JWT повторно
- Если nginx упал — все сервисы недоступны снаружи (приемлемо, nginx крайне стабилен)
- SSE (`/api/generate/`): `proxy_buffering off`, `proxy_cache off`, `proxy_read_timeout 300s`
