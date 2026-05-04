# ADR-008: Стратегия парсинга JSON-ответа LLM

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

generation-service ожидает от LLM список тест-кейсов в JSON. Не все модели и серверы поддерживают `response_format={"type":"json_object"}`.

## Решение

Двухуровневый подход:

1. **Пробуем JSON mode** — передаём `response_format={"type":"json_object"}` если сервер поддерживает (Ollama 0.3+, vLLM)
2. **Fallback** — парсим ответ вручную:

```python
def parse_llm_response(content: str) -> list:
    try:
        return json.loads(content)
    except json.JSONDecodeError:
        pass
    # JSON в markdown-блоке
    m = re.search(r'```json\s*(.*?)\s*```', content, re.DOTALL)
    if m:
        return json.loads(m.group(1))
    # первый JSON-объект/массив в тексте
    m = re.search(r'[\[{].*[\]}]', content, re.DOTALL)
    if m:
        return json.loads(m.group(0))
    raise LLMParseError(content)
```

При `LLMParseError` — retry (tenacity, до 2 повторных запросов с уточнённым промптом).

## Последствия

- Первая неделя интеграции с каждой новой моделью — проверить, нужен ли fallback
- Промпт всегда явно требует JSON, даже при использовании JSON mode (двойная страховка)
