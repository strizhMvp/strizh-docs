# ADR-009: Логи через API-границу, не прямое чтение БД

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

generation-service пишет логи в `strizh_generation.logs`. admin-service должен отображать их в панели. Вопрос: читать напрямую из MongoDB или через API?

## Решение

generation-service владеет логами и экспонирует `GET /internal/logs` с фильтрами. admin-service вызывает его по HTTP, сам в чужую базу не лезет.

```
GET /internal/logs?type=error|llm|audit&from=ISO&to=ISO&user_id=...&limit=100&offset=0
→ { items: [...], total: int }
```

## Обоснование

Прямое подключение admin-service к `strizh_generation` нарушает data ownership. Если generation-service завтра переедет логи в Loki или Elasticsearch — admin-service сломается. API-граница изолирует это изменение.

## Последствия

- generation-service: эндпоинт `GET /internal/logs` с фильтрами (type, from/to, user_id, pagination)
- admin-service: HTTP-прокси к generation-service, кеширует результаты если нужно
- Дополнительный сетевой вызов при открытии логов в UI (приемлемо, логи не hot path)
