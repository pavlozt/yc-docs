# Как начать работать с {{ data-transfer-name }}

Копируйте или реплицируйте данные из источника в приемник, создав [эндпоинты](concepts/index.md#endpoint) и настроив между ними [трансферы](concepts/index.md#transfer).
См. [полный список эндпоинтов](transfer-matrix.md) и возможных трансферов между ними.


## Перед началом работы {#before-you-begin}

1. Перейдите в [консоль управления]({{ link-console-main }}), затем войдите в {{ yandex-cloud }} или зарегистрируйтесь.
1. Перейдите в сервис [{{ billing-name }}]({{ link-console-billing }}) и убедитесь, что у вас подключен [платежный аккаунт](../billing/concepts/billing-account.md), и он находится в [статусе](../billing/concepts/billing-account-statuses.md) `ACTIVE` или `TRIAL_ACTIVE`. Если платежного аккаунта нет, [создайте его](../billing/quickstart/index.md) и [привяжите](../billing/operations/pin-cloud.md) к нему облако.

1. Перейдите в [консоль]({{ link-console-main }}) {{ yandex-cloud }} и выберите каталог, в котором будете выполнять операции. Если такого каталога нет, [создайте его](../resource-manager/operations/folder/create.md).
1. На странице [Управление доступом]({{ link-console-access-management }}) убедитесь, что у вас есть роль `editor` или выше на нужный каталог или облако, которому принадлежит этот каталог.

Если вы создаете эндпоинт управляемой базы данных для кластера, который находится в другом каталоге, вам потребуется сервисная или примитивная [роль `viewer`](../iam/roles-reference.md#viewer), выданная на этот каталог.

{% note info %}

При создании эндпоинта выбрать кластер, который находится в другом каталоге, можно только в CLI {{ yandex-cloud }}, Terraform или API.

{% endnote %}

Подробнее о том, какие роли нужны для пользования сервисом {{ data-transfer-name }}, см. в разделе [Какие роли необходимы](security/index.md#required-roles).


Для работы с сервисом в консоли управления перейдите на страницу каталога и выберите сервис [**{{ ui-key.yacloud.iam.folder.dashboard.label_data-transfer }}**]({{ link-console-main }}/link/data-transfer/). 

## Настройте источник и приемник {#db-settings}

В качестве источника и приемника могут выступать разные системы.

### Проверьте совместимость источников и приемников {#connectivity-matrix}

{% include [include](../_includes/data-transfer/connectivity-marix.md) %}

### Подготовьте источники и приемники данных {#prepare-source-and-target}

Подготовьте источник к отправке данных:

* {{ AB }}:
    * [AWS CloudTrail](operations/prepare.md#source-aws)
    * [BigQuery](operations/prepare.md#source-bigquery)
    * [Microsoft SQL Server](operations/prepare.md#source-mssql)
    * [S3](operations/prepare.md#source-s3)
* [{{ KF }}](operations/prepare.md#source-kf)
* [{{ CH }}](operations/prepare.md#source-ch)
* [{{ ES }}](operations/prepare.md#source-es)
* [{{ GP }}](operations/prepare.md#source-gp)
* [{{ MG }}](operations/prepare.md#source-mg)
* [{{ MY }}](operations/prepare.md#source-my)
* [{{ OS }}](operations/prepare.md#source-os)
* [Oracle](operations/prepare.md#source-oracle)
* [{{ PG }}](operations/prepare.md#source-pg)
* [{{ yds-full-name }}](operations/prepare.md#source-yds)

* [{{ ydb-full-name }}](operations/prepare.md#source-ydb)

Подготовьте приемник к получению данных:

* [{{ CH }}](operations/prepare.md#target-ch)
* [{{ ES }}](operations/prepare.md#target-es)
* [{{ GP }}](operations/prepare.md#target-gp)
* [{{ MG }}](operations/prepare.md#target-mg)
* [{{ MY }}](operations/prepare.md#target-my)
* [{{ objstorage-name }}](operations/prepare.md#target-storage)
* [{{ OS }}](operations/prepare.md#target-os)
* [{{ PG }}](operations/prepare.md#target-pg)


* [{{ ydb-name }}](operations/prepare.md#target-ydb)


## Создайте эндпоинт для источника {#source}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-transfer }}**.
  1. На панели слева выберите ![image](../_assets/console-icons/aperture.svg) **{{ ui-key.yacloud.data-transfer.label_endpoints }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.data-transfer.button_create-endpoint }}**.
  1. Убедитесь, что в поле **{{ ui-key.yacloud.data-transfer.forms.label-is_source }}** указано `{{ ui-key.yacloud.data-transfer.forms.label_source-type }}`.
  1. Укажите имя эндпоинта.
  1. В поле **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}** выберите тип СУБД, из которой вы хотите передавать данные.
  1. Укажите параметры эндпоинта в соответствующем блоке настроек.
  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

Подробнее см. в разделе [{#T}](operations/endpoint/index.md).

## Создайте эндпоинт для приемника {#target}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-transfer }}**.
  1. На панели слева выберите ![image](../_assets/console-icons/aperture.svg) **{{ ui-key.yacloud.data-transfer.label_endpoints }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.data-transfer.button_create-endpoint }}**.
  1. Убедитесь, что в поле **{{ ui-key.yacloud.data-transfer.forms.label-is_source }}** указано `{{ ui-key.yacloud.data-transfer.forms.label_target-type }}`.
  1. Укажите имя эндпоинта.
  1. В поле **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}** выберите тип СУБД, в которую вы хотите передавать данные.
  1. Укажите параметры эндпоинта в соответствующем блоке настроек.
  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

Подробнее см. в разделе [{#T}](./operations/endpoint/index.md).

## Создайте трансфер {#create-transfer}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-transfer }}**.
  1. На панели слева выберите ![image](../_assets/console-icons/arrow-right-arrow-left.svg) **{{ ui-key.yacloud.data-transfer.label_connectors }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.data-transfer.button_create-transfer }}**.
  1. Укажите имя трансфера.
  1. (Опционально) Добавьте описание трансфера.
  1. Выберите эндпоинт для источника и эндпоинт для приемника.
  1. Выберите [тип трансфера](./concepts/index.md#transfer-type.md):
     * {{ dt-type-copy }} — чтобы создать полную копию данных без дальнейшего получения обновлений из источника. Этот тип также можно использовать для [репликации постоянно меняющихся таблиц](concepts/transfer-lifecycle.md#select-transfer-type).
       Если вам нужно создавать полную копию данных через определенные интервалы времени, включите настройку **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferTypeSnapshot.regular_snapshot.title }}**.
     * {{ dt-type-repl }} — чтобы непрерывно получать изменения данных от источника и применять их к приемнику (без создания полной копии данных источника).
     * {{ dt-type-copy-repl }} — чтобы создать полную копию данных источника и поддерживать ее в актуальном состоянии.
  1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

Подробнее см. в разделе [Типы трансферов](./concepts/transfer-lifecycle.md#transfer-types).

## Активируйте трансфер {#activate}

{% list tabs group=instructions %}

- Консоль управления {#console}

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_data-transfer }}**.
  1. На панели слева выберите ![image](../_assets/console-icons/arrow-right-arrow-left.svg) **{{ ui-key.yacloud.data-transfer.label_connectors }}**.
  1. Нажмите значок ![ellipsis](../_assets/console-icons/ellipsis.svg) рядом с именем нужного трансфера и выберите пункт **{{ ui-key.yacloud.data-transfer.label_connector-operation-ACTIVATE }}**.

{% endlist %}

Начнется процесс переноса данных.

Подробнее см. в разделе [{#T}](operations/transfer.md).

## Что дальше {#whats-next}

* Изучите [концепции сервиса](concepts/index.md).
* Узнайте больше о [подготовке баз данных для использования сервиса](operations/prepare.md) и [настройке трансферов](operations/transfer.md).
* Изучите [роли](security/index.md), действующие в {{ data-transfer-name }}.
* Ознакомьтесь с [практическими руководствами по работе с сервисом](tutorials/index.md).

{% include [greenplum-trademark](../_includes/mdb/mgp/trademark.md) %}

{% include [clickhouse-disclaimer](../_includes/clickhouse-disclaimer.md) %}
