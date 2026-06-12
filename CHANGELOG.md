# Changelog

Все заметные изменения публичного API фиксируются здесь.

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
