# HS Battlegrounds RU API

Публичный бесплатный JSON API по русской базе карт Hearthstone Battlegrounds.

База: <https://db.kolodahs.ru>  
API base URL: <https://db.kolodahs.ru/api/v1>  
Авторизация: не нужна  
CORS: `Access-Control-Allow-Origin: *`  
Текущая версия API: `1.1.0`

## Эндпоинты

| Метод | URL | Описание |
|---|---|---|
| `GET` | `/api/v1` | Информация об API и список эндпоинтов |
| `GET` | `/api/v1/meta` | Типы существ, уровни таверны и счетчики |
| `GET` | `/api/v1/cards` | Список карт с фильтрами и пагинацией |
| `GET` | `/api/v1/cards/{card_id}` | Одна карта по `card_id` |
| `GET` | `/api/v1/cards/by-dbf/{dbf}` | Одна карта по `dbf` |

## Быстрые примеры

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?per_page=10&in_pool=1'
```

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG34_231'
```

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/by-dbf/95267'
```

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?q=мурлок&tier=3&creature_type=murloc'
```

## Фильтры списка карт

`GET /api/v1/cards`

| Параметр | Тип | Описание |
|---|---:|---|
| `q` | string | Поиск по RU/EN названию, `card_id`, `dbf`, тексту карты |
| `tier` | integer | Уровень таверны `1..7` |
| `creature_type` | string | Тип существа: `all`, `undead`, `dragon`, `mech`, `murloc`, `demon`, `quilboar`, `naga`, `pirate`, `beast`, `elemental` |
| `dbf` | integer | Точный поиск по dbf |
| `in_pool` | `0`/`1` | Только карты в текущем пуле или вне пула |
| `duos_only` | `0`/`1` | Только дуо-карты или не только дуо |
| `updated_since` | ISO 8601 datetime | Только карты, измененные начиная с указанного времени, например `2026-06-12T00:00:00Z` |
| `page` | integer | Страница, по умолчанию `1` |
| `per_page` | integer | Размер страницы, по умолчанию `50`, максимум `200` |

`updated_since` использует сравнение `updated_at >= updated_since`, чтобы синк по последней секунде не терял карты. На стороне клиента лучше дедуплицировать результат по `card_id`.

## Формат карты

```json
{
  "id": 9,
  "card_id": "BG25_011",
  "dbf": 95267,
  "name": {
    "ru": "Неруб-смертороевик",
    "en": "Nerubian Deathswarmer"
  },
  "tavern_tier": 2,
  "creature_type": {
    "slug": "undead",
    "name_ru": "Нежить"
  },
  "attack": 1,
  "health": 4,
  "in_pool": true,
  "duos_only": false,
  "mechanics": [
    {
      "slug": "BATTLECRY",
      "name_ru": "Боевой клич"
    }
  ],
  "text_ru": "Боевой клич: ваша нежить получает +1 к атаке до конца матча (где бы она ни была).\n\nТипы: Нежить\n\nМеханики: BATTLECRY\n\nЗолотая версия dbf: 95268",
  "images": {
    "card": "https://db.kolodahs.ru/uploads/cards/BG25_011.png",
    "golden": "https://db.kolodahs.ru/uploads/golden/BG25_011.png",
    "art": "https://db.kolodahs.ru/uploads/art/BG25_011.jpg",
    "framed": "https://db.kolodahs.ru/uploads/framed/BG25_011.png"
  },
  "created_at": "2026-06-09 14:27:24",
  "updated_at": "2026-06-12 20:16:17"
}
```

### Механики

Поле `mechanics` есть у каждой карты. Если механик нет или они не указаны в источнике, приходит пустой массив.

Примеры `slug`:

| `slug` | `name_ru` |
|---|---|
| `BATTLECRY` | Боевой клич |
| `DEATHRATTLE` | Предсмертный хрип |
| `START_OF_COMBAT` | Начало боя |
| `DIVINE_SHIELD` | Божественный щит |
| `TAUNT` | Провокация |
| `REBORN` | Перерождение |
| `MAGNETIC` | Магнетизм |
| `BACON_SPELLCRAFT_ID` | Чародейство |

### Картинки

В поле `images` лежат абсолютные публичные URL:

| Поле | Что это |
|---|---|
| `card` | обычный рендер карты |
| `golden` | золотая/триплет-версия |
| `art` | очищенный арт карты |
| `framed` | арт в рамке HSReplay, прозрачный PNG |

## Пагинация

Ответ `GET /api/v1/cards`:

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "per_page": 50,
    "total": 946,
    "total_pages": 19,
    "has_next": true,
    "has_prev": false
  }
}
```

## Кэширование

Успешные JSON-ответы API отдают:

| Заголовок | Значение |
|---|---|
| `Cache-Control` | `public, max-age=300, stale-while-revalidate=60` |
| `ETag` | Хэш конкретного JSON-ответа |
| `Last-Modified` | Максимальный `updated_at` среди карт в ответе/выборке |

API поддерживает условные запросы:

```bash
curl -I 'https://db.kolodahs.ru/api/v1/cards?per_page=10'
curl -H 'If-None-Match: "<etag>"' 'https://db.kolodahs.ru/api/v1/cards?per_page=10'
```

Если данные не менялись, сервер вернет `304 Not Modified`.

Картинки в `/uploads/` публичные и отдаются с `ETag`, `Last-Modified` и недельным `Cache-Control`.

## Ошибки

Ошибки возвращаются JSON-объектом:

```json
{
  "error": {
    "code": "not_found",
    "message": "Карта не найдена."
  }
}
```

Частые коды:

| HTTP | `error.code` | Когда |
|---:|---|---|
| `400` | `invalid_parameter` | Неверный фильтр или значение параметра |
| `404` | `not_found` | Нет такого эндпоинта или карты |
| `405` | `method_not_allowed` | Метод не `GET`/`HEAD`/`OPTIONS` |
| `500` | `internal_error` | Внутренняя ошибка API |

## OpenAPI

Полный контракт лежит в [`openapi.yaml`](openapi.yaml).

## Changelog

История изменений API лежит в [`CHANGELOG.md`](CHANGELOG.md).
