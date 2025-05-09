---
title: Интеграционный шаг YDBDocument
description: В статье описаны поля для интеграционного шага YDBDocument.
---

# YDBDocument

Взаимодействие с документными таблицами в базе данных [{{ ydb-full-name }}](../../../../../ydb/). Поля `get`, `put`, `update` и `scan` — взаимоисключающие, можно использовать только одно из них.

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`database` | `string` | Да | Нет | Нет | Идентификатор базы данных.
`tableName` | `string` | Да | Нет | Нет | Имя таблицы.
`get` | [YdbDocumentGet](#YdbDocumentGet) | Нет | Нет | Нет | Конфигурация действия `get` — получение записи из таблицы.
`put` | [YdbDocumentPut](#YdbDocumentPut) | Нет | Нет | Нет | Конфигурация действия `put` — добавление записи в таблицу.
`update` | [YdbDocumentUpdate](#YdbDocumentUpdate) | Нет | Нет | Нет | Конфигурация действия `update` — обновление записи в таблице.
`scan` | [YdbDocumentScan](#YdbDocumentScan) | Нет | Нет | Нет | Конфигурация действия `scan` — получение списка записей из таблицы.

## YdbDocumentGet {#YdbDocumentGet}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`key` | `string` | Да | Нет | Да | Значение первичного ключа элемента в базе данных.

## YdbDocumentPut {#YdbDocumentPut}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`item` | `string` | Да | Нет | Да | Элемент данных для добавления в таблицу, сериализованный в JSON-строку.

## YdbDocumentUpdate {#YdbDocumentUpdate}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`key` | `string` | Да | Нет | Да | Значение первичного ключа элемента в базе данных.
`expression` | `string` | Да | Нет | Да | Выражение, описывающее обновления атрибутов элемента в базе данных. Подробнее см. [UpdateExpression](../../../../../ydb/docapi/api-ref/actions/updateItem.md).
`expressionAttributeValues` | `string` | Нет | `""` | Да | Значения атрибутов, используемых в выражении. Подробнее см. [ExpressionAttributeValues](../../../../../ydb/docapi/api-ref/actions/updateItem.md).

## YdbDocumentScan {#YdbDocumentScan}

Имя поля | Тип | Обязательное | Значение по умолчанию | Поддерживается [шаблонизация](../../templating.md) | Описание
--- | --- | --- | --- | --- | ---
`limit` | `string` | Нет | Нет | Да | Максимальное количество элементов в списке.
`exclusive_start_key` | `string` | Нет | Нет | Да | Значение первичного ключа элемента в базе данных, с которого начнется поиск.