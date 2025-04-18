# Полнотекстовый поиск в REST API

Полнотекстовый поиск позволяет находить диалоги, в которых присутствует заданное слово или фраза. Запрос возвращает идентификаторы подходящих диалогов. Подробнее о том, как получить информацию о диалоге по его идентификатору, см. в [инструкции](rest-get-dialog-data.md).

Чтобы уточнить результаты поиска, вы можете использовать [фильтрацию по параметрам](rest-search-filters.md) вместе с полнотекстовым поиском. В таком случае в ответе вернутся только идентификаторы запросов, которые удовлетворяют и критериям полнотекстового поиска, и дополнительным фильтрам.

## Перед началом работы {#before-you-begin}

{% include [before-you-begin](../../../_includes/speechsense/data/rest-search-before-you-begin.md) %}

## Полнотекстовый поиск {#full-text-search}

1. Создайте файл `search.json`, укажите в нем нужные [идентификаторы и параметры](#full-text-search-ref) для полнотекстового поиска: 

    ```json
    {
      "organizationId": "<идентификатор_организации>",
      "spaceId": "<идентификатор_пространства>",
      "connectionId": "<идентификатор_подключения>",
      "projectId": "<идентификатор_проекта>",
      "query": {
        "text": "<поисковый_запрос>",
        "channelNumber": "<номер_канала>"
      },
      "sortData": {
        "fields": [{  
          "field": "<характеристика_диалога,_по_которой_производится_поиск>",
          "order": "<порядок_сортировки:_по_возрастанию_или_убыванию>",
          "position": "<приоритет_поля_сортировки>"
        }]
      },
      "pageSize": "<количество_документов_на_странице>",
      "pageToken": "<токен_следующей_страницы_с_результатами_поиска>"
    }
    ```

    Где: 

    {% include [id-parameters](../../../_includes/speechsense/data/api-id-parameters.md) %}

    * `query` — тело запроса полнотекстового поиска. Поддерживает следующие параметры: 

      * `text` — текст поискового запроса. Вы можете задать слово или фразу. {{ speechsense-name }} выполнит поиск заданной строки по текстовой расшифровке аудиозаписи или тексту чата. 
      * `channelNumber` — номер канала. Если параметр указан, поиск производится только по текстовой расшифровке аудиозаписи или тексту чата для указанного канала. 

        Нумерация каналов в подключениях для чатов: 

        * `0` — канал оператора; 
        * `1` — канал клиента; 
        * `2` — канал бота. 

        Нумерация каналов для аудио предустанавливается на уровне подключения и отличается от нумерации каналов для чатов. 
    * `sortData` — параметры сортировки данных в ответе на запрос. 
      * `fields` — список характеристик диалога, по которым производится сортировка. Поддерживает следующие параметры: 
        * `field` — характеристика диалога, по которой производится сортировка. 
        * `order` — порядок сортировки: по возрастанию или убыванию. 
        * `position` — приоритет поля сортировки (при сортировке по нескольким характеристикам диалога одновременно). 
    * `pageSize` — количество документов на странице. 
    * `pageToken` — токен следующей страницы с результатами поискового запроса. 
      Если результаты запроса разделены на несколько страниц, каждая страница имеет свой токен. В ответе на каждый поисковый запрос содержится токен следующей страницы `next_page_token` (если она существует). Вставьте его в параметр `pageToken` поискового запроса, чтобы получить следующую страницу с результатами поиска. 

    Подробнее о параметрах search-запроса см. в [справочнике API](../../api-ref/Talk/search.md). 

1. {% include [api-key](../../../_includes/speechsense/data/api-key.md) %}
1. Отправьте search-запрос к API {{ speechsense-name }} при помощи cURL: 

    ```bash
    curl -X POST https://rest-api.speechsense.yandexcloud.net/speechsense/v1/talks/search \
       -H "Content-Type: application/json" \
       -H "authorization: Api-Key ${API_KEY}" \
       -d @search.json
    ```

    Где `Api-Key` — API-ключ для аутентификации. Если вы используете IAM-токен, укажите `Bearer ${IAM_TOKEN}` вместо `Api-Key ${API_KEY}`. 

    Идентификаторы диалогов, которые удовлетворяют условиям поиска, будут выведены в терминал в JSON-формате. 

## Примеры запросов {#query-examples}

### Пример тела запроса для полнотекстового поиска {#full-text-search-example}

Например, нужно найти все диалоги с техподдержкой провайдера, в которых оператор предлагал оформить заявку на выезд мастера. JSON-файл с параметрами запроса будет выглядеть так: 

```json
{
  "organizationId": "yc.organization****************",
  "spaceId": "f3fuclf1kufs********",
  "connectionId": "eag0u346n4hn********",
  "projectId": "eag9t3rm3o43********",
  "query": {
    "text": "выезд мастера",
    "channelNumber": "0"
  },  
}
```

Результат выполнения запроса: 

```json
{
  "talkIds": [
    "aud95sn63lra********"
  ],
  "talksCount":" 1",
  "nextPageToken": ""
}
```

Где: 

* `talkIds` — идентификаторы диалогов, которые удовлетворяют условиям поиска. 
* `talksCount` — количество диалогов, которые удовлетворяют условиям поиска. 
* `nextPageToken` — токен следующей страницы с результатами поиска. Если результаты поиска разделены на несколько страниц, этот токен используется в следующем запросе, чтобы запросить следующую страницу. Если это поле вернулось пустым, результаты поиска заканчиваются на текущей странице. 

### Пример тела запроса для полнотекстового поиска с фильтрацией по параметрам {#full-text-search-with-filters-example}

Например, нужно найти все диалоги с техподдержкой провайдера в промежуток между 11:00 и 12:00 24 сентября 2024 года, в которых оператор предлагал оформить заявку на выезд мастера. JSON-файл с параметрами запроса будет выглядеть так: 

```json
{
  "organizationId": "yc.organization****************",
  "spaceId": "f3fuclf1kufs********",
  "connectionId": "eag0u346n4hn********",
  "projectId": "eag9t3rm3o43********",
  "query": {
    "text": "выезд мастера",
    "channelNumber": "0"
  },
  "filters": [
    {
      "key": "userMeta.date",
      "dateRange": {
        "fromValue": "2024-09-24T11:00:00Z",
        "toValue": "2024-09-24T12:00:00Z"
      }
    }
  ],  
}
```

Результат выполнения запроса: 

```json
{
  "talkIds": [
    "aud95sn63lra********"
  ],
  "talksCount":" 1",
  "nextPageToken": ""
}
```

Где: 

* `talkIds` — идентификаторы диалогов, которые удовлетворяют условиям поиска. 
* `talksCount` — количество диалогов, которые удовлетворяют условиям поиска. 
* `nextPageToken` — токен следующей страницы с результатами поиска. Если результаты поиска разделены на несколько страниц, этот токен используется в следующем запросе, чтобы запросить следующую страницу. Если это поле вернулось пустым, результаты поиска заканчиваются на текущей странице. 
