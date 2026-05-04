# API Overview

Временный контракт для синхронизации фронта и бэка. Когда сервис поднят — смотри живую документацию на `http://localhost:<port>/docs`.

Все запросы идут через nginx (`http://localhost:80`). Авторизация — Bearer-токен в заголовке `Authorization`.

---

## Auth — `/api/auth/`

| Метод | Путь | Описание | Auth |
|-------|------|----------|------|
| POST | `/api/auth/login` | Логин, возвращает JWT | — |
| POST | `/api/auth/logout` | Инвалидировать токен | ✓ |
| GET | `/api/auth/me` | Текущий пользователь | ✓ |

### POST /api/auth/login
```json
// request
{ "username": "string", "password": "string" }

// response 200
{ "access_token": "string", "token_type": "bearer" }
```

---

## Profile — `/api/profile/`

| Метод | Путь | Описание | Auth |
|-------|------|----------|------|
| GET | `/api/profile/` | Профиль пользователя | ✓ |
| PATCH | `/api/profile/pat` | Обновить Jira PAT | ✓ |
| PATCH | `/api/profile/password` | Сменить пароль | ✓ |

---

## Generate — `/api/generate/`

| Метод | Путь | Описание | Auth |
|-------|------|----------|------|
| POST | `/api/generate` | Запустить генерацию (SSE) | ✓ |
| GET | `/api/generate/{id}` | Статус генерации | ✓ |
| GET | `/api/generate/{id}/test-cases` | Список тест-кейсов | ✓ |
| PATCH | `/api/generate/test-cases/{id}` | Редактировать поле | ✓ |
| DELETE | `/api/generate/test-cases/{id}` | Удалить тест-кейс | ✓ |

### POST /api/generate — SSE Stream
```
// request
{ "confluence_url": "string", "template_id": "string" }

// SSE events:
data: {"type": "status", "message": "Fetching Confluence page..."}
data: {"type": "token", "content": "..."}
data: {"type": "done", "generation_id": "string", "test_cases": [...]}
```

---

## History — `/api/history/`

| Метод | Путь | Описание | Auth |
|-------|------|----------|------|
| GET | `/api/history/` | История генераций (свои) | ✓ |
| GET | `/api/history/{id}` | Детали генерации | ✓ |

---

## Confluence / Xray — `/api/confluence/`, `/api/xray/`

| Метод | Путь | Описание | Auth |
|-------|------|----------|------|
| POST | `/api/confluence/validate` | Превью страницы по URL | ✓ |
| GET | `/api/xray/folders` | Дерево Test Repository | ✓ |
| POST | `/api/xray/push` | Запушить тест-кейсы | ✓ |

### POST /api/xray/push
```json
// request
{ "test_case_ids": ["string"], "folder_id": "string" }

// response
{ "success": [{"id": "string", "xray_url": "string"}], "failed": [{"id": "string", "error": "string"}] }
```

---

## Admin — `/api/admin/` (только роль Admin)

| Метод | Путь | Описание |
|-------|------|----------|
| GET/POST | `/api/admin/templates/` | CRUD шаблонов |
| GET/PATCH/DELETE | `/api/admin/templates/{id}` | Один шаблон |
| GET | `/api/admin/templates/available` | Шаблоны для User |
| GET/PATCH | `/api/admin/prompts/{type}` | Промпты (system/assistant/user) |
| GET/PATCH | `/api/admin/settings/{key}` | Настройки (temperature, max_tokens) |
| GET | `/api/admin/logs/errors` | Логи ошибок |
| GET | `/api/admin/logs/llm` | Логи LLM-взаимодействий |
| GET | `/api/admin/logs/audit` | Аудит-логи |
| GET/POST | `/api/admin/users/` | Управление пользователями |
| PATCH/DELETE | `/api/admin/users/{id}` | Один пользователь |
