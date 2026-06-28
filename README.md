# HS Battlegrounds RU API

Публичный бесплатный JSON API по русской базе карт Hearthstone Battlegrounds.

- Сайт базы: <https://db.kolodahs.ru>
- API base URL: <https://db.kolodahs.ru/api/v1>
- Авторизация: не нужна
- CORS: `Access-Control-Allow-Origin: *`
- Текущая версия API: `1.2.0`
- Формат ответов: `application/json; charset=utf-8`

API подходит для сайтов, ботов, таблиц, модов, внутренних инструментов и клиентских синхронизаторов, которым нужны русские названия, текст, характеристики, картинки и wiki-метаданные карт Полей сражений.

## Что есть в API

- карты Полей сражений: существа и заклинания;
- русские и английские названия;
- `card_id` и `dbf`;
- уровень таверны, тип существа, атака, здоровье;
- флаги текущего пула и дуо;
- публичные URL картинок карты, золотой версии, арта и арта в рамке;
- механики из локальной базы с русскими названиями;
- опциональные wiki-метаданные из `hearthstone.wiki.gg`;
- звуки карты, artist, race, availability, external links и related cards из wiki;
- локализованные `Wiki mechanics` и `Wiki tags`, если для них уже заполнен русский перевод.

На версии `1.2.0` публичный API отдает карты. Герои Полей сражений синхронизируются в базе сайта отдельно и будут документироваться в API после публикации hero endpoints.

## Быстрый старт

Получить первые 10 карт из текущего пула:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?per_page=10&in_pool=1'
```

Найти карту по `card_id`:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG_AT_069'
```

Найти карту по `dbf`:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/by-dbf/92878'
```

Получить карту вместе с wiki-метаданными:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG_AT_069?include=wiki'
```

Получить только wiki-метаданные карты:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG_AT_069/wiki'
```

Поиск по русскому тексту, названию, `card_id`, `dbf` и заметкам:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?q=мурлок&tier=3&creature_type=murloc'
```

Инкрементальная синхронизация после указанной даты:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?updated_since=2026-06-12T00:00:00Z&per_page=200'
```

## Эндпоинты

| Метод | URL | Описание |
|---|---|---|
| `GET` | `/api/v1` | Информация об API, версия и список endpoint-ов |
| `GET` | `/api/v1/meta` | Справочники, уровни таверны и счетчики |
| `GET` | `/api/v1/cards` | Список карт с фильтрами и пагинацией |
| `GET` | `/api/v1/cards?include=wiki` | Список карт с wiki-метаданными |
| `GET` | `/api/v1/cards/{card_id}` | Одна карта по `card_id` |
| `GET` | `/api/v1/cards/{card_id}?include=wiki` | Одна карта с wiki-метаданными |
| `GET` | `/api/v1/cards/{card_id}/wiki` | Только wiki-метаданные карты |
| `GET` | `/api/v1/cards/by-dbf/{dbf}` | Одна карта по `dbf` |
| `GET` | `/api/v1/cards/by-dbf/{dbf}?include=wiki` | Одна карта по `dbf` с wiki-метаданными |
| `GET` | `/api/v1/cards/by-dbf/{dbf}/wiki` | Только wiki-метаданные карты по `dbf` |

Поддерживаемые HTTP-методы: `GET`, `HEAD`, `OPTIONS`.

## Фильтры списка карт

`GET /api/v1/cards`

| Параметр | Тип | Описание |
|---|---:|---|
| `q` | string | Поиск по RU/EN названию, `card_id`, `dbf`, тексту карты |
| `tier` | integer | Уровень таверны `1..7` |
| `card_type` | string | Тип карты: `minion` или `spell` |
| `creature_type` | string | Тип существа: `all`, `undead`, `dragon`, `mech`, `murloc`, `demon`, `quilboar`, `naga`, `pirate`, `beast`, `elemental` |
| `dbf` | integer | Точный поиск по `dbf` |
| `in_pool` | `0`/`1` | Только карты в текущем пуле или вне пула |
| `duos_only` | `0`/`1` | Только дуо-карты или не только дуо |
| `updated_since` | ISO 8601 datetime | Только карты, измененные начиная с указанного времени |
| `include` | string | Сейчас поддерживается значение `wiki`; можно передавать `include=wiki` |
| `page` | integer | Страница, по умолчанию `1` |
| `per_page` | integer | Размер страницы, по умолчанию `50`, максимум `200` |

`updated_since` использует сравнение `updated_at >= updated_since`, чтобы синк по последней секунде не терял карты. На стороне клиента лучше дедуплицировать результат по `card_id`.

## Ответ списка

`GET /api/v1/cards` возвращает объект с `data` и `pagination`:

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "per_page": 50,
    "total": 2352,
    "total_pages": 48,
    "has_next": true,
    "has_prev": false
  }
}
```

## Формат карты

Пример сокращен, реальные URL картинок могут содержать параметр версии `v`.

```json
{
  "id": 470,
  "card_id": "BG_AT_069",
  "dbf": 92878,
  "card_type": {
    "slug": "minion",
    "name_ru": "Существо"
  },
  "name": {
    "ru": "Спарринг-партнер",
    "en": "Sparring Partner"
  },
  "tavern_tier": 2,
  "creature_type": null,
  "attack": 3,
  "health": 2,
  "in_pool": false,
  "duos_only": false,
  "mechanics": [
    {
      "slug": "BATTLECRY",
      "name_ru": "Боевой клич"
    },
    {
      "slug": "TAUNT",
      "name_ru": "Провокация"
    }
  ],
  "text_ru": "Провокация. Боевой клич: выбранное существо получает «Провокацию».",
  "images": {
    "card": "https://db.kolodahs.ru/uploads/cards/BG_AT_069.png",
    "golden": "https://db.kolodahs.ru/uploads/golden/BG_AT_069.png",
    "art": "https://db.kolodahs.ru/uploads/art/BG_AT_069.jpg",
    "framed": "https://db.kolodahs.ru/uploads/framed/BG_AT_069.png"
  },
  "created_at": "2026-06-12 13:32:50",
  "updated_at": "2026-06-28 00:17:05"
}
```

### Основные поля карты

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer | Внутренний ID записи в базе |
| `card_id` | string | Hearthstone card ID, например `BG_AT_069` |
| `dbf` | integer/null | Hearthstone DBF ID |
| `card_type.slug` | string | `minion` или `spell` |
| `card_type.name_ru` | string | Русское название типа карты |
| `name.ru` | string | Русское название |
| `name.en` | string/null | Английское название |
| `tavern_tier` | integer/null | Уровень таверны |
| `creature_type` | object/null | Тип существа, если есть |
| `attack` | integer/null | Атака |
| `health` | integer/null | Здоровье |
| `in_pool` | boolean | Есть ли карта в текущем пуле |
| `duos_only` | boolean | Только для дуо |
| `mechanics` | array | Локальные механики с русскими названиями |
| `text_ru` | string/null | Текст и служебные заметки на русском |
| `images` | object | Публичные URL изображений |
| `created_at` | string | Дата создания записи в формате MySQL UTC |
| `updated_at` | string | Дата последнего изменения записи в формате MySQL UTC |

## Механики

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

Полный актуальный список можно получить через `GET /api/v1/meta`.

## Картинки

В поле `images` лежат абсолютные публичные URL:

| Поле | Что это |
|---|---|
| `card` | обычный рендер карты |
| `golden` | золотая/триплет-версия |
| `art` | очищенный арт карты |
| `framed` | арт в рамке HSReplay, прозрачный PNG |

Картинки могут быть `null`, если конкретный файл еще не загружен или не существует для этой карты.

## Wiki-метаданные

Wiki-данные не добавляются в обычный ответ, чтобы не раздувать JSON. Их нужно запросить явно:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards/BG_AT_069?include=wiki'
curl 'https://db.kolodahs.ru/api/v1/cards/BG_AT_069/wiki'
```

Пример поля `wiki` внутри карты:

```json
{
  "wiki": {
    "status": "ok",
    "source": "hearthstone.wiki.gg",
    "page": {
      "title": "Battlegrounds/Sparring Partner",
      "url": "https://hearthstone.wiki.gg/wiki/Battlegrounds/Sparring_Partner"
    },
    "artist": "Jim Nelson",
    "race": "Human",
    "minion_type": null,
    "wiki_mechanics": ["Battlecry", "Taunt"],
    "wiki_mechanics_localized": [
      {
        "name_en": "Battlecry",
        "name_ru": "Боевой клич"
      },
      {
        "name_en": "Taunt",
        "name_ru": "Провокация"
      }
    ],
    "wiki_tags": ["Targeted", "Taunt-granting"],
    "wiki_tags_localized": [
      {
        "name_en": "Targeted",
        "name_ru": null
      },
      {
        "name_en": "Taunt-granting",
        "name_ru": null
      }
    ],
    "availability": {
      "formats": [],
      "exclusions": [],
      "notes": [],
      "page_entries": []
    },
    "sounds": [
      {
        "heading": "Play",
        "clips": [
          {
            "description": "Practice makes perfect.",
            "file_title": "VO_AT_069_PLAY_01.wav",
            "file_url": "https://hearthstone.wiki.gg/images/VO_AT_069_PLAY_01.wav?ed8661",
            "group": "Play"
          }
        ]
      }
    ],
    "external_links": [
      {
        "label": "HSReplay.net",
        "url": "https://hsreplay.net/cards/92878"
      }
    ],
    "related_cards": [],
    "fetched_at": "2026-06-28 00:27:48",
    "changed_at": "2026-06-28 00:27:48"
  }
}
```

### Поля wiki-блока

| Поле | Описание |
|---|---|
| `status` | `ok`, `missing` или `error` |
| `source` | Источник данных, сейчас `hearthstone.wiki.gg` |
| `page` | Название и URL wiki-страницы |
| `artist` | Художник карты, если найден |
| `race` | Race/character race из wiki |
| `minion_type` | Minion type из wiki, если указан |
| `wiki_mechanics` | Английские Wiki mechanics как массив строк |
| `wiki_mechanics_localized` | Массив объектов `{name_en, name_ru}` |
| `wiki_tags` | Английские Wiki tags как массив строк |
| `wiki_tags_localized` | Массив объектов `{name_en, name_ru}` |
| `availability` | Данные блока Availability |
| `sounds` | Группы звуков и ссылки на audio-файлы |
| `external_links` | Внешние ссылки из wiki |
| `related_cards` | Карты из блока Related with, если найдены |
| `fetched_at` | Когда wiki-данные были получены |
| `changed_at` | Когда wiki-пакет для карты изменился |

`name_ru` в локализованных wiki terms может быть `null`, если перевод еще не заполнен в базе. Английские массивы `wiki_mechanics` и `wiki_tags` сохраняются для обратной совместимости.

Если wiki-данные для карты еще не синхронизированы, поле `wiki` в ответе с `include=wiki` будет `null`. Endpoint `/wiki` для такой карты вернет `404`.

## Справочники и счетчики

`GET /api/v1/meta` возвращает:

| Поле | Описание |
|---|---|
| `totals.cards` | Общее число карт |
| `totals.minions` | Число существ |
| `totals.spells` | Число заклинаний |
| `totals.in_pool` | Карт в текущем пуле |
| `totals.duos_only` | Карт только для дуо |
| `totals.with_framed_image` | Карт с framed image |
| `creature_types` | Справочник типов существ |
| `card_types` | Справочник типов карт |
| `mechanics` | Справочник локальных механик |
| `tavern_tiers` | Уровни таверны |
| `counts_by_tier` | Счетчики по уровню таверны |
| `counts_by_creature_type` | Счетчики по типу существа |

## Рекомендованный клиентский синк

Для полного первичного импорта:

1. Запросите `/api/v1/meta`, чтобы сохранить справочники.
2. Идите по `/api/v1/cards?per_page=200&page=1`, затем `page=2` и дальше до `has_next=false`.
3. Сохраняйте карты по ключу `card_id`; `dbf` удобен для поиска, но `card_id` лучше как стабильный primary key.
4. Если нужны звуки, artist, external links и related cards, используйте `include=wiki`.

Для последующих обновлений:

1. Храните максимальный `updated_at`, который вы обработали.
2. Запрашивайте `/api/v1/cards?updated_since=<last_seen>&per_page=200`.
3. Дедуплицируйте по `card_id`, потому что `updated_since` использует `>=`.
4. Используйте `ETag` или `If-Modified-Since`, чтобы не скачивать неизменившиеся ответы.

## Кэширование

Успешные JSON-ответы API отдают:

| Заголовок | Значение |
|---|---|
| `Cache-Control` | `public, max-age=300, stale-while-revalidate=60` |
| `ETag` | Хэш конкретного JSON-ответа |
| `Last-Modified` | Максимальный `updated_at`/wiki timestamp среди данных ответа |

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
| `404` | `not_found` | Нет такого endpoint-а, карты или wiki-метаданных |
| `405` | `method_not_allowed` | Метод не `GET`/`HEAD`/`OPTIONS` |
| `500` | `internal_error` | Внутренняя ошибка API |

## OpenAPI

Контракт лежит в [`openapi.yaml`](openapi.yaml). README описывает текущую публичную версию API и основные практические сценарии.

## Changelog

История изменений API лежит в [`CHANGELOG.md`](CHANGELOG.md).
