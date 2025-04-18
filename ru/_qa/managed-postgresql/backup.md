#### Можно ли восстановить резервную копию кластера в работающий экземпляр {{ mpg-short-name }} в другой облачной сети? {#diff-network}

Восстановить кластер из резервной копии можно только в рамках одной облачной сети.

Чтобы перенести данные между облачными сетями, воспользуйтесь [инструкциями по миграции](../../managed-postgresql/tutorials/replication-overview.md).

#### Можно ли изменить срок хранения автоматических резервных копий? {#backup-retain-days}

При [создании](../../managed-postgresql/operations/cluster-create.md) или [изменении](../../managed-postgresql/operations/update.md#change-additional-settings) кластера можно задать срок хранения автоматических резервных копий.

#### Нужен ли плагин wal2json, если выполняется только реплицирование, но не копирование данных? {#wal2json}

Да, плагин обязателен, даже если вы не выполняете копирование данных.

#### Можно ли локально выгрузить резервную копию базы данных? {#backup-local-dump}

Локально выгрузить резервную копию из {{ yandex-cloud }} средствами сервиса нельзя, но вы можете воспользоваться [утилитой pg_dump]({{ pg.docs.org }}/current/app-pgdump.html).

#### Как перенести локальный дамп базы данных {{ PG }} в {{ yandex-cloud }}? {#dump-to-yc}

Воспользуйтесь инструкцией в разделе [Миграция базы данных](../../managed-postgresql/tutorials/data-migration.md).

#### Почему перенос данных через создание и восстановление логического дампа завершается ошибкой? {#backup-error}

[Восстановление логического дампа](../../managed-postgresql/tutorials/data-migration.md#backup) может завершаться одной из ошибок:

* `ERROR:  role "<имя_пользователя_источника>" does not exist`
* `ERROR:  must be member of role "<имя_пользователя_источника>"`

Ошибки связаны с отсутствием в кластере-приемнике пользователя или привилегий пользователя, от имени которого был создан логический дамп в кластере-источнике.

Чтобы устранить ошибки:

1. [Добавьте пользователя](../../managed-postgresql/operations/cluster-users.md#adduser) в кластере-приемнике с доступом к базе данных для переноса и таким же именем, что и у пользователя, с помощью которого был создан логический дамп в кластере-источнике.
1. [Восстанавливайте логической дамп](../../managed-postgresql/tutorials/data-migration.md#restore) с помощью этого пользователя или [выдайте его привилегии](../../managed-postgresql/operations/grant.md#grant-privilege) пользователю, с помощью которого вы восстанавливаете логической дамп.

#### Что делать, если при выполнении логической репликации возникает ошибка `replication slot already exists`? {#repl-slot-exists}

По умолчанию при [создании подписки](../../tutorials/dataplatform/postgresql-data-migration.md#create-publication-subscription) создается и слот репликации. Ошибка `replication slot already exists` означает, что слот репликации уже существует.

Устранить ошибку можно одним из способов:

1. Привяжите подписку к существующему слоту репликации. Для этого добавьте в запрос для создания подписки параметр `create_slot = false`.
1. [Удалите существующий слот репликации](../../managed-postgresql/operations/replication-slots.md#delete) и попробуйте создать подписку еще раз.
