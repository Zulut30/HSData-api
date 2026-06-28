# Changelog

Все заметные изменения публичного API фиксируются здесь.

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
