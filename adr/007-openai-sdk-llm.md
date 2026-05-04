# ADR-007: openai SDK для LLM-клиента

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

generation-service должен вызывать LLM. Целевая модель — Qwen 397B на отдельном железе. На этапе разработки — локальная Qwen через Ollama или vLLM. Оба варианта поднимают OpenAI-совместимый API.

## Решение

`openai` Python SDK с параметром `base_url` указывающим на локальный или удалённый сервер.

```python
client = AsyncOpenAI(base_url=settings.LLM_BASE_URL, api_key=settings.LLM_API_KEY)
```

## Обоснование

openai SDK работает с любым OpenAI-совместимым endpoint. Берёт на себя обработку SSE-чанков, retry, заголовков. Писать raw httpx для стриминга — избыточно.

Переключение между локальным Ollama и продовым Qwen 397B — только смена `LLM_BASE_URL` в env.

## Последствия

- Зависимость от формата OpenAI API (но он де-факто стандарт)
- JSON mode (`response_format={"type":"json_object"}`) поддерживается не всеми моделями — см. ADR-008
