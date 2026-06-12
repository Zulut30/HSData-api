# HS Battlegrounds RU API

Публичный бесплатный JSON API по русской базе карт Hearthstone Battlegrounds.

База: <https://db.kolodahs.ru>  
API base URL: <https://db.kolodahs.ru/api/v1>  
Авторизация: не нужна  
CORS: `Access-Control-Allow-Origin: *`

## Эндпоинты

| Метод | URL | Описание |
|---|---|---|
| `GET` | `/api/v1` | Информация об API и список эндпоинтов |
| `GET` | `/api/v1/meta` | Типы существ, уровни таверны и счетчики |
| `GET` | `/api/v1/cards` | Список карт с фильтрами и пагинацией |
| `GET` | `/api/v1/cards/{card_id}` | Одна карта по `card_id` |

## Быстрые примеры

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?per_page=10&in_pool=1'
```

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG34_231'
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
  "id": 10,
  "card_id": "BG34_231",
  "dbf": 126802,
  "name": {
    "ru": "Старая душа",
    "en": "Old Soul"
  },
  "tavern_tier": 2,
  "creature_type": {
    "slug": "undead",
    "name_ru": "Нежить"
  },
  "attack": 3,
  "health": 4,
  "in_pool": true,
  "duos_only": false,
  "text_ru": "Пока находится в вашей руке: ...",
  "images": {
    "card": "https://db.kolodahs.ru/uploads/cards/BG34_231.png",
    "golden": "https://db.kolodahs.ru/uploads/golden/BG34_231.png",
    "art": "https://db.kolodahs.ru/uploads/art/BG34_231.jpg",
    "framed": "https://db.kolodahs.ru/uploads/framed/BG34_231.png"
  },
  "created_at": "2026-06-09 14:29:32",
  "updated_at": "2026-06-12 20:16:18"
}
```

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
| `405` | `method_not_allowed` | Метод не `GET`/`OPTIONS` |
| `500` | `internal_error` | Внутренняя ошибка API |

## OpenAPI

Полный контракт лежит в [`openapi.yaml`](openapi.yaml).
