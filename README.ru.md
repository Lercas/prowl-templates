<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo.svg" width="420" alt="Prowl">
  </picture>
</p>

<p align="center"><a href="README.md">English</a> · <b>Русский</b></p>

<p align="center">Правила детектирования и живые верификаторы для <a href="https://github.com/Lercas/prowl">Prowl</a>.</p>

---

В этом репозитории лежит библиотека детектирования секретов Prowl, вынесенная за пределы сканера,
чтобы её можно было обновлять в собственном ритме:

- **`rules/`:** 159 шаблонов детектирования (по одному YAML на провайдера), сгруппированы по категориям (см. таблицу ниже).
- **`verifiers/`:** 79 правил живой верификации на основе данных: HTTP-запрос к read-only эндпоинту
  идентификации провайдера плюс условные матчеры, с подключаемой системой подписи (AWS SigV4, bearer,
  basic).

### Распределение шаблонов

| Категория | Шаблонов | Примеры |
|---|--:|---|
| Мессенджеры и уведомления | 38 | Slack, Telegram, Discord, Datadog |
| SaaS и API | 29 | Airtable, Algolia, Asana, Notion |
| Облака | 28 | AWS, GCP, Azure, Alibaba |
| AI и ML | 19 | OpenAI, Anthropic, Cohere |
| Контроль версий | 18 | GitHub, GitLab, Bitbucket |
| Базы данных | 14 | PostgreSQL, MongoDB, Redis, ClickHouse |
| Платежи | 13 | Stripe, Coinbase, Adyen |
| **Итого** | **159** | |

## Установка в Prowl

Prowl по умолчанию берёт данные из этого репозитория:

```sh
prowl rules update           # clone + validate + install rules into ~/.prowl/rules
prowl verifiers update       # same for verifiers into ~/.prowl/verifiers
```

Укажите на форк или локальную копию через `--source`:

```sh
prowl rules update --source ./prowl-templates          # a local clone
prowl rules update --source https://github.com/you/prowl-templates.git
```

После установки `prowl scan .` загружает правила автоматически, а `--verify` использует верификаторы -
никаких флагов не нужно. Каждое обновление **проверяет перед установкой** и записывает манифест
версий, так что неудачный пуш не сломает сканирование.

## Создание правил

Правило - это один YAML-файл с матчерами `word` + `regex` + `entropy`, объединёнными через AND/OR;
верификатор - это HTTP-запрос плюс матчеры на ответ с интерполяцией `{{secret}}`. Полные схемы:

- [`rules/SCHEMA.md`](rules/SCHEMA.md) · сгенерируйте с помощью [`rules/PROMPT.md`](rules/PROMPT.md)
- [`verifiers/SCHEMA.md`](verifiers/SCHEMA.md) · сгенерируйте с помощью [`verifiers/PROMPT.md`](verifiers/PROMPT.md)

```sh
prowl rules validate rules/          # lint: YAML, RE2 compile, required fields, duplicate ids
prowl verifiers validate verifiers/  # parse + regex-compile check
```

Существующие наборы правил **gitleaks** (`.toml`) и **trufflehog** (`.yaml`) тоже подключаются в Prowl
без изменений через `prowl scan . --rules gitleaks.toml` (см. основной репозиторий).

## Лицензия

**PolyForm Noncommercial License 1.0.0**: только некоммерческое использование, не для использования в коммерческих продуктах. См. [LICENSE](LICENSE).
