---
title: 'Изучаем Data Lake: суть и преимущества Озера данных'
description: 'Data Lake или Озеро данных — технология для получения и управления данными в разных форматах: в необработанном, неупорядоченном или, наоборот, структурированном или слабоструктурированном виде, в едином репозитории.'
---

# Data Lake

Data Lake или Озеро данных — технология для получения и управления данными в разных форматах: в необработанном, неупорядоченном или, наоборот, структурированном или слабоструктурированном виде, в едином репозитории. Данные, которые можно хранить в озере:

Тип данных             | Примеры
---------------------- | ----------------
Неструктурированные    | Текстовые документы, медицинские данные, изображения и видео
Слабоструктурированные | Файлы в формате xml, edi, json и лог-файлы
Структурированные      | Строки и столбцы реляционных БД, таблицы Excel

## Применение Data Lake {#usage}

Термин придуман в 2010-м году основателем компании Pentaho Джеймсом Диксоном. Описывая концепцию, он сравнил Data Lake и [Data Mart](datamart.md). Витрины данных похожи на бутилированную воду — очищенную и упакованную. Озера данных — это открытые водоемы, в которые вода стекается из различных источников. В водоемы можно погружаться, а можно брать образцы с поверхности.

Так, озера данных удобны для сбора, хранения и обработки больших потоков информации, которая поступает непрерывно. Если грамотно их использовать, они станут надежным инструментом для следующих отраслей:

* **Телекоммуникации.** Озера данных часто применяются для хранения и анализа данных о клиентах, трафике, сетевых устройствах и других факторах, которые влияют на бизнес телекоммуникационных компаний.
* **Нефтегазовая промышленность.** Data Lake собирают терабайты данных и используют их в прогнозных моделях для разведки месторождений, управления цепочками поставок и техническим обслуживанием.
* **Медицина.** База данных о пациентах, их диагнозах и способах лечения может быть использована для автоматизации диагностики.
* **Розничная торговля.** Data lake позволяет хранить и анализировать данные о продажах, клиентах, инвентаре и других факторах, которые важны для розничных компаний.
* **Организации, занимающиеся финансами, страхованием, логистикой, закупками** — любой бизнес, который обрабатывает огромные объемы данных, может извлечь выгоду из использования Data Lake.

## Примеры использования Data Lake {#examples}

* Омниканальный маркетинг.

   Приложения для смартфонов нередко собирают информацию о действиях пользователя, а озера данных позволяют оперативно ее получать. На основе этой информации маркетологи могут делать специальные предложения или персонализированные скидки. Так, стриминговый сервис Netflix, Inc. с помощью Data Lake [получает](https://www.protocol.com/enterprise/how-netflix-and-uber-helped-create-the-data-lakehouse-by-preserving-an-open-source-tradition) данные о просмотренных пользователями фильмах и сериалах.

* Цифровая цепочка поставок.

   В цифровой цепочке поставок часто необходимо собирать большие объемы данных разного формата. Например, информация из цеха, отчеты о доставке и оплате. Благодаря Data Lake производитель может объединить их. Одним из первых пользователей Data Lake в промышленности [стал](https://www.ge.com/news/press-releases/ge-announces-first-data-lake-approach-industrial-internet-better-access-analyze-and) международный производитель техники General Electric.

* Интернет вещей.

   [Интернет вещей](https://ru.wikipedia.org/wiki/Интернет_вещей) непрерывно расширяется за счет многочисленных датчиков, которые устанавливаются на транспортные средства. С помощью них отслеживается передвижение транспорта, безопасность его эксплуатации, расход топлива и другое. Озера данных популярны для сбора такой информации, поскольку не требуют ее структурирования. Например, сервис доставки и такси Uber с помощью Data Lake [следит](https://www.protocol.com/enterprise/how-netflix-and-uber-helped-create-the-data-lakehouse-by-preserving-an-open-source-tradition) за своими автомобилями.

## Как устроено озеро данных {#structure}

Озеро данных можно рассматривать как шаблон проектирования — формализованные рекомендации, которые можно использовать для решения распространенных проблем при разработке инфраструктуры. Четыре основные особенности устройства Data Lake:
* Данные необработаны либо слабообработаны.
* Большой срок хранения данных.
* Есть возможность преобразования данных.
* Поддерживаются разные схемы чтения данных.

Файлы в Data Lake хранятся на нескольких серверах, куда поступают из таких источников, как CRM-системы, социальные сети, интернет-магазины, датчики на производстве и прочих. Поступающей в озеро информации присваиваются метаданные: время поступления, источник, формат, структура и другое.

Все это может использоваться для извлечения данных в будущем, чтобы провести аналитику или обучить чему-то искусственный интеллект. Способы организации Data Lake могут включать HDFS, [S3](s3.md), [Data Vault](datavault.md) и распределенные файловые системы. Для размещения озера данных можно использовать как локальное хранилище, так и облачное.

## Отличия Data Lake от хранилища данных {#datalake-vs-storage}

У озера данных и хранилища данных есть существенные различия, которые надо учитывать при выборе способа хранения информации:

Область сравнения | Data Lake | Хранилище данных
------------------|-----------|-----------------
Сбор данных | Данные любой структуры и из любых источников | Данные приведены к единому виду
Обработка данных | Осуществляется после сбора | Осуществляется перед сбором
Основные пользователи | Специалисты по глубокому анализу данных | Оперативные пользователи
Стоимость хранения | Ниже | Выше
Получение данных | Высокая скорость получения | Низкая скорость получения

## Преимущества Data Lake {#advantages}

* Гибкие варианты использования.

   При использовании озер данных не нужно заранее знать, как их необходимо будет анализировать. Например, данные из одного и того же озера можно использовать для поиска совпадающих записей или удаления дублирующихся, преобразования данных для внешней интеграции, классификации и кластеризации или машинного обучения.

* Снижение эксплуатационных расходов.

   Традиционные хранилища данных для аналитики и систем поддержки принятия решений используются уже более 30 лет. Озера данных совмещают в себе лучшие открытые и бесплатные технологии, что позволяет сэкономить на сборе и обработке информации.

* Быстрый доступ к данным.

   Информация — это стратегический актив, на основе которого можно разрабатывать инновации. Data Lake позволяет быстрее получить данные и принять необходимые решения. Искусственный интеллект также зависит от больших объемов разнообразной информации, быстрый доступ к которой можно организовать с помощью озер данных.

* Совместное использование.

   Крупные организации традиционно работают в разрозненных группах, каждая из которых может использовать разные типы данных. Объединенное хранилище для них — отличное решение, чтобы наладить совместную работу между командами.

* Масштабируется бесконечно.

   Благодаря низкой стоимости Data Lake не имеют ограничений по размеру. Также озера данных способны масштабироваться горизонтально и вертикально, что позволяет обрабатывать вплоть до нескольких петабайт данных.

## Недостатки Data Lake {#disadvantages}

Озера данных оптимизированы для высокой пропускной способности, но ради этого приходится жертвовать качеством данных:

* В Data Lake не требуется структурировать данные, поэтому их сложнее анализировать.
* Data Lake не имеет инструментов для интегрированного или целостного получения всех данных.
* Без квалифицированного контроля за озерами данных трудно гарантировать конфиденциальность и безопасность хранилища.
* Для сбора реляционных данных есть гораздо более удобные решения, чем Data Lake.
* Если управление озером организовано плохо, в нем быстро накапливаются большие объемы неконтролируемых, и, возможно, бесполезных данных. Для эффективной фильтрации данных и отсечения недостоверных источников требуется высокая квалификация.

## Советы по использованию Data Lake {#tips}

Для извлечения максимальной выгоды из озер данных нужно с умом подойти к их использованию. К счастью, технология уже обкатана многими компаниями, которые сформулировали основные правила работы с озерами. Можно выделить три основных момента, которые помогут развернуть озеро данных, избежав возможных проблем в будущем:

1. Главное — сбор данных.

   Прежде всего необходимо максимально конкретизировать, что именно собирать, потому что озеро может поместить в себя что угодно и превратиться в болото. Грамотная настройка источников информации и фильтров в дальнейшем сильно упростит анализ и поможет сэкономить.

2. Максимальная детализация метаданных.

   Данные в озерах часто неструктурированные или слабоструктурированные, но навести порядок в хранилище можно с помощью каталогизации и метаданных. Затраченные усилия непременно окупятся, когда придет время анализировать результаты.

3. План уничтожения данных.

   Распространенная ошибка при работе с большими объемами информации — отсутствие плана по избавлению от ненужной. Если до сбора данных в озеро их удастся правильно разметить, то это поможет не удалить что-то нужное вместе с мусором, а также избавит от проблем с различными регуляторами и соответствием [регламентам](https://ru.wikipedia.org/wiki/Общий_регламент_по_защите_данных).

## Реализация Data Lake с помощью сервисов {{ yandex-cloud }} {#datalake-yc}

{{ yandex-cloud }} предлагает следующие инструменты для работы с озерами данных:
   * Сервис [{{ mkf-name }}](/services/managed-kafka/) — работа с кластерами в режиме реального времени. Преимущество платформы в высокой пропускной способности и низкой задержке. Подробнее см. в [документации](../managed-kafka/).
   * Сервис [{{ dataproc-name }}](../data-proc), который автоматически создает кластеры, настраивает сеть, устанавливает и обновляет ПО:
     * Работа с заданиями [Hive](../data-proc/operations/jobs-hive.md).
     * Работа с заданиями [Hadoop](../data-proc/operations/jobs-mapreduce.md).
