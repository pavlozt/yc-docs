# CDN-ресурс

_Ресурс_ — это основная логическая сущность сервиса {{ cdn-full-name }}, позволяющая настраивать и управлять распространением контента с [источников](origins.md) через CDN.

Чтобы начать распространять контент через CDN, нужно создать ресурс по [инструкции](../operations/resources/create-resource.md), указав в нем:

* источник или группу источников, которые содержат контент;
* [доменные имена для раздачи контента](#hostnames);
* основные настройки обмена данными [между клиентами и CDN](clients-to-servers.md) и [между CDN и источниками](servers-to-origins.md).

После создания ресурса в нем также можно настроить:

* [кеширование](caching.md);
* HTTP-заголовки и методы (см. [инструкцию](../operations/resources/configure-http.md));
* обработку [кросс-доменных запросов (CORS)](cors.md);
* [сжатие](compression.md) и [сегментирование](slicing.md) контента.

Доступ к ресурсу можно ограничить с помощью [защищенных токенов](./secure-tokens.md) и [политики доступа по IP-адресам](./ip-address-acl.md).

Можно настроить [перенаправление запросов](./http-rewrite.md) от CDN-ресурса к источнику, изменив [URI](https://ru.wikipedia.org/wiki/URI) запроса с помощью [правила](./http-rewrite.md#rewrite-rule) `Rewrite`.

{% include [edge-center-features-note](../../_includes/cdn/edge-center-features-note.md) %}

Настройки ресурса [начинают действовать через некоторое время](#delay) после создания или изменения ресурса.


## Доменные имена для раздачи контента {#hostnames}

_Доменные имена для раздачи контента_ используются в запросах клиентов к CDN-серверам. Например, чтобы файл `styles/common.css` был доступен по адресу `cdn.example.com/styles/common.css`, нужно указать доменное имя `cdn.example.com`.  

В {{ cdn-name }} поддерживаются любые доменные имена, в том числе [IDN](https://ru.wikipedia.org/wiki/IDN) с символами национальных алфавитов не из ASCII, например кириллическими. Такие имена можно указывать в исходном виде (`пример.рф`) или преобразовывать в [Punycode](https://{{ lang }}.wikipedia.org/wiki/Punycode) с добавлением префиксов `xn--` (`xn--e1afmkfd.xn--p1ai`). В информации о ресурсе в [консоли управления]({{ link-console-main }}) IDN будут отображаться в исходном виде, без преобразования в Punycode.

При создании ресурса нужно указать хотя бы одно доменное имя. Первое имя является основным, его нельзя изменить после создания ресурса.

Для корректной работы ресурса у вас должны быть:

* Права на все указанные доменные имена.
* Доступ к настройкам [DNS](../../glossary/dns.md) для имен. 

  Для каждого имени в настройках вашего DNS-хостинга нужно создать CNAME-запись, связывающую это имя с доменным именем CDN-балансировщика вида `cl-********.edgecdn.ru`. Оно отображается при создании ресурса и на странице уже созданного ресурса в консоли управления. Например, так может выглядеть запись для доменного имени `cdn.example.com`: 

  ``` 
  cdn.example.com. CNAME cl-********.edgecdn.ru
  ```

{% note info %}

Нельзя на одном домене второго уровня иметь и CDN-ресурс, и почтовый сервер. Рекомендуем создать два домена:

* Для контента, распространяемого через CDN. Обратите внимание, что {{ cdn-name }} следует использовать только с ресурсными записями [CNAME](../../dns/concepts/resource-record.md#cname), поскольку при использовании записи [ANAME](../../dns/concepts/resource-record.md#aname) конечный пользователь получит ответ, не зависящий от его геолокации. Подробнее о CNAME-записях и их [ограничениях](https://www.rfc-editor.org/rfc/rfc1912#section-2.4) см. в [RFC-1035](https://www.ietf.org/rfc/rfc1035.html#section-3.3.1).
* Для легковесного контента и почтового сервера. В этом случае вы можете использовать ресурсные записи ANAME и [MX](../../dns/concepts/resource-record.md#mx).

{% endnote %}


## Время, требуемое на применение настроек {#delay}

CDN-ресурс начинает корректно работать через некоторое время после создания или изменения: 

* {% include [after-creation-tip](../../_includes/cdn/after-creation-tip.md) %}
* {% include [after-changes-tip](../../_includes/cdn/after-changes-tip.md) %}


## Статусы ресурса {#status}

Ресурс {{ cdn-name }} может быть в одном из статусов:

* `Active`, если контент доступен всем (или станет доступен [после применения настроек](#delay));
* `Not active`, если контент недоступен никому.

Для переключения используется опция **{{ ui-key.yacloud.cdn.field_access }}** в основных настройках ресурса.

Если к ресурсу в течение 90 дней нет пользовательских запросов, он переходит в статус `Not active`.

## Статистика использования ресурса {#stats}

{{ cdn-name }} предоставляет метрики использования ресурса за последние 30 дней:

* **{{ ui-key.yacloud.cdn.label_metrics-upstream-bytes }}** — объем трафика, загруженного CDN-серверами из источников.
* **{{ ui-key.yacloud.cdn.label_metrics-send-bytes }}** — объем трафика, отправленного с CDN-серверов клиентам.
* **{{ ui-key.yacloud.cdn.label_metrics-total-bytes }}** — сумма метрик **{{ ui-key.yacloud.cdn.label_metrics-upstream-bytes }}** и **{{ ui-key.yacloud.cdn.label_metrics-send-bytes }}**.
* **{{ ui-key.yacloud.cdn.label_metrics-requests }}** — количество запросов клиентов к CDN.
* **{{ ui-key.yacloud.cdn.label_responses-hit }}** — количество запросов клиентов, в ответ на которые CDN-серверы отправили [кешированные](caching.md) копии файлов, не запрашивая их у источников (если кеширование на CDN-серверах включено).
* **{{ ui-key.yacloud.cdn.label_responses-miss }}** — количество запросов клиентов, в ответ на которые CDN-серверы не смогли отправить кешированные копии файлов (например, кеширование отключено, файл еще не кешировался сервером или [время жизни кеша](caching.md#server-side-cache-age) истекло) и обратились за ними к источникам.
* **{{ ui-key.yacloud.cdn.label_cache-hit-ratio }}** — доля трафика, отправленного с CDN-серверов клиентам, за которым не потребовалось обращаться к источникам. Рассчитывается по формуле `(1 − ({{ ui-key.yacloud.cdn.label_metrics-upstream-bytes }} / {{ ui-key.yacloud.cdn.label_metrics-send-bytes }})) × 100%`.
* **{{ ui-key.yacloud.cdn.label_metrics-shield-bytes }}** — объем трафика, загруженного экранирующим сервером из источников (если [экранирование](origins-shielding.md) включено).
* **{{ ui-key.yacloud.cdn.label_responses2xx }}**, **3xx**, **4xx**, **5xx** — количество ответов, отправленных CDN-серверами клиентам, с соответствующими HTTP-кодами состояния.

Посмотреть статистику можно на странице ресурса в [консоли управления]({{ link-console-main }}).

## Метрики {{ monitoring-name }} {#resource-stats}

{{ cdn-name }} автоматически отправляет метрики работы ресурсов в сервис [{{ monitoring-full-name }}](../../monitoring/).

Вы можете посмотреть статистику работы на [странице ресурса](../operations/resources/get-stats.md#cdn-ui) или в [интерфейсе {{ monitoring-name }}](../operations/resources/get-stats.md#monitoring).

{% include [metrics-note](../../_includes/cdn/metrics-note.md) %}

Список метрик, передаваемых в {{ monitoring-name }}, представлен в [справочнике](../metrics.md).


## Примеры использования {#examples}

* [{#T}](../tutorials/migrate-to-yc-cdn.md)
* [{#T}](../tutorials/prefetch.md)
* [{#T}](../tutorials/thumbor.md)
* [{#T}](../tutorials/blue-green-canary-deployment.md)
* [{#T}](../tutorials/cdn-storage-integration.md)
* [{#T}](../tutorials/protected-access-to-content/index.md)


#### См. также {#see-also}

* [Инструкции по работе с ресурсами](../operations/index.md#resources).
* [Инструкция по работе с основными настройками ресурса](../operations/resources/configure-basics.md).