# ADR-006: Fernet для шифрования PAT, одно поле с fallback

**Статус:** Принято  
**Дата:** 2026-05-04

## Контекст

Jira PAT пользователя нужно хранить в базе и передавать в integration-service при каждом запросе. PAT — секрет с правами на запись в Jira/Xray.

Также: в большинстве on-prem инсталляций Jira DC и Confluence DC — на одном домене, один PAT работает для обоих.

## Решение

**Шифрование:** Fernet (AES-128-CBC + HMAC-SHA256) из пакета `cryptography`. Ключ хранится в `ENCRYPTION_KEY` env-переменной, никогда в коде.

**Модель User:** одно поле `jira_pat_encrypted`. В integration-service: `pat = user.confluence_pat_encrypted or user.jira_pat_encrypted`.

Опциональное поле `confluence_pat_encrypted: Optional[str] = None` — для раздельных инсталляций Atlassian.

## Обоснование

Fernet — простое, проверенное симметричное шифрование. API: `Fernet(key).encrypt(data)` / `.decrypt(data)`. Для MVP достаточно. Vault/AWS KMS — оверкилл без DevOps-команды рядом.

Одно поле с fallback решает 95% инсталляций (общий домен) без усложнения UX. Два поля в профиле — только когда реально нужно.

## Последствия

- `ENCRYPTION_KEY` обязателен в `.env` auth-service (ротация ключа = перешифрование всех PAT)
- integration-service получает расшифрованный PAT от auth-service в заголовке запроса, сам ключи не хранит
- При компрометации `ENCRYPTION_KEY` — все PAT скомпрометированы
