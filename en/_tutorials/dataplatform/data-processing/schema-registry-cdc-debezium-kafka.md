# Creating a schema registry to deliver data in Debezium CDC format from {{ KF }}

In this tutorial, you will create a schema registry to track schema evolution for an {{ KF }} source using [{{ data-transfer-full-name }}](../../../data-transfer).

Getting started:

1. Request network permissions in [Puncher](https://puncher.yandex-team.ru/?create_comment=Для%20использования%20сервиса%20Schema%20Registry&create_destinations=_CLOUD_YANDEX_CLIENTS_SCHEMA_REGISTRY_NETS_,schema-registry.yandex-team.ru,schema-registry-preprod.yandex-team.ru&create_locations=office,vpn&create_ports=443&create_protocol=tcp&create_until=persistent).
1. [Request the required roles](../../../metadata-hub/api-ref/authentication.md) and a token for access.

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) you are going to use to work with your schema registry.
  1. Select **{{ metadata-hub-full-name }}**.
  1. In the left-hand panel, select ![image](../../../_assets/console-icons/layout-cells.svg) **{{ schema-registry-name }}** / **Namespace**.
  1. Click **Create namespace**. 
  1. Specify [namespace settings](../../../metadata-hub/operations/create-name-space.md). After saving a namespace, you can use its ID to get the _Schema Registry URL_: `{namespace_id}.schema-registry.yandex-team.ru:443`.
  1. In the [management console]({{ link-console-main }}), go to **{{ data-transfer-full-name }}**. 
  1. Configure an [{{ KF }} endpoint](../../../data-transfer/operations/endpoint/source/kafka.md). 
  1. Apply the _Schema Registry URL_ in **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.format.title }}** settings for **{{ ui-key.yc-data-transfer.data-transfer.console.form.logbroker.console.form.logbroker.ParserConfigCommon.parser_config_common_debezium_cdc.title }}**.
  1. [Create and run](../../../data-transfer/operations/transfer.md) a data transfer.

{% endlist %}

