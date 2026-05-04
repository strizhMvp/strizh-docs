# ADR-003: Redis для JWT jti-блэклиста

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

JWT — stateless токены. При logout нужно их инвалидировать до истечения `exp`. Возможные варианты: MongoDB TTL-коллекция или Redis.

## Решение

Redis 7 как хранилище jti-блэклиста.

```
POST /api/auth/logout:
  SET jti:<jti_value> 1 EX <remaining_ttl_seconds>

GET /internal/auth/validate (каждый запрос через nginx):
  EXISTS jti:<jti_value>  →  если 1, возвращаем 401
```

## Обоснование

MongoDB TTL-индекс удаляет устаревшие документы раз в ~60 секунд. В этом окне токен формально инвалидирован пользователем, но продолжает проходить валидацию. Для logout это неприемлемо.

Redis `SET ... EX` + `EXISTS` — атомарно, мгновенно, именно для этого сценария и создавался.

Redis в Phase 2 также используется для кеширования дерева Xray Test Repository и rate limiting.

## Последствия

- Добавлена зависимость Redis 7 в docker-compose (всегда, не в профиле)
- auth-service: зависимость `redis>=5.0.0` (включает `redis.asyncio`)
- Переменная `REDIS_URL` в `.env` auth-service
- При падении Redis — auth недоступен (приемлемо: Redis в alpine, крайне стабилен)
