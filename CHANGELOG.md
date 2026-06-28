# Changelog

Все заметные изменения публичного API фиксируются здесь.

## 1.3.0 - 2026-06-28

- Добавлены hero endpoints: `GET /api/v1/heroes`, `GET /api/v1/heroes/{card_id}`, `GET /api/v1/heroes/by-dbf/{dbf}`.
- В ответ героя добавлены броня, броня в дуо, здоровье, artist, race, character, As a hero и описание.
- Добавлены русские данные силы героя и buddy из Blizzard API: `dbf`, название, текст, обычная/золотая картинка и crop image.
- Добавлены hero images: картинка героя и full art.
- Добавлены wiki-данные героя: `availability`, `hero_skins`, `gallery`, `card_changes`, `external_links`, `fetched_at`, `changed_at`.
- В `GET /api/v1` добавлены `heroes_total` и hero endpoints.
- В `GET /api/v1/meta` добавлен счетчик `totals.heroes`.
- README и OpenAPI расширены примерами получения героев, buddy, hero power, art, skins и gallery.

## 1.2.0 - 2026-06-28

- Добавлен параметр `include=wiki` для списка карт, карты по `card_id` и карты по `dbf`.
- Добавлены endpoints `GET /api/v1/cards/{card_id}/wiki` и `GET /api/v1/cards/by-dbf/{dbf}/wiki`.
- В wiki-блок добавлены `artist`, `race`, `minion_type`, `availability`, `sounds`, `external_links`, `related_cards`, `fetched_at` и `changed_at`.
- Добавлены локализованные wiki-поля `wiki_mechanics_localized` и `wiki_tags_localized`; старые массивы `wiki_mechanics` и `wiki_tags` сохранены.
- В объект карты добавлен `card_type` с `slug` и русским названием.
- В `GET /api/v1/meta` добавлены счетчики `minions`, `spells` и справочник `card_types`.
- README расширен подробными примерами, описанием wiki-метаданных, клиентской синхронизации и кэширования.

## 1.1.0 - 2026-06-12

- Добавлен endpoint `GET /api/v1/cards/by-dbf/{dbf}` для Hearthstone-инструментов, которые работают через `dbf`.
- В объект карты добавлено поле `mechanics`: массив механик с `slug` и русским названием.
- В `GET /api/v1/meta` добавлен справочник `mechanics`.
- В `openapi.yaml` добавлены реальные examples для списка карт, одной карты и JSON-ошибки.
- Документация обновлена под новый формат ответа карты.

## 1.0.0 - 2026-06-12

- Опубликован бесплатный публичный API `https://db.kolodahs.ru/api/v1`.
- Добавлены endpoints `GET /api/v1`, `GET /api/v1/meta`, `GET /api/v1/cards`, `GET /api/v1/cards/{card_id}`.
- Добавлены фильтры списка, пагинация, `updated_since`, `Cache-Control`, `ETag` и `Last-Modified`.
- Добавлены публичные URL картинок `card`, `golden`, `art` и `framed`.
