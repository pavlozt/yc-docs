---
title: Контроль данных в {{ sd-full-name }}
description: Контроль данных ({{ dspm-name }}) — это один из модулей сервиса {{ sd-name }}, помогающий обнаруживать сохраненную в бакетах {{ objstorage-full-name }} чувствительную информацию и оценивать влияние такой информации на уровень безопасности и соответствие требованиям нормативных актов и отраслевых стандартов.
---

# Модуль контроля данных ({{ dspm-name }})

[Модуль контроля данных]({{ link-sd-main }}dspm/) или {{ dspm-name }} (Data Security Posture Management) — это инструмент, помогающий оперативно обнаруживать чувствительную информацию, сохраненную в [бакетах](../../storage/concepts/bucket.md) {{ objstorage-full-name }}, с тем чтобы своевременно принимать необходимые меры для ее защиты от несанкционированного доступа или утечки (настраивать [политики доступа](../../storage/concepts/policy.md), обезличивать данные и т.п.)

При наличии соответствующей интеграции {{ dspm-name }} может работать в режиме предотвращения утечек, блокируя передачу или сохранение конфиденциальной информации.

## Сканирование на наличие чувствительной информации {#scanning}

{{ dspm-name }} находит чувствительную информацию в бакетах с помощью сканирования _источников данных_.

Прежде чем начать сканирование, необходимо выбрать источник данных и задать _категории данных_ для поиска. 

### Источник данных {#data-source}

Источник данных содержит информацию о бакете, в котором будет выполняться сканирование, и дополнительные настройки.

Например, для источника данных вы можете задать область сканирования — `Все файлы` или `Файлы по шаблону`. Выбрав вариант `Файлы по шаблону`, вы можете в форме [регулярных выражений](https://ru.wikipedia.org/wiki/Регулярные_выражения) указать условия, которым должны или не должны соответствовать имена файлов, подлежащих сканированию.

Для одного бакета вы можете создать несколько источников данных с разными настройками области сканирования.

### Категории данных {#data-categories}

Создавая новое сканирование, вы можете указать категорию данных, которые необходимо искать. Вы можете выбрать как все доступные категории одновременно, так и отдельно любую из них.

Доступные категории данных для сканирования:

* `Персональные данные` — ФИО, номера телефонов, адреса электронной почты, номера документов, IP-адреса и т.п.
* `Секреты` — облачные ключи доступа, пароли, токены, SSH-ключи и т.п.

Для того чтобы создавать источники данных, создавать и запускать сканирования, а также просматривать результаты сканирования, пользователю должны быть назначены соответствующие [роли](../security/index.md).

#### См. также {#see-also}

* [{#T}](../operations/dspm/create-data-source.md)
* [{#T}](../operations/dspm/create-scan.md)
