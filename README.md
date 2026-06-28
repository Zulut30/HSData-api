# HS Battlegrounds RU API

Публичный бесплатный JSON API по русской базе карт Hearthstone Battlegrounds.

- Сайт базы: <https://db.kolodahs.ru>
- API base URL: <https://db.kolodahs.ru/api/v1>
- Авторизация: не нужна
- CORS: `Access-Control-Allow-Origin: *`
- Текущая версия API: `1.5.0`
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
- card changes и простой список `related_card_ids`;
- wiki-метаданные для tavern spells;
- локализованные `Wiki mechanics` и `Wiki tags`, если для них уже заполнен русский перевод.
- герои Полей сражений;
- броня героя, броня в дуо и здоровье;
- сила героя на русском языке с картинками;
- компаньон/buddy на русском языке с картинками;
- hero skins, gallery, full art, availability и card changes из wiki.
- отдельная категория хрономальных карт Timewarped Tavern;
- для Timewarped-карт: русская основная карта, золотая версия, `Availability`, `Related cards`, `Sounds`, `Gallery`, `Card changes` и `External links`.

На версии `1.5.0` публичный API отдает карты, tavern spells, героев и отдельную категорию Timewarped Tavern. Wiki-ссылка `wiki_page` есть в карточных ответах, а полный wiki-блок для обычных карт по-прежнему подключается через `include=wiki`.

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

Получить список героев:

```bash
curl 'https://db.kolodahs.ru/api/v1/heroes?per_page=20'
```

Получить одного героя вместе с силой героя, buddy, skins, gallery и art:

```bash
curl 'https://db.kolodahs.ru/api/v1/heroes/TB_BaconShop_HERO_16'
```

Получить героя по `dbf`:

```bash
curl 'https://db.kolodahs.ru/api/v1/heroes/by-dbf/57944'
```

Получить хрономальные карты Timewarped Tavern:

```bash
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards?per_page=20'
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards/BG34_Giant_009'
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards/by-dbf/126343'
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
| `GET` | `/api/v1/heroes` | Список героев с пагинацией |
| `GET` | `/api/v1/heroes/{card_id}` | Один герой по `card_id` |
| `GET` | `/api/v1/heroes/by-dbf/{dbf}` | Один герой по `dbf` |
| `GET` | `/api/v1/timewarped-cards` | Список хрономальных карт Timewarped Tavern |
| `GET` | `/api/v1/timewarped-cards/{card_id}` | Одна хрономальная карта по `card_id` |
| `GET` | `/api/v1/timewarped-cards/by-dbf/{dbf}` | Одна хрономальная карта по `dbf` |

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
  "wiki_page": {
    "title": "Battlegrounds/Sparring Partner",
    "url": "https://hearthstone.wiki.gg/wiki/Battlegrounds/Sparring_Partner"
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
| `wiki_page` | object/null | Ссылка на страницу карты на hearthstone.wiki.gg, если wiki-синхронизация нашла страницу |
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
    "related_card_ids": [],
    "card_changes": [
      {
        "heading": "Card changes",
        "entries": [
          {
            "patch": "Patch 27.2.0.183876",
            "patch_url": "https://hearthstone.wiki.gg/wiki/Patch_27.2.0.183876",
            "date": "2023-08-22",
            "items": [
              "Removed from the pool of available minions in Battlegrounds."
            ]
          }
        ]
      }
    ],
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
| `related_card_ids` | Упрощенный список card ID из Related with |
| `card_changes` | Card changes и Bug fixes в виде patch/date/items |
| `fetched_at` | Когда wiki-данные были получены |
| `changed_at` | Когда wiki-пакет для карты изменился |

`name_ru` в локализованных wiki terms может быть `null`, если перевод еще не заполнен в базе. Английские массивы `wiki_mechanics` и `wiki_tags` сохраняются для обратной совместимости.

Если wiki-данные для карты еще не синхронизированы, поле `wiki` в ответе с `include=wiki` будет `null`. Endpoint `/wiki` для такой карты вернет `404`.

### Tavern spells wiki

Таверн-спеллы запрашиваются теми же card endpoints, потому что они лежат в общем списке `/cards` с `card_type.slug = "spell"`:

```bash
curl 'https://db.kolodahs.ru/api/v1/cards?card_type=spell&include=wiki&per_page=10'
curl 'https://db.kolodahs.ru/api/v1/cards/BG28_168/wiki'
```

Для spells wiki-блок отдает те же поля:

- `availability`;
- `wiki_mechanics` и `wiki_mechanics_localized`;
- `wiki_tags` и `wiki_tags_localized`;
- `related_cards`;
- `related_card_ids`;
- `card_changes`;
- `external_links`.

## Хрономальные карты Timewarped Tavern

Timewarped-карты лежат в отдельной категории, чтобы не смешивать их с обычным пулом `/cards`.

```bash
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards?per_page=20'
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards?card_type=minion&tier=3'
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards/BG34_Giant_009'
curl 'https://db.kolodahs.ru/api/v1/timewarped-cards/by-dbf/126343'
```

Параметры списка:

| Параметр | Тип | Описание |
|---|---:|---|
| `q` | string | Поиск по RU/EN названию, тексту, `card_id`, `dbf` |
| `tier` | integer | Уровень таверны `1..7` |
| `card_type` | string | `minion`, `spell` или `hero_power` |
| `dbf` | integer | Точный поиск по DBF |
| `updated_since` | ISO 8601 datetime | Только записи, измененные начиная с указанного времени |
| `page` | integer | Страница, по умолчанию `1` |
| `per_page` | integer | Размер страницы, по умолчанию `50`, максимум `200` |

Пример одной Timewarped-карты:

```json
{
  "card_id": "BG34_Giant_009",
  "dbf": 126343,
  "category": {
    "slug": "timewarped",
    "name_ru": "Хрономальные карты"
  },
  "card_type": {
    "slug": "minion",
    "name_ru": "Существо"
  },
  "name": {
    "ru": "Хрономальный бродячий кот",
    "en": "Timewarped Alleycat"
  },
  "text": {
    "ru": "В конце вашего хода призывает домашнюю кошку с характеристиками этого существа.",
    "en": "At the end of your turn, summon a Tabbycat with this minion's stats."
  },
  "tavern_tier": 3,
  "attack": 7,
  "health": 7,
  "minion_type": "Beast",
  "artist": "Anton Zemskov",
  "images": {
    "card": "https://art.hearthstonejson.com/v1/bgs/latest/ruRU/512x/BG34_Giant_009.png",
    "golden": "https://hearthstone.wiki.gg/images/BG34_Giant_009_G_Battlegrounds.png?..."
  },
  "golden": {
    "card_id": "BG34_Giant_009_G",
    "dbf": 127360,
    "name": {
      "ru": null,
      "en": "Timewarped Alleycat"
    },
    "image": "https://hearthstone.wiki.gg/images/BG34_Giant_009_G_Battlegrounds.png?..."
  },
  "wiki": {
    "page": {
      "title": "Battlegrounds/Timewarped Alleycat",
      "url": "https://hearthstone.wiki.gg/wiki/Battlegrounds/Timewarped_Alleycat"
    },
    "availability": {},
    "related_cards": [],
    "related_card_ids": [],
    "sounds": [],
    "gallery": [],
    "card_changes": [],
    "external_links": []
  }
}
```

В `wiki` для Timewarped-карт сразу возвращаются `Availability`, `Related cards`, `Sounds`, `Gallery`, `Card changes`, `External links`, `Wiki mechanics`, `Wiki tags`, `full_tags`, `fetched_at` и `changed_at`. Основная картинка строится как русский Battlegrounds-рендер, золотая версия берется с wiki, когда отдельная golden-страница есть.

## Герои

Герои доступны отдельно от карт:

```bash
curl 'https://db.kolodahs.ru/api/v1/heroes?per_page=20'
curl 'https://db.kolodahs.ru/api/v1/heroes/TB_BaconShop_HERO_16'
curl 'https://db.kolodahs.ru/api/v1/heroes/by-dbf/57944'
```

### Фильтры списка героев

`GET /api/v1/heroes`

| Параметр | Тип | Описание |
|---|---:|---|
| `q` | string | Поиск по RU/EN имени героя, `card_id`, `dbf`, силе героя и buddy |
| `dbf` | integer | Точный поиск по `dbf` героя |
| `updated_since` | ISO 8601 datetime | Только герои, измененные начиная с указанного времени |
| `page` | integer | Страница, по умолчанию `1` |
| `per_page` | integer | Размер страницы, по умолчанию `50`, максимум `200` |

Список возвращает только полностью синхронизированных героев со статусом `ok`. Если фоновая синхронизация еще идет, `pagination.total` будет расти.

### Формат героя

Пример сокращен, но показывает основные поля: броню, силу героя, buddy, картинки, skins, gallery и card changes.

```json
{
  "card_id": "TB_BaconShop_HERO_16",
  "dbf": 57944,
  "hero_id": 57944,
  "name": {
    "ru": "А.Ф.Ка",
    "en": "A. F. Kay"
  },
  "health": 30,
  "armor": {
    "normal": 15,
    "duos": 13,
    "text": "15"
  },
  "artist": "Adam Byrne",
  "race": "Human",
  "character": {
    "name": "A. F. Kay",
    "as_hero": "A. F. Kay is a hero that the player can pick in Battlegrounds.",
    "description": null
  },
  "images": {
    "hero": "https://hearthstone.wiki.gg/images/TB_BaconShop_HERO_16.png?40be52",
    "full_art": "https://hearthstone.wiki.gg/images/A._F._Kay_%28boss%29_full.jpg?460e37"
  },
  "hero_power": {
    "dbf": 59891,
    "card": {
      "dbf": 59891,
      "name": "Прокрастинация",
      "text": "Вы пропускаете первые два хода и <b>раскапываете</b> существ из таверны 3-го и 4-го уровня.",
      "image": "https://d15f34w2p8l1cc.cloudfront.net/hearthstone/16033108ddf25769296cd3975e458cea3e0a3ceff7e260168eefb78ac8cd472b.png",
      "image_gold": "https://d15f34w2p8l1cc.cloudfront.net/hearthstone/bf7274a72f605d9166d76e07d38aa632ede90e6b27b833c477a19a7b9572ee4a.png",
      "crop_image": "https://d15f34w2p8l1cc.cloudfront.net/hearthstone/fb260fc1e38428e372e1b7d8d29fb6f68ed0cb04e571e90662c20460bf73730d.png",
      "card_type_id": 10
    }
  },
  "buddy": {
    "dbf": 77774,
    "card": {
      "dbf": 77774,
      "name": "Торговец закусками",
      "text": "В конце вашего хода существо 3-го уровня получает характеристики этого существа.",
      "image": "https://d15f34w2p8l1cc.cloudfront.net/hearthstone/adae7ac0b3d21dc2aa40eb5a8d2c4f72f708d2e44de4d9d25feba637d0a74734.png",
      "crop_image": "https://d15f34w2p8l1cc.cloudfront.net/hearthstone/bbb69a6184a24f3e7166fad467f0e716e1fbe06d2329d3e25bb76160dda85fea.png",
      "parent_id": 57944,
      "card_type_id": 4
    }
  },
  "wiki": {
    "status": "ok",
    "page": {
      "title": "Battlegrounds/A. F. Kay",
      "url": "https://hearthstone.wiki.gg/wiki/Battlegrounds/A._F._Kay"
    },
    "availability": {
      "formats": [],
      "exclusions": [],
      "notes": [],
      "page_entries": []
    },
    "hero_skins": [
      {
        "heading": "Hero skins",
        "cards": [
          {
            "card_id": "TB_BaconShop_HERO_16_SKIN_A",
            "title": "Battlegrounds/Sunlounger A. F. Kay",
            "url": "https://hearthstone.wiki.gg/wiki/Battlegrounds/Sunlounger_A._F._Kay",
            "image_url": "https://hearthstone.wiki.gg/images/thumb/TB_BaconShop_HERO_16_SKIN_A.png/235px-TB_BaconShop_HERO_16_SKIN_A.png?1e36b8"
          }
        ]
      }
    ],
    "gallery": [
      {
        "caption": "A. F. Kay, full art",
        "file_url": "https://hearthstone.wiki.gg/images/A._F._Kay_%28boss%29_full.jpg?460e37",
        "thumb_url": "https://hearthstone.wiki.gg/images/thumb/A._F._Kay_%28boss%29_full.jpg/281px-A._F._Kay_%28boss%29_full.jpg?460e37"
      }
    ],
    "card_changes": [],
    "external_links": [],
    "fetched_at": "2026-06-28 00:26:30",
    "changed_at": "2026-06-28 00:26:30",
    "error": null
  },
  "created_at": "2026-06-28 00:26:30",
  "updated_at": "2026-06-28 00:26:30"
}
```

### Поля героя

| Поле | Описание |
|---|---|
| `card_id` | Hearthstone card ID героя |
| `dbf` | DBF героя |
| `hero_id` | Hero ID из источника, если найден |
| `name.ru` / `name.en` | Русское и английское имя героя |
| `health` | Здоровье героя |
| `armor.normal` | Обычная броня |
| `armor.duos` | Броня в дуо |
| `artist` | Художник героя |
| `race` | Race/character race из wiki |
| `character` | Character, As a hero и описание |
| `images.hero` | Картинка героя |
| `images.full_art` | Полный арт героя |
| `hero_power.dbf` | DBF силы героя |
| `hero_power.card` | Русская карта силы героя из Blizzard API |
| `buddy.dbf` | DBF компаньона |
| `buddy.card` | Русская карта buddy из Blizzard API |
| `wiki.availability` | Availability из wiki |
| `wiki.hero_skins` | Скины героя, включая `card_id`, `url`, `image_url` |
| `wiki.gallery` | Gallery/full art из wiki |
| `wiki.card_changes` | История изменений карты/героя |
| `wiki.external_links` | Внешние ссылки |
| `fetched_at`, `changed_at`, `updated_at` | Времена синхронизации и обновления |

## Справочники и счетчики

`GET /api/v1/meta` возвращает:

| Поле | Описание |
|---|---|
| `totals.cards` | Общее число карт |
| `totals.minions` | Число существ |
| `totals.spells` | Число заклинаний |
| `totals.heroes` | Число синхронизированных героев |
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
5. Для Timewarped Tavern синхронизируйте отдельный ресурс `/api/v1/timewarped-cards`.

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
