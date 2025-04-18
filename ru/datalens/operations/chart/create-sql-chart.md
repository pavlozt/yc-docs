---
title: Создание QL-чарта
description: Следуя данной инструкции, вы сможете создать QL-чарт.
---

# Создание QL-чарта



Создать QL-чарт можно одним из способов:

* [через главную страницу](#main-page);
* [через подключение](#connection).

{% include [datalens-monitoring-prometheus-access-note](../../../_includes/datalens/datalens-monitoring-prometheus-access-note.md) %}

Для QL-чартов доступны те же [общие настройки](../../concepts/chart/settings.md#common-settings) и [настройки секций](../../concepts/chart/settings.md#section-settings), что и для чартов на основе датасета. Для полей чарта доступны только некоторые [настройки показателей](../../concepts/chart/settings.md#indicator-settings).

На каждом шаге можно [отменить или повторно выполнить](../../concepts/chart/settings.md#undo-redo) внесенные изменения в пределах текущей версии.

## Создание QL-чарта через главную страницу {#main-page}


{% include [datalens-workbooks-collections-note](../../../_includes/datalens/operations/datalens-workbooks-collections-note.md) %}



1. Перейдите на [главную страницу]({{ link-datalens-main }}) {{ datalens-short-name }}.


1. На рабочей панели слева выберите ![image](../../../_assets/console-icons/chart-column.svg) **Чарты**.
1. Нажмите кнопку **Cоздать чарт** → **QL-чарт**.
1. В открывшемся окне выберите **Тип чарта**:

   {% list tabs %}

   - SQL

     1. Нажмите **Выбрать подключение**.
     1. Выберите из списка подключение БД, к которому у вас есть доступ. Убедитесь, что в подключении активирована настройка **Уровень доступа SQL-запросов** → **Разрешить подзапросы в датасетах и запросы из чартов**.
     1. Нажмите **Создать**.
     1. На вкладке **Запрос** укажите запрос, используя SQL-диалект БД, к которой вы обращаетесь.
     1. В левом нижнем углу нажмите кнопку **Запустить**.

     После выполнения запроса появится визуализация на основе ваших данных.

     {% include [datalens-sql-ch-example](../../../_includes/datalens/datalens-sql-ch-example.md) %}

   - {{ prometheus-name }}

     1. Нажмите **Выбрать подключение**.
     1. Выберите из списка подключение {{ prometheus-name }}, к которому у вас есть доступ.
     1. Нажмите **Создать**.
     1. Нажмите **Добавить запрос** и укажите запрос на языке {{ prometheus-name }}.
     1. В левом нижнем углу нажмите кнопку **Запустить**.

     После выполнения запроса появится визуализация на основе ваших данных.

     {% include [datalens-prometheus-ch-example](../../../_includes/datalens/datalens-prometheus-ch-example.md) %}

     QL-чарты по данным {{ prometheus-name }} можно параметризовать так же, как и чарты типа SQL. Также для этого типа чартов предопределены обязательные глобальные параметры, которые создаются автоматически при создании QL-чарта. Вы можете отредактировать значения по умолчанию для этих параметров.

     | Параметр | Тип данных | Описание |
     |---|---|---|
     | from | datetime | Нижняя временная граница. |
     | to | datetime | Верхняя временная граница. |
     | step | number | Шаг по временной шкале в секундах. |

     {% note info %}

     На дашборде вы можете использовать один селектор для даты со специальным именем `interval` вместо двух селекторов `from` и `to`.

     {% endnote %}

   - {{ monitoring-short-name }}

     
     1. Нажмите **Выбрать подключение**.
     1. Выберите из списка подключение {{ monitoring-short-name }}, к которому у вас есть доступ.
     1. Нажмите **Создать**.
     1. Нажмите **Добавить запрос** и укажите запрос на языке {{ monitoring-short-name }}.
     1. В левом нижнем углу нажмите кнопку **Запустить**.


     После выполнения запроса появится визуализация на основе ваших данных.

     {% include [datalens-monitoring-ch-example](../../../_includes/datalens/datalens-monitoring-ch-example.md) %}

     QL-чарты по данным {{ monitoring-short-name }} можно параметризовать так же, как и чарты типа SQL. Также для этого типа чартов предопределены обязательные глобальные параметры, которые создаются автоматически при создании QL-чарта. Вы можете отредактировать значения по умолчанию для этих параметров.

     | Параметр | Тип данных | Описание |
     |---|---|---|
     | from | datetime | Нижняя временная граница. |
     | to | datetime | Верхняя временная граница. |

     {% note info %}

     На дашборде вы можете использовать один селектор для даты со специальным именем `interval` вместо двух селекторов `from` и `to`.

     {% endnote %}

   {% endlist %}

## Создание QL-чарта через подключение {#connection}

1. Перейдите к уже созданному подключению БД.
1. Убедитесь, что в подключении активирована настройка **Уровень доступа SQL-запросов** → **Разрешить подзапросы в датасетах и запросы из чартов**.
1. В правом верхнем углу нажмите **Создать QL-чарт**.
1. На вкладке **Запрос** укажите запрос, используя SQL-диалект БД, к которой вы обращаетесь.
1. В левом нижнем углу нажмите кнопку **Запустить**.

После выполнения запроса появится визуализация на основе ваших данных.


## Добавление параметров селектора {#selector-parameters}

Для [QL-чартов](../../concepts/chart/index.md#sql-charts) в области редактирования чарта на вкладке **Параметры** можно управлять параметрами селектора, а на вкладке **Запрос** указывать переменную в самом запросе в формате `not_var{{variable}}`.

Чтобы добавить параметр:

1. Перейдите на вкладку **Параметры** при создании чарта.
1. Нажмите **Добавить параметр**.
1. Установите тип значения для параметра. Например, `date-interval`.
1. Задайте имя параметру. Например, `interval`.
1. Установите значения по умолчания. Например, `2017-01-01 — 2019-12-31`.

   ![image](../../../_assets/datalens/parameters/date-interval.png =450x167)

   Для параметров типа `date`, `datetime`, `date-interval` и `datetime-interval` значения можно указать несколькими способами:

   * **Точная дата** — чтобы указать точное значение.
   * **Смещение от текущей даты** — чтобы указать относительное значение, которое будет обновляться автоматически.
   
   Для быстрого заполнения значений используйте пресеты.

Чтобы управлять значениями параметра на дашборде, [создайте селектор](../dashboard/add-selector.md) с ручным вводом и в поле **Имя поля или параметра** укажите имя параметра.

### Интервалы {#params-interval}

Параметры типа `date-interval` и `datetime-interval` можно использовать в коде запроса только с постфиксами `_from` и `_to`. Например, для параметра `interval` со значением `2017-01-01 — 2019-12-31` нужно указать:

* `interval_from` — для получения начала интервала (`2017-01-01`);
* `interval_to` — для получения конца интервала (`2019-12-31`).

{% cut "Пример запроса" %}

```sql
SELECT toDate(Date) as datedate, count ('Oreder ID')
FROM samples.SampleLite
WHERE not_var{{interval_from}} < datedate AND datedate < not_var{{interval_to}}
GROUP BY datedate
ORDER BY datedate
```

{% endcut %}

### Подстановка параметра в запрос QL-чарта {#params-in-select}

Значения для параметра из селектора в QL-чарт приходят в виде:

* единичного значения — если выбран один элемент;
* кортежа ([tuple](https://docs.python.org/3/library/stdtypes.html#tuples)) — если выбрано несколько элементов.

Если в тексте запроса для подключений {{ CH }} или {{ PG }} перед параметром указан оператор `in`, то подставляемое значение всегда преобразовывается в кортеж. Для остальных подключений автоматического преобразования в кортеж не происходит. Запрос с оператором `in` отработает корректно, если выбрано одно или несколько значений.

{% cut "Пример запроса с оператором `in`" %}

```sql
SELECT sum (Sales) as Sales, Category
FROM samples.SampleLite
WHERE Category in not_var{{category}} 
GROUP BY Category
ORDER BY Category
```

{% endcut %}

Если в тексте запроса перед параметром указан оператор `=`, запрос отработает корректно, только если будет выбрано одно значение.

{% cut "Пример запроса с оператором `=`" %}

```sql
SELECT sum (Sales) as Sales, Category
FROM samples.SampleLite
WHERE Category = not_var{{category}} 
GROUP BY Category
ORDER BY Category
```

{% endcut %}

### Пустой выбор в селекторе и параметры {#empty-selector}

Если в селекторе не выбрано ни одного значения и для параметра не задано значение по умолчанию, то в запрос передается пустое значение. В [чартах на основе датасета](../../concepts/chart/dataset-based-charts.md) в этом случае выбираются все значения, а в генерации запроса фильтр по соответствующему столбцу пропадает.

Чтобы обеспечить аналогичное поведение в QL-чартах, можно использовать в запросе конструкцию вида:

```sql
AND
CASE
    WHEN LENGTH(not_var{{param}}::VARCHAR)=0 THEN TRUE
    ELSE column IN not_var{{param}}
END
```

#### См. также {#see-also}

* [{#T}](../../concepts/chart/index.md)
* [{#T}](../dashboard/add-chart.md)

{% include [clickhouse-disclaimer](../../../_includes/clickhouse-disclaimer.md) %}