---
title: How to delete a connector
description: Follow this guide to delete a connector.
---

# Deleting a connector

{% note info %}

You cannot delete a connector if deletion protection is enabled. Before deleting the rule, [disable](update.md) the protection.

{% endnote %}

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the folder where you want to delete a [connector](../../../concepts/eventrouter/connector.md).
  1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_serverless-integrations }}**.
  1. In the left-hand panel, click ![image](../../../../_assets/console-icons/object-align-center-vertical.svg) **{{ ui-key.yacloud.serverless-event-router.label_service }}**.
  1. Select the [bus](../../../concepts/eventrouter/bus.md) you need.
  1. Navigate to the ![image](../../../../_assets/console-icons/broadcast-signal.svg) **{{ ui-key.yacloud.serverless-event-router.label_connectors }}** tab.
  1. In the line with the connector you need, click ![image](../../../../_assets/console-icons/ellipsis.svg) and select ![image](../../../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.common.delete }}**.
  1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

- CLI {#cli}

  {% include [cli-install](../../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../../_includes/default-catalogue.md) %}

  1. View the description of the CLI command for deleting a [connector](../../../concepts/eventrouter/connector.md):

      ```bash
      yc serverless eventrouter connector delete --help
      ```

  1. {% include [get-connectors-list](../../../../_includes/serverless-integrations/get-connectors-list.md) %}
  1. Delete a connector:

      ```bash
      yc serverless eventrouter connector delete <connector_name_or_ID>
      ```

      Result:

      ```text
      done (1s)
      ```

- {{ TF }} {#tf}

  {% include [terraform-definition](../../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../../../_includes/terraform-install.md) %}

  To delete a [connector](../../../concepts/eventrouter/connector.md):

  1. Open the {{ TF }} configuration file and delete the fragment with the `yandex_serverless_eventrouter_connector` resource description:

      ```hcl
      resource "yandex_serverless_eventrouter_connector" "example_connector" {
        bus_id              = "<bus_ID>"
        name                = "<connector_name>"
        description         = "<connector_description>"
        deletion_protection = <true|false>

        labels = {
          <key_1> = "<value_1>"
          <key_2> = "<value_2>"
          ...
          <key_n> = "<value_n>"
        }
        ...
      }
      ```

  1. Apply the changes:

      {% include [terraform-validate-plan-apply](../../../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

      You can check the updates using the [management console]({{ link-console-main }}) or this [CLI](../../../../cli/) command:

      ```bash
      yc serverless eventrouter connector list
      ```

- API {#api}

  To delete a [connector](../../../concepts/eventrouter/connector.md), use the [Delete](../../../../serverless-integrations/eventrouter/api-ref/Connector/delete.md) REST API method for the [Connector](../../../../serverless-integrations/eventrouter/api-ref/Connector/index.md) resource or the [Connector/Delete](../../../../serverless-integrations/eventrouter/api-ref/grpc/Connector/delete.md) gRPC API call.

{% endlist %}