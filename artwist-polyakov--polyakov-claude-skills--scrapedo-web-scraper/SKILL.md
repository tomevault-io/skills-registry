---
name: scrapedo-web-scraper
description: | Use when this capability is needed.
metadata:
  author: artwist-polyakov
---

# Scrape.do Web Scraper

Скрапинг веб-страниц через Scrape.do API. Используй когда обычный fetch не работает (блокировка, JavaScript).

## Использование

```bash
# Получить текст страницы
python scripts/scrape.py https://example.com

# Получить HTML
python scripts/scrape.py --html https://example.com
```

## Из Python

```python
from scripts.scrape import fetch_via_scrapedo

result = fetch_via_scrapedo('https://example.com')
if result['success']:
    print(result['content'])  # текст
    # result['html'] — оригинальный HTML
else:
    print(result['content'])  # описание ошибки
```

## Результат

- **Успех**: текст страницы (или HTML с `--html`)
- **Ошибка**: понятное сообщение (нет токена / лимит / недоступно)

Если вернулась ошибка — страница недоступна через этот метод.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artwist-polyakov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
