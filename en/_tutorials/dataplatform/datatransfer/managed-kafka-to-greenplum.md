You can set up data transfer from a {{ mkf-full-name }} topic to {{ mgp-full-name }} using {{ data-transfer-full-name }}. Proceed as follows:

1. [Prepare the test data](#prepare-data).
1. [Set up and activate your transfer](#prepare-transfer).
1. [Test your transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).


## Required paid resources {#paid-resources}

The support cost includes:

* {{ mkf-name }} cluster fee: Using computing resources allocated to hosts (including ZooKeeper hosts) and disk space (see [{{ KF }} pricing](../../../managed-kafka/pricing.md)).
* {{ mgp-name }} cluster fee: Using computing resources allocated to hosts and disk space (see [{{ mgp-name }} pricing](../../../managed-greenplum/pricing/index.md)).
* Fee for using public IP addresses for cluster hosts (see [{{ vpc-name }} pricing](../../../vpc/pricing.md)).


## Getting started {#before-you-begin}

1. Set up your infrastructure:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. [Create a source {{ mkf-full-name }} cluster](../../../managed-kafka/operations/cluster-create.md#create-cluster) in any suitable configuration with publicly available hosts.

        1. [In the source cluster, create a topic](../../../managed-kafka/operations/cluster-topics.md#create-topic) named `sensors`.

        1. [In the source cluster, create a user](../../../managed-kafka/operations/cluster-accounts.md#create-account) named `mkf-user` with the `ACCESS_ROLE_PRODUCER` and `ACCESS_ROLE_CONSUMER` access permissions for the topic.

        1. [Create a {{ mgp-full-name }} target cluster](../../../managed-greenplum/operations/cluster-create.md#create-cluster) in any suitable configuration using the admin username (`user`) and hosts located in the public domain.

        1. Make sure that the cluster security groups are set up correctly and allow connecting to them:
            * [{{ mkf-name }}](../../../managed-kafka/operations/connect/index.md#configuring-security-groups).
            * [{{ mgp-name }}](../../../managed-greenplum/operations/connect.md#configuring-security-groups).

    - {{ TF }} {#tf}

        1. {% include [terraform-install-without-setting](../../../_includes/mdb/terraform/install-without-setting.md) %}
        1. {% include [terraform-authentication](../../../_includes/mdb/terraform/authentication.md) %}
        1. {% include [terraform-setting](../../../_includes/mdb/terraform/setting.md) %}
        1. {% include [terraform-configure-provider](../../../_includes/mdb/terraform/configure-provider.md) %}

        1. Download the [kafka-greenplum.tf](https://github.com/yandex-cloud-examples/yc-data-transfer-from-kafka-to-greenplum/blob/main/kafka-greenplum.tf) configuration file to the same working directory.

            This file describes:

            * [Networks](../../../vpc/concepts/network.md#network) and [subnets](../../../vpc/concepts/network.md#subnet) for hosting the clusters.
            * [Security groups](../../../vpc/concepts/security-groups.md) for making cluster connections.
            * {{ mkf-name }} source cluster.
            * {{ mgp-name }} target cluster.
            * Transfer.

        1. In the `kafka-greenplum.tf` file, specify user passwords and {{ KF }} and {{ GP }} versions.
        1. Make sure the {{ TF }} configuration files are correct using this command:

            ```bash
            terraform validate
            ```

            If there are any errors in the configuration files, {{ TF }} will point them out.

        1. Create the required infrastructure:

            {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

            {% include [explore-resources](../../../_includes/mdb/terraform/explore-resources.md) %}

    {% endlist %}

1. Install the utilities:

    * [kafkacat](https://github.com/edenhill/kcat) to read and write data to {{ KF }} topics.

        ```bash
        sudo apt update && sudo apt install --yes kafkacat
        ```

        Check that you can use it to [connect to the {{ mkf-name }} source cluster over SSL](../../../managed-kafka/operations/connect/clients.md#bash-zsh).

    * [jq](https://stedolan.github.io/jq/) for JSON file stream processing.

        ```bash
        sudo apt update && sudo apt-get install --yes jq

## Prepare the test data {#prepare-data}

Let's assume the {{ KF }} `sensors` topic in the source cluster receives data from car sensors in JSON format.

Create a file named `sample.json` with test data on your working instance:

{% cut "sample.json" %}

```json
{
    "device_id": "iv9a94th6rzt********",
    "datetime": "2020-06-05 17:27:00",
    "latitude": 55.70329032,
    "longitude": 37.65472196,
    "altitude": 427.5,
    "speed": 0,
    "battery_voltage": 23.5,
    "cabin_temperature": 17,
    "fuel_level": null
}
```

{% endcut %}

## Set up and activate the transfer {#prepare-transfer}

1. [Create a source endpoint](../../../data-transfer/operations/endpoint/source/kafka.md) with the `{{ KF }}` type and specify the following items for it:

    * **{{ ui-key.yc-data-transfer.data-transfer.console.form.kafka.console.form.kafka.KafkaSourceConnection.topic_name.title }}**: `sensors`.
    * `json` conversion rules. In the **{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.ConvertRecordOptions.data_schema.title }}** field, select `{{ ui-key.yc-data-transfer.data-transfer.console.form.common.console.form.common.DataSchema.json_fields.title }}` and copy and paste the following field specification into the form that opens:

    {% cut "sensors-specification" %}

    ```json
    [
        {
            "name": "device_id",
            "type": "utf8",
            "key": true
        },
        {
            "name": "datetime",
            "type": "utf8"
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
            "type": "double"
        },
        {
            "name": "cabin_temperature",
            "type": "uint16"
        },
        {
            "name": "fuel_level",
            "type": "uint16"
        }
    ]
    ```

    {% endcut %}

1. [Create a target endpoint](../../../data-transfer/operations/endpoint/target/greenplum.md) of the `{{ GP }}` type and put `user` for username.
1. Create and activate the transfer:

    {% list tabs group=instructions %}

    - Manually {#manual}

        1. [Create a transfer](../../../data-transfer/operations/transfer.md#create) of the _{{ dt-type-repl }}_ type that will use the created endpoints.
        1. [Activate the transfer](../../../data-transfer/operations/transfer.md#activate) and wait until its status switches to {{ dt-status-repl }}.

    - {{ TF }} {#tf}

        1. In the `kafka-greenplum.tf` file, specify these variables:

            * `kf_source_endpoint_id`: ID of the source endpoint.
            * `gp_target_endpoint_id`: Target endpoint ID.
            * `transfer_enabled`: `1` to create a transfer.

        1. Make sure the {{ TF }} configuration files are correct using this command:

            ```bash
            terraform validate
            ```

            If there are any errors in the configuration files, {{ TF }} will point them out.

        1. Create the required infrastructure:

            {% include [terraform-apply](../../../_includes/mdb/terraform/apply.md) %}

        1. The transfer will be activated automatically. Wait for its status to change to {{ dt-status-repl }}.

    {% endlist %}

## Test your transfer {#verify-transfer}

Make sure the data from the topic in the source {{ mkf-name }} cluster is being moved to the {{ mgp-name }} database:

1. Send data from the `sample.json` file to the {{ mkf-name }} `sensors` topic using `jq` and `kafkacat`:

    ```bash
    jq -rc . sample.json | kafkacat -P \
        -b <broker_host_FQDN>:9091 \
        -t sensors \
        -k key \
        -X security.protocol=SASL_SSL \
        -X sasl.mechanisms=SCRAM-SHA-512 \
        -X sasl.username="<username_in_source_cluster>" \
        -X sasl.password="<user_password_in_source_cluster>" \
        -X ssl.ca.location={{ crt-local-dir }}{{ crt-local-file }} -Z
    ```

    To learn more about setting up an SSL certificate and working with `kafkacat`, see [{#T}](../../../managed-kafka/operations/connect/clients.md).

1. Make sure the data from the source {{ mkf-name }} cluster has been moved to the {{ mgp-name }} database:

    1. [Connect to the {{ mgp-name }} database](../../../managed-greenplum/operations/connect.md).
    1. Check that the database contains a table named `sensors` with the test data from the topic:

        ```sql
        SELECT * FROM public.sensors;
        ```

## Delete the resources you created {#clear-out}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

* Make sure the transfer has the {{ dt-status-finished }} status and [delete](../../../data-transfer/operations/transfer.md#delete) it.
* [Delete both the source endpoint and the target endpoint](../../../data-transfer/operations/endpoint/index.md#delete).
* Delete the clusters:

    {% list tabs group=instructions %}

    - Manually {#manual}

        * [{{ mkf-name }}](../../../managed-kafka/operations/cluster-delete.md).
        * [{{ mgp-name }}](../../../managed-greenplum/operations/cluster-delete.md).

    - {{ TF }} {#tf}

        {% include [terraform-clear-out](../../../_includes/mdb/terraform/clear-out.md) %}

    {% endlist %}
