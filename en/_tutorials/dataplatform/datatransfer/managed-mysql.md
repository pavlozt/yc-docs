# Transferring data using {{ data-transfer-full-name }} {#data-transfer}

To transfer a database from {{ MY }} to {{ mmy-name }}:

1. [Start data transfer](#start-transfer).
1. [Finish data transfer](#finish-transfer).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mmy-name }} cluster fee: Using computing resources allocated to hosts and disk space (see [{{ mmy-name }} pricing](../../../managed-mysql/pricing.md)).
* Fee for using public IP addresses if public access is enabled for cluster hosts (see [{{ vpc-name }} pricing](../../../vpc/pricing.md)).
* Transfer fee: Using computing resources and the number of transferred data rows (see [{{ data-transfer-name }} pricing](../../../data-transfer/pricing.md)).


## Start data transfer {#start-transfer}

1. [Prepare the source cluster](../../../data-transfer/operations/prepare.md#source-my).
1. Prepare the infrastructure and start the data transfer:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. Create a [{{ mmy-name }} target cluster](../../../managed-mysql/operations/cluster-create.md) in any suitable configuration. In this case, the following applies:

            * The {{ MY }} version must be the same or higher than in the source cluster.

                Transferring data with {{ MY }} major version upgrade is possible but not guaranteed. For more information, see the [{{ MY }} documentation](https://dev.mysql.com/doc/refman/8.0/en/faqs-migration.html).

                You [cannot](https://dev.mysql.com/doc/refman/8.0/en/downgrading.html) perform migration while downgrading {{ MY }} version.

            * [SQL mode](../../../managed-mysql/concepts/settings-list.md#setting-sql-mode) must be the same as in the source cluster.

        1. [Prepare the target cluster](../../../data-transfer/operations/prepare.md#target-my).

        1. [Create a source endpoint](../../../data-transfer/operations/endpoint/index.md#create):

            * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `MySQL`
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlSource.title }}** → **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlSource.connection.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnectionType.on_premise.title }}`

                Specify the parameters for connecting to the source cluster.

        1. [Create a target endpoint](../../../data-transfer/operations/endpoint/index.md#create):

            * **{{ ui-key.yacloud.data-transfer.forms.label-database_type }}**: `MySQL`
            * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlTarget.title }}** → **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlTarget.connection.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnectionType.mdb_cluster_id.title }}`

                Select a target cluster from the list and specify its connection settings.

        1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the _{{ dt-type-copy-repl }}_ type that will use the created endpoints.
        1. [Activate](../../../data-transfer/operations/transfer.md#activate) your transfer.

            {% note warning %}

            Abstain from making any changes to the data schema in the source and target clusters when the data transfer is running. To learn more, see [{#T}](../../../data-transfer/operations/db-actions.md).

            {% endnote %}

    - {{ TF }} {#tf}

        1. [Prepare the source cluster](../../../data-transfer/operations/prepare.md#source-my).

        1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
        1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
        1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
        1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

        1. Download the [data-transfer-mysql-mmy.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-on-premise-mysql-to-cloud/blob/main/data-transfer-mysql-mmy.tf) configuration file to the same working directory.

            This file describes:

            * [Network](../../../vpc/concepts/network.md#network).
            * [Subnet](../../../vpc/concepts/network.md#subnet).
            * [Security group](../../../vpc/concepts/security-groups.md) and the rule required to connect to a cluster.
            * {{ mmy-name }} target cluster.
            * Source endpoint.
            * Target endpoint.
            * Transfer.

        1. Specify the following in the `data-transfer-mysql-mmy.tf` file:

            * [Source endpoint parameters](../../../data-transfer/operations/endpoint/source/mysql.md#on-premise).
            * Target cluster parameters also used as [target endpoint parameters](../../../data-transfer/operations/endpoint/target/mysql.md#managed-service):

                * `target_mysql_version`: {{ MY }} version. Must be the same or higher than in the source cluster.
                * `target_sql_mode`: [SQL mode](../../../managed-mysql/concepts/settings-list.md#setting-sql-mode). It must be the same as in the source cluster.
                * `target_db_name`: Database name.
                * `target_user` and `target_password`: Name and user password of the database owner.

        1. Make sure the {{ TF }} configuration files are correct using this command:

            ```bash
            terraform validate
            ```

            If there are any errors in the configuration files, {{ TF }} will point them out.

        1. Create the required infrastructure:

            {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

            {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

            Once created, your transfer will be activated automatically.

    {% endlist %}

## Finish data transfer {#finish-transfer}

1. Wait for the transfer status to change to {{ dt-status-repl }}.
1. Switch the source cluster to <q>read-only</q> mode and transfer the load to the target cluster.
1. On the [transfer monitoring](../../../data-transfer/operations/monitoring.md) page, wait for the **Maximum data transfer delay** metric to decrease to zero. This means that all changes that occurred in the source cluster after data was copied are transferred to the target cluster.
1. [Deactivate](../../../data-transfer/operations/transfer.md#deactivate) the transfer and wait for its status to change to {{ dt-status-stopped }}.

    For more information about transfer statuses, see [Transfer lifecycle](../../../data-transfer/concepts/transfer-lifecycle.md#statuses).

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

{% list tabs group=instructions %}

- Manually {#manual}

    * [Delete the {{ mmy-name }} cluster](../../../managed-mysql/operations/cluster-delete.md).
    * [Delete the stopped transfer](../../../data-transfer/operations/transfer.md#delete).
    * [Delete the endpoints](../../../data-transfer/operations/endpoint/index.md#delete) for both the source and target.

- {{ TF }} {#tf}

    {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

{% endlist %}


For a real example of {{ MY }} database migration using {{ data-transfer-name }}, see [Syncing MySQL data using {{ data-transfer-full-name }}](../../../tutorials/dataplatform/sync-mysql.md).

