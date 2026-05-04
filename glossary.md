# Глоссарий

| Термин | Определение |
|--------|-------------|
| **Generation** | Один запрос на генерацию тест-кейсов: входная Confluence-страница + шаблон + результат. Хранится в `strizh_generation.generations`. |
| **TestCase** | Один сгенерированный тест. Привязан к Generation. Поля определяются выбранным Template. |
| **Template** | Набор полей, по которым LLM генерирует тест-кейсы. Создаётся Admin-ом, доступен всем User-ам. |
| **Prompt** | Системный / ассистентский / пользовательский текст для LLM. Редактируется Admin-ом в реальном времени. |
| **PAT** | Personal Access Token из Jira/Confluence DC. Хранится зашифрованным (Fernet) в профиле User. |
| **Push** | Отправка тест-кейсов из СТРИЖ в Jira Xray DC через bulk-import. |
| **Test Repository** | Дерево папок в Xray, куда кладутся тест-кейсы. Пользователь выбирает папку перед Push. |
| **SSE** | Server-Sent Events — протокол стриминга токенов LLM от generation-service к фронту. |
| **jti** | JWT ID — уникальный идентификатор токена. При logout помещается в Redis-блэклист. |
| **auth_request** | Механизм nginx: перед проксированием запроса делает суб-запрос к auth-service для валидации JWT. |
| **internal route** | Маршрут `/internal/*` — только для межсервисного трафика внутри Docker-сети, nginx наружу не пропускает. |
| **DC** | Data Center — on-prem редакция Atlassian (Jira DC, Confluence DC). Аутентификация через PAT, не OAuth. |
