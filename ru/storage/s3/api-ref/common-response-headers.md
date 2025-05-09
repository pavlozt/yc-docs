---
title: Какие заголовки ответов существуют в S3 API {{ objstorage-full-name }}
description: Из статьи вы узнаете, какие заголовки ответов существуют в S3 API.
---

# Общие заголовки ответов

Помимо [стандартных HTTP-заголовков](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields) Yandex {{ objstorage-name }} использует дополнительные заголовки, которые могут присутствовать в ответах.

В таблице ниже описаны дополнительные заголовки, а также стандартные заголовки, к которым необходимы дополнительные пояснения.

Заголовок | Описание
----- | -----
`ETag` | [MD5-хэш](https://ru.wikipedia.org/wiki/MD5) объекта, если объект загружен единым файлом. Если объект загружен с помощью [составной загрузки](../../concepts/multipart.md), то для вычисления `ETag` необходимо получить MD5-хэш от суммы MD5-хэшей загруженных частей (назовем ее `MD5_sum`) и присоединить к полученной строке количество частей (`N`): `"MD5_sum-N"`.<br/><br/>Не включает метаданные объекта.
`X-Amz-Request-Id` | Уникальный идентификатор запроса.<br/><br/>Может понадобиться при обращении в поддержку {{ yandex-cloud }} при возникновении проблем.

Если запрос был отправлен с CORS-заголовками, то ответ может содержать [заголовки ответа](object/options.md#response-headers) на предварительный запрос `options`.

{% include [the-s3-api-see-also-include](../../../_includes/storage/the-s3-api-see-also-include.md) %}