---
title: How to delete a static public IP address
description: Follow this guide to delete a static public IP address.
---

# Deleting a static public IP address

{% note alert %}

Before deleting a [static IP address](../concepts/address.md), you should release it by deleting the resource it is assigned to.
If deletion protection is enabled for the IP address, disable it first.
You cannot restore an address after it is deleted.

{% endnote %}

{% list tabs group=instructions %}

- Management console {#console}

   1. In the [management console]({{ link-console-main }}), go to the folder where you need to delete a static address.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}**.
   1. In the left-hand panel, select ![image](../../_assets/console-icons/map-pin.svg) **{{ ui-key.yacloud.vpc.switch_addresses }}**.
   1. Click ![image](../../_assets/console-icons/ellipsis.svg) next to the IP address you need:
      * Select ![image](../../_assets/console-icons/trash-bin.svg) **{{ ui-key.yacloud.common.delete }}**.
      * (Optional) Select ![image](../../_assets/console-icons/lock-open.svg) **{{ ui-key.yacloud.vpc.addresses.button_action-disable-protection }}** if protection is enabled and click **{{ ui-key.yacloud.vpc.addresses.popup-confirm_button_disable-protection }}**.
   1. In the window that opens, confirm by clicking **{{ ui-key.yacloud.common.delete }}**.

- CLI {#cli}

   {% include [include](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. See the description of the CLI command for deleting an address:

      ```bash
      yc vpc address delete --help
      ```

   1. Get a list of addresses in the default folder:

      ```bash
      yc vpc address list
      ```

      Result:

      ```text
      +----------------------+------+----------------+----------+-------+
      |          ID          | NAME |    ADDRESS     | RESERVED | USED  |
      +----------------------+------+----------------+----------+-------+
      | e9b6un9gkso6******** |      | 178.154.253.52 | true     | false |
      +----------------------+------+----------------+----------+-------+
      ```

   1. Delete the address by specifying its ID:

      ```bash
      yc vpc address delete e9b6un9gkso6********
      ```

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To delete a static public IP address created with {{ TF }}:

  1. Open the {{ TF }} configuration file and delete the fragment with the static public IP address description.

     Example of a static public IP address description in the {{ TF }} configuration:

     ```hcl
     ...
     resource "yandex_vpc_address" "addr" {
       name = "exampleAddress"
       external_ipv4_address {
         zone_id = "{{ region-id }}-a"
       }
     }
     ...
     ```

  1. In the command line, go to the directory with the {{ TF }} configuration file.

  1. Check the configuration using this command:

     ```
     terraform validate
     ```

     If the configuration is correct, you will get this message:

     ```
     Success! The configuration is valid.
     ```

  1. Run this command:

     ```
     terraform plan
     ```

     The terminal will display a list of resources with their parameters. No changes will be made at this step. If the configuration contains any errors, {{ TF }} will point them out.

  1. Apply the configuration changes:

     ```
     terraform apply
     ```

  1. Confirm the changes: type `yes` into the terminal and press **Enter**.

     You can check the update using the [management console]({{ link-console-main }}) or this [CLI](../../cli/quickstart.md) command:

     ```
     yc vpc address list
     ```

- API {#api}

  To delete a [static IP address](../concepts/address.md), use the [delete](../api-ref/Address/delete.md) REST API method for the [Address](../api-ref/Address/index.md) resource or the [AddressService/Delete](../api-ref/grpc/Address/delete.md) gRPC API call, and provide the ID of the IP address you are deleting in the `addressId` request parameter.

  {% include [get-address-id](../../_includes/vpc/get-adress-id.md) %}

  {% include [get-catalog-id](../../_includes/get-catalog-id.md) %}

{% endlist %}
