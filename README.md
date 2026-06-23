<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <img src="assets/logo.svg" width="420" alt="Prowl">
  </picture>
</p>

<p align="center"><b>English</b> · <a href="README.ru.md">Русский</a></p>

<p align="center">Detection rules and live verifiers for <a href="https://github.com/Lercas/prowl">Prowl</a>.</p>

---

This repository holds Prowl's secret-detection library, kept outside the scanner so it can be updated
on its own cadence:

- **`rules/`:** 159 detection templates (one YAML per provider), grouped by category (see the table below).
- **`verifiers/`:** 79 data-driven live-verification rules: an HTTP request to a provider's read-only
  identity endpoint plus conditional matchers, with a pluggable signer (AWS SigV4, bearer, basic).

### Template distribution

| Category | Templates | Examples |
|---|--:|---|
| Messaging & notifications | 38 | Slack, Telegram, Discord, Datadog |
| SaaS & APIs | 29 | Airtable, Algolia, Asana, Notion |
| Cloud | 28 | AWS, GCP, Azure, Alibaba |
| AI & ML | 19 | OpenAI, Anthropic, Cohere |
| Version control | 18 | GitHub, GitLab, Bitbucket |
| Databases | 14 | PostgreSQL, MongoDB, Redis, ClickHouse |
| Payments | 13 | Stripe, Coinbase, Adyen |
| **Total** | **159** | |

## Install into Prowl

Prowl pulls from this repository by default:

```sh
prowl rules update           # clone + validate + install rules into ~/.prowl/rules
prowl verifiers update       # same for verifiers into ~/.prowl/verifiers
```

Point at a fork or a local checkout with `--source`:

```sh
prowl rules update --source ./prowl-templates          # a local clone
prowl rules update --source https://github.com/you/prowl-templates.git
```

After installing, `prowl scan .` loads the rules automatically and `--verify` uses the verifiers, no
flags needed. Every update **validates before installing** and records a version manifest, so a bad
push can't break a scan.

## Authoring

A rule is one YAML file with `word` + `regex` + `entropy` matchers combined by AND/OR; a verifier is
an HTTP request plus response matchers with `{{secret}}` interpolation. Full schemas:

- [`rules/SCHEMA.md`](rules/SCHEMA.md) · generate one with [`rules/PROMPT.md`](rules/PROMPT.md)
- [`verifiers/SCHEMA.md`](verifiers/SCHEMA.md) · generate one with [`verifiers/PROMPT.md`](verifiers/PROMPT.md)

```sh
prowl rules validate rules/          # lint: YAML, RE2 compile, required fields, duplicate ids
prowl rules show <rule-id>           # inspect a rule: matchers, regex, severity, reference
prowl rules test '<sample string>'   # check which rules fire on a sample (author/debug loop)
prowl verifiers validate verifiers/  # parse + regex-compile check
```

Existing **gitleaks** (`.toml`) and **trufflehog** (`.yaml`) rulesets also drop into Prowl unchanged
via `prowl scan . --rules gitleaks.toml` (see the main repo).

## License

**PolyForm Noncommercial License 1.0.0**: noncommercial use only, not for use in commercial products. See [LICENSE](LICENSE).
