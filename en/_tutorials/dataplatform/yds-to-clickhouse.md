# Saving a {{ yds-full-name }} data stream in {{ mch-full-name }}


With {{ data-transfer-name }}, you can deliver data from a [data stream {{ yds-name }}](../../data-streams/concepts/glossary.md#stream-concepts) to {{ mch-name }}.

To transfer data:

1. [Set up a stream in {{ yds-name }}](#prepare-source).
1. [Set up and activate your transfer](#prepare-transfer).
1. [Test your transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mch-name }} cluster fee: Using computing resources allocated to hosts (including ZooKeeper hosts) and disk space (see [{{ mch-name }} pricing](../../managed-clickhouse/pricing.md)).
* Fee for using public IP addresses if public access is enabled for cluster hosts (see [{{ vpc-name }} pricing](../../vpc/pricing.md)).

* Fee for the {{ ydb-name }} database. The charge depends on the usage mode:

	* For the serverless mode, you pay for data operations and the amount of stored data.
	* For the dedicated instance mode, you pay for the use of computing resources, dedicated DBs, and disk space.
	
    Learn more about the [{{ ydb-name }} pricing](../../ydb/pricing/index.md) plans.

* {{ yds-name }} fee. The fee depends on the pricing mode:

	* Based on allocated resources: You pay for the number of units of written data and resources allocated for data streaming.
	* Based on actual use:
		* If the DB operates in serverless mode, the data stream is charged under the [{{ ydb-short-name }} serverless mode pricing policy](../../ydb/pricing/serverless.md).

		* If the DB operates in dedicated instance mode, the data stream is not charged separately (you only pay for the DB, see the [pricing policy](../../ydb/pricing/dedicated)).

    Learn more about the [{{ yds-name }} pricing](../../data-streams/pricing.md) plans.

* Transfer fee: Using computing resources and the number of transferred data rows (see [{{ data-transfer-name }} pricing](../../data-transfer/pricing.md)).


## Getting started {#before-you-begin}

Set up your infrastructure:

{% list tabs group=instructions %}

- Manually {#manual}

    1. [Create a {{ ydb-name }} database](../../ydb/operations/manage-databases.md) in any suitable configuration.
    1. [Create a {{ mch-name }} cluster](../../managed-clickhouse/operations/cluster-create.md) with any suitable configuration.
    1. [Create a target endpoint](../../data-transfer/operations/endpoint/index.md#create):

        * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `ClickHouse`.
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseTarget.title }}**:

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseTarget.connection.title }}**:

                * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}`.

                    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseManaged.mdb_cluster_id.title }}**: Select the source cluster from the list.

                * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseConnection.database.title }}**: Enter the database name.
                * **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.user.title }}** and **{{ ui-key.yc-data-transfer.data-transfer.console.form.clickhouse.console.form.clickhouse.ClickHouseCredentials.password.title }}**: Enter the name and password of the user who has access to the database (for example, the database owner).

- {{ TF }} {#tf}

    1. {% include [terraform-install-without-setting](../../_includes/mdb/terraform/install-without-setting.md) %}
    1. {% include [terraform-authentication](../../_includes/mdb/terraform/authentication.md) %}
    1. {% include [terraform-setting](../../_includes/mdb/terraform/setting.md) %}
    1. {% include [terraform-configure-provider](../../_includes/mdb/terraform/configure-provider.md) %}

    1. Download the [data-transfer-yds-mch.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-yds-to-clickhouse/blob/main/data-transfer-yds-mch.tf) configuration file to the same working directory.

        This file describes:

        * Database: {{ ydb-name }}.
        * Service account with the `yds.editor` role.
        * [Network](../../vpc/concepts/network.md#network).
        * [Subnet](../../vpc/concepts/network.md#subnet).
        * [Security group](../../vpc/concepts/security-groups.md) and rules required to connect to the {{ mch-name }} cluster from the internet.
        * {{ mch-name }} target cluster.
        * Target endpoint.
        * Transfer.

    1. Specify the following in the `data-transfer-yds-mch.tf` file:

        * `folder_id`: [ID of the folder](../../resource-manager/operations/folder/get-id.md) the resources will be created in.
        * `sa_name`: Name of the service account to be used in endpoints.
        * `source_db_name`: {{ ydb-name }} database name.
        * `target_db_name`: {{ CH }} database name.
        * `target_user` and `target_password`: Username and password of the {{ CH }} database owner.
        * `transfer_enabled`: `0` to ensure that no transfer is created before [a source endpoint is created manually](#prepare-transfer).

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Set up a {{ yds-name }}-enabled stream {#prepare-source}

1. [Create a {{ yds-name }} data stream](../../data-streams/operations/aws-cli/create.md).
1. [Send test data to the data stream](../../data-streams/operations/aws-cli/send.md). Use data from the vehicle sensors in JSON format as a message:

```json
{
    "device_id":"iv9a94th6rzt********",
    "datetime":"2022-06-05 17:27:00",
    "latitude":55.70329032,
    "longitude":37.65472196,
    "altitude":427.5,
    "speed":0,
    "battery_voltage":"23.5",
    "cabin_temperature":17,
    "fuel_level":null
}
```

## Set up and activate the transfer {#prepare-transfer}

{% include [tips for endpoint settings](../../_includes/data-transfer/queue-ch-transfer-tips.md) %}

1. [Create a source endpoint](../../data-transfer/operations/endpoint/index.md#create):

    * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `{{ yds-full-name }}`.
    * **Endpoint settings**:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSource.connection.title }}**:

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSConnection.database.title }}**: Select the {{ ydb-name }} database from the list.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSConnection.stream.title }}**: Specify the name of the {{ yds-name }}-enabled stream.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSConnection.service_account_id.title }}**: Select or create a service account with the `yds.editor` role.

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSource.advanced_settings.title }}**:

            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.yds.console.form.yds.YDSSourceAdvancedSettings.converter.title }}**: `JSON`.
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.data_schema.title }}**: You can specify a schema in two ways:
              * `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataSchema.fields.title }}`.

                Set a list of topic fields manually:

                | {{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ColSchema.name.title }} | {{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ColSchema.type.title }} | {{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ColSchema.key.title }} |
                | :-- | :-- | :--- |
                |`device_id`|`STRING`| Yes|
                |`datetime` |`DATETIME`|  |
                |`latitude` |`DOUBLE`|  |
                |`longitude`|`DOUBLE`|  |
                |`altitude` |`DOUBLE`|  |
                |`speed`    |`DOUBLE`|  |
                |`battery_voltage`| `ANY`||
                |`cabin_temperature`| `DOUBLE`||
                | `fuel_level`|`ANY`||

              * `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataSchema.json_fields.title }}`.

                Create and upload a `json_schema.json` data schema file in JSON format:

                {% cut "json_schema.json" %}

                ```json
                [
                    {
                        "name": "device_id",
                        "type": "string",
                        "key": true
                    },
                    {
                        "name": "datetime",
                        "type": "datetime"
                    },
                    {
                        "name": "latitude",
                        "type": "double"
                    },
                    {
                        "name": "longitude",
                        "type": "double"
                    },
                    {
                        "name": "altitude",
                        "type": "double"
                    },
                    {
                        "name": "speed",
                        "type": "double"
                    },
                    {
                        "name": "battery_voltage",
                        "type": "any"
                    },
                    {
                        "name": "cabin_temperature",
                        "type": "double"
                    },
                    {
                        "name": "fuel_level",
                        "type": "any"
                    }
                ]
                ```

                {% endcut %}

1. Create a transfer:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. [Create a transfer](../../data-transfer/operations/transfer.md#create) of the **{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.increment.title }}** type that will use the created endpoints.
        1. [Activate](../../data-transfer/operations/transfer.md#activate) your transfer.

    - {{ TF }} {#tf}

        1. In the `data-transfer-yds-mch.tf` file, specify these variables:

            * `source_endpoint_id`: ID of the source endpoint.
            * `transfer_enabled`: `1` to create a transfer.

        1. Make sure the {{ TF }} configuration files are correct using this command:

            ```bash
            terraform validate
            ```

            If there are any errors in the configuration files, {{ TF }} will point them out.

        1. Create the required infrastructure:

            {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

            Once created, your transfer will be activated automatically.

    {% endlist %}

## Test your transfer {#verify-transfer}

1. Wait until the transfer status switches to **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}**.

1. [Send a new message to the data stream](../../data-streams/operations/aws-cli/send.md) {{ yds-name }}:

    ```json
    {
        "device_id":"rhibbh3y08qm********",
        "datetime":"2022-06-06 09:49:54",
        "latitude":55.71294467,
        "longitude":37.66542005,
        "altitude":429.13,
        "speed":55.5,
        "battery_voltage":null,
        "cabin_temperature":18,
        "fuel_level":32
    }
    ```

1. Make sure the data from the {{ yds-name }} stream has been moved to the {{ mch-name }} cluster database:

   1. [Connect to the {{ mch-name }} target cluster](../../managed-clickhouse/operations/connect/clients.md).
   1. Check that the {{ CH }} database contains a table with the name of the [created {{ yds-name }} data stream](#prepare-source) with the same columns as the [data schema in the source endpoint](#prepare-transfer) and the sent test data.

## Delete the resources you created {#clear-out}

{% note info %}

Before deleting the resources you created, [deactivate the transfer](../../data-transfer/operations/transfer.md#deactivate).

{% endnote %}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

1. [Delete the transfer](../../data-transfer/operations/transfer.md#delete).
1. Delete the resources depending on how they were created:

   {% list tabs group=instructions %}

   - Manually {#manual}

       * [Delete the target endpoint](../../data-transfer/operations/endpoint/index.md#delete).
       * [Delete the {{ ydb-name }} database](../../ydb/operations/manage-databases.md#delete-db).
       * [Delete the {{ mch-name }} cluster](../../managed-clickhouse/operations/cluster-delete.md).

   - {{ TF }} {#tf}

       {% include [terraform-clear-out](../../_includes/mdb/terraform/clear-out.md) %}

   {% endlist %}

1. [Delete the source endpoint](../../data-transfer/operations/endpoint/index.md#delete).
