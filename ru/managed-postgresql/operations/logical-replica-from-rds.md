---
title: Создание логической реплики Amazon RDS для {{ PG }} в {{ mpg-full-name }}
description: Следуя данной инструкции, вы сможете создать логическую реплику Amazon RDS для {{ PG }} в {{ mpg-name }}.
---

# Создание логической реплики Amazon RDS для {{ PG }} в {{ mpg-name }}


Перенести базу данных из _кластера-источника_ Amazon RDS для {{ PG }} в _кластер-приемник_ {{ mpg-name }} можно с помощью логической репликации.

[Логическая репликация]({{ pg-docs }}/logical-replication.html) использует механизм [подписки (subscription)]({{ pg-docs }}/sql-createsubscription.html). Это позволяет перенести данные в кластер-приемник с минимальным временем простоя. Логическая репликация доступна в Amazon RDS для {{ PG }} версии 10.4 и выше.

Используйте логическую репликацию в том случае, если [перенос данных с помощью {{ data-transfer-full-name }}](../tutorials/data-migration.md#data-transfer) по каким-либо причинам невозможен.

Чтобы перенести базу данных из кластера-источника Amazon RDS для {{ PG }} в кластер-приемник {{ mpg-name }}:

1. [Настройте Amazon RDS](#amazon-set).
1. [Настройте кластер-приемник и создайте подписку](#mdb-pg-set).
1. [Перенесите последовательности](#transfer-sequences).
1. [Удалите подписку и переключите нагрузку на кластер-приемник](#transfer-load).


## Необходимые платные ресурсы {#paid-resources}

В стоимость поддержки описываемого решения входят:

* Плата за кластер {{ mpg-name }}: использование вычислительных ресурсов, выделенных хостам, и дискового пространства (см. [тарифы {{ mpg-name }}](../../managed-postgresql/pricing.md)).
* Плата за использование публичных IP-адресов для хостов кластера (см. [тарифы {{ vpc-name }}](../../vpc/pricing.md)).


## Особенности использования логической репликации {#logical-replica-specific}

* Изменения схемы базы данных и DDL не реплицируются.

    В первую очередь применяйте новые изменения схемы на стороне [подписчика (subscription)]({{ pg-docs }}/logical-replication-subscription.html), а потом — на стороне [публикации (publication)]({{ pg-docs }}/logical-replication-publication.html).

* Последовательности (`SEQUENCES`) не реплицируются.

    В составе таблицы реплицируются данные в столбцах `serial` или столбцах идентификации, которые генерируются последовательностями. Но сама последовательность на подписчике будет сохранять стартовое значение.

    В случае переключения на базу подписчика, необходимо обновить последовательность до последнего значения:

    ```sql
    ALTER SEQUENCE serial RESTART WITH <новое_значение>;
    ```

* По умолчанию при создании подписки происходит полное копирование данных из исходных таблиц.

    Для ускорения копирования создайте только первичный ключ (`PRIMARY KEY`), а после его завершения создайте все остальные индексы.

* Если в таблице отсутствует первичный ключ, во время репликации появятся ошибки:

    ```text
    ERROR: 55000: cannot update table "<имя_таблицы>" because it does not have a replica identity and publishes updates
    HINT: To enable updating the table, set REPLICA IDENTITY using ALTER TABLE.
    ```

    Для работы репликации `UPDATE` и `DELETE` в таблицах без первичного ключа необходимо изменить `REPLICA IDENTITY`:

    ```sql
    ALTER TABLE <имя_таблицы> REPLICA IDENTITY FULL;
    ```

* Внешние таблицы не реплицируются.
* Если потребуется пересоздать подписку, то для предотвращения ошибок ограничения первичного ключа очистите таблицы в кластере-приемнике.
* Ошибки, связанные с работой логической репликации, смотрите в [логах {{ mpg-name }}](cluster-logs.md).

## Перед началом работы {#before-you-begin}

Создайте необходимые ресурсы:

{% list tabs group=resources %}

- Вручную {#manual}

    [Создайте кластер {{ mpg-name }}](../operations/cluster-create.md) с публичным доступом к хостам. При этом:

    * Версия {{ PG }} должна быть не ниже, чем в кластере-источнике. Миграция с понижением версии {{ PG }} невозможна.
    * Имя базы данных должно быть такое же, как и в кластере-источнике.
    * Включите те же [расширения {{ PG }}](../operations/extensions/cluster-extensions.md), что и в базе-источнике.

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. Скачайте в ту же рабочую директорию файл конфигурации [logical-replica-amazon-rds-to-postgresql.tf](https://github.com/yandex-cloud-examples/yc-postgresql-amazon-rds-replica/blob/main/logical-replica-amazon-rds-to-postgresql.tf).

        В этом файле описаны:

        * [сеть](../../vpc/concepts/network.md#network);
        * [подсеть](../../vpc/concepts/network.md#subnet);
        * [группа безопасности](../../vpc/concepts/security-groups.md) и правило, разрешающее подключение к кластеру;
        * кластер {{ mpg-name }} с публичным доступом из интернета.

    1. Укажите параметры инфраструктуры в файле конфигурации `logical-replica-amazon-rds-to-postgresql.tf` в блоке `locals`:

        * `pg_version` — версия {{ PG }}. Она должна быть не ниже, чем в Amazon RDS.
        * `db_name` — имя базы данных в кластере-приемнике. Должно совпадать с именем базы-источника.
        * `username` и `password` — имя и пароль пользователя-владельца базы данных.
        * Имена и версии расширений {{ PG }}, используемых в Amazon RDS. Для этого раскомментируйте и размножьте блок `extension`.

    1. Проверьте корректность файлов конфигурации {{ TF }} с помощью команды:

        ```bash
        terraform validate
        ```

        Если в файлах конфигурации есть ошибки, {{ TF }} на них укажет.

    1. Создайте необходимую инфраструктуру:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Настройте Amazon RDS {#amazon-set}

{% note warning %}

Экземпляр БД должен иметь публичный доступ — `Public accessibility = yes`.

{% endnote %}

1. Настройте логическую репликацию.

    1. Установите параметр в `parameter group` вашего экземпляра БД:

        ```text
        rds.logical_replication = 1
        ```

    1. Перезапустите кластер для применения изменений.

1. Создайте отдельного пользователя с ролью `rds_replication`. Для этого выполните от имени пользователя с ролью `rds_superuser`:

    ```sql
    CREATE ROLE <имя_пользователя> WITH LOGIN PASSWORD <пароль>;
    GRANT rds_replication TO <имя_пользователя>;
    ```

1. Предоставьте привилегию `SELECT` на все таблицы, участвующие в репликации:

    ```sql
    GRANT SELECT ON <таблица_1>, <таблица_2>, ..., <таблица_n> TO <имя_пользователя>;
    ```

1. Создайте публикацию:

    ```sql
    CREATE PUBLICATION pub FOR TABLE <таблица_1>, <таблица_2>, ..., <таблица_n>;
    ```

    {% note info %}

    Не рекомендуется использовать публикации `FOR ALL TABLES` из-за невозможности отредактировать список таблиц в будущем.

    {% endnote %}

1. Добавьте правило для входящего трафика в [{{ vpc-short-name }} security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html). Например:

    ```text
    protocol: tcp, port: 5432, source: 84.201.175.90/32
    ```

    Где `84.201.175.90` — публичный IP-адрес.

## Настройте кластер-приемник и создайте подписку {#mdb-pg-set}

В кластерах {{ mpg-name }} подписки может использовать владелец базы данных (пользователь, созданный одновременно с кластером) и пользователи с ролью `mdb_admin` или `mdb_superuser` для этого кластера.

1. (Опционально) [Назначьте](../operations/grant.md#grant-role) пользователю кластера {{ mpg-name }} роль `mdb_admin` или `mdb_superuser`.

1. Создайте подписку со строкой подключения к кластеру-источнику:

    ```sql
    CREATE SUBSCRIPTION s_data_migration CONNECTION 'host=<адрес_кластера-источника> port=<порт> user=<имя_пользователя> sslmode=prefer dbname=<имя_БД>' PUBLICATION pub;
    ```

    Подробнее о создании подписок см. в [документации {{ PG }}]({{ pg-docs }}/sql-createsubscription.html).

1. Чтобы получить статус репликации, обратитесь к каталогам `pg_subscription_rel`.

    ```sql
    SELECT * FROM pg_subscription_rel;
    ```

    Значение `r` в поле `srsubstate` означает, что репликация завершилась.

### Перенесите последовательности {#transfer-sequences}

Чтобы завершить синхронизацию кластера-источника и кластера-приемника:

1. Переведите кластер-источник в режим <q>только чтение</q>.
1. Создайте дамп с последовательностями:

    ```bash
    pg_dump --host=<адрес_кластера-источника> \
            --username=<имя_пользователя> \
            --port=<порт> \
            --dbname=<имя_БД> \
            --data-only \
            --table='*.*_seq' > /tmp/seq-data.sql
    ```

    Обратите внимание на используемый паттерн `*.*_seq`. Если в переносимой базе есть не соответствующие ему последовательности, то для их выгрузки укажите другой паттерн.

    Подробнее о паттернах см. в [документации {{ PG }}]({{ pg-docs }}/app-psql.html#APP-PSQL-PATTERNS).

1. Восстановите дамп с последовательностями в кластере-приемнике:

    ```bash
    psql \
        --host=<FQDN_хоста-мастера_кластера-приемника> \
        --username=<имя_пользователя> \
        --port={{ port-mpg }} \
        --dbname=<имя_БД> < /tmp/seq-data.sql
    ```

### Удалите подписку и переключите нагрузку на кластер-приемник {#transfer-load}

1. Удалите подписку в кластере-приемнике:

    ```sql
    DROP SUBSCRIPTION s_data_migration;
    ```

1. Перенесите нагрузку на кластер-приемник.
