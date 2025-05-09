---
title: How to create a network load balancer
description: Follow this guide to create a network load balancer.
---

# Creating a network load balancer

{% note info %}

Before creating a network load balancer, [create](target-group-create.md) a target group to attach to it.

{% include [type-update](../../_includes/network-load-balancer/type-update.md) %}

{% endnote %}

{% list tabs group=instructions %}

- Management console {#console}

  To create a [network load balancer](../concepts/index.md):

  1. In the [management console]({{ link-console-main }}), select the folder to create a load balancer in.
  1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_load-balancer }}**.
  1. Click **{{ ui-key.yacloud.load-balancer.network-load-balancer.button_create }}**.
  1. Enter a name. The naming requirements are as follows:

      {% include [name-format](../../_includes/name-format.md) %}

  1. Assign a public IP address to the load balancer. You can assign an address automatically or select one from the list of reserved addresses.
  1. Under **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.section_listeners }}**, add a [listener](../concepts/listener.md):
      1. Click **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_add-listener }}**.
      1. In the window that opens, set the listener parameters:

          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-name }}**.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-protocol }}**: `{{ ui-key.yacloud.common.label_tcp }}` or `{{ ui-key.yacloud.common.label_udp }}`.

            {% note info %}

            By default, the listener uses TCP. To use UDP, [submit a request to technical support]({{ link-console-support }}).

            {% endnote %}

          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-port }}** where the listener will listen for incoming traffic. The values range from `1` to `32767`.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-target-port }}** the load balancer will redirect traffic to. The values range from `1` to `32767`.

      1. Click **{{ ui-key.yacloud.common.add }}**.

  1. Under **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.section_target-groups }}** add a [target group](../concepts/target-resources.md):
      1. Click **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_add-target-group }}**.
      1. Select a target group or [create a new one](target-group-create.md):
          * In the **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_target-group-id }}** field, select ![image](../../_assets/console-icons/plus.svg) **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.button_create-target-group }}**.
          * In the window that opens, enter a target group name.
          * Add virtual machines to the target group.
          * Click **{{ ui-key.yacloud.common.create }}**.
      1. Optionally, under **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check }}**, click **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_edit-health-check }}**. In the window that opens, specify the [resource health check](../concepts/health-check.md) settings:
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-name }}**.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-protocol }}**: `{{ ui-key.yacloud.common.label_http }}` or `{{ ui-key.yacloud.common.label_tcp }}`. For health checks to use HTTP, specify the URL to check in the **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-path }}** field.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-port }}** for health checks. The values range from `1` to `32767`.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-timeout }}**: Response timeout in seconds. The possible values are from `1` to `60`. The interval must be at least 1 second longer than the response timeout.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-interval }}**: Health check interval in seconds. The values range from `1` to `60`.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-healthy-threshold }}**: Number of successful checks required to consider a VM instance ready to receive traffic.
          * **{{ ui-key.yacloud.load-balancer.network-load-balancer.label_health-check-unhealthy-threshold }}**: Number of failed checks to stop routing traffic to a VM.

      1. Click **{{ ui-key.yacloud.common.apply }}**.
  1. Click **{{ ui-key.yacloud.common.create }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command to create a network load balancer:

     ```bash
     yc load-balancer network-load-balancer create --help
     ```

  1. To create a load balancer with a [listener](../concepts/listener.md) and a [target group](../concepts/target-resources.md), run this command:

     ```bash
     yc load-balancer network-load-balancer create <load_balancer_name> \
        --listener name=<listener_name>,`
                  `port=<port>,`
                  `target-port=<target_port>,`
                  `protocol=<protocol>,`
                  `external-ip-version=<IP_address_version> \
        --target-group target-group-id=<target_group_ID>,`
                      `healthcheck-name=<health_check_name>,`
                      `healthcheck-interval=<interval_between_checks>s,`
                      `healthcheck-timeout=<health_check_timeout>s,`
                      `healthcheck-unhealthythreshold=<number_of_failed_checks_to_get_Unhealthy_status>,`
                      `healthcheck-healthythreshold=<number_of_successful_checks_to_get_Healthy_status>,`
                      `healthcheck-tcp-port=<TCP_port>,`
                      `healthcheck-http-port=<HTTP_port>,`
                      `healthcheck-http-path=<URL>
     ```

     Where:

     {% include [listener-cli-description](../../_includes/network-load-balancer/listener-cli-description.md) %}

     {% include [target-group-cli-description](../../_includes/network-load-balancer/target-group-cli-description.md) %}

- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  1. Describe the parameters of the network load balancer resource in a configuration file.

     Here is an example of the configuration file structure:

     ```hcl
     resource "yandex_lb_network_load_balancer" "foo" {
       name = "<load_balancer_name>"
       deletion_protection = "<deletion_protection>"
       listener {
         name = "<listener_name>"
         port = <port_number>
         external_address_spec {
           ip_version = "<IP_address_version>"
         }
       }
       attached_target_group {
         target_group_id = "<target_group_ID>"
         healthcheck {
           name = "<health_check_name>"
           http_options {
             port = <port_number>
             path = "<URL>"
           }
         }
       }
     }
     ```

     Where:

     * `name`: Name of the network load balancer.
     * `deletion_protection`: Deletion protection for the network load balancer. You cannot delete a load balancer with this option enabled. If load balancer deletion protection is enabled, you can still delete its listeners and target groups. The default value is `false`.
     * `listener`: Listener properties:
       * `name`: Listener name.
       * `port`: Port in the range from `1` to `32767` the network load balancer will receive incoming traffic at.
       * `external_address_spec`: Specification of the listener for the external load balancer:
         * `ip_version`: External IP address specification. Specify the IP address version, `ipv4` or `ipv6`. The default value is `ipv4`.
     * `attached_target_group`: Description of the network load balancer's target group parameters:
        * `target_group_id`: Target group ID.

          {% include [get-target-group-id](../../_includes/network-load-balancer/get-target-group-id.md) %}

        * `healthcheck`: Health check parameters. Enter a name, a port number ranging from `1` to `32767`, and a path for health checks.

     For more information about the `yandex_lb_network_load_balancer` resource parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/lb_network_load_balancer).

  1. Make sure the settings are correct.

     {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a network load balancer.

     {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

- API {#api}

  To create a new network load balancer, use the [create](../api-ref/NetworkLoadBalancer/create.md) REST API method for the [NetworkLoadBalancer](../api-ref/NetworkLoadBalancer/index.md) resource or the [NetworkLoadBalancerService/Create](../api-ref/grpc/NetworkLoadBalancer/create.md) gRPC API call.

{% endlist %}

## Examples {#examples}

### Creating a network load balancer without a listener {#without-listener}

Create a network load balancer named `test-load-balancer-1` without a listener or target group.

{% list tabs group=instructions %}

- CLI {#cli}

  Run this command:

  ```bash
  yc load-balancer network-load-balancer create test-load-balancer-1
  ```

- {{ TF }} {#tf}

  1. In the configuration file, describe the resource parameters without the `listener` and `attached_target_group` sections:

     ```hcl
     resource "yandex_lb_network_load_balancer" "foo" {
       name = "test-load-balancer-1"
       deletion_protection = "true"
     }
     ```

     For more information about the resources you can create with {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/lb_network_load_balancer).

  1. Make sure the settings are correct.

     {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a network load balancer.

     {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

- API {#api}

  Use the [create](../api-ref/NetworkLoadBalancer/create.md) API method and include the following information in the request body:

  ```api
  {
    "folderId": "<folder_ID>",
    "name": "test-load-balancer-1",
    "type": "EXTERNAL"
  }
  ```

{% endlist %}

### Creating a network load balancer with a listener and attached target group {#with-listener-and-target-group}

Create a network load balancer with a listener and attached target group with the following test specifications:

* Name: `test-load-balancer-2`.
* Listener parameters:
    * Name: `test-listener`.
    * Port: `80`.
    * Target port: `81`.
    * Protocol: `TCP`.
    * IP address version: `ipv4`.
* Target group ID: `enpu2l7q9kth********`.
* Target group resource health check parameters:
    * Name: `http`.
    * Health check interval: `2` seconds.
    * Response timeout: `1` second.
    * Unhealthy threshold: `2`.
    * Healthy threshold: `2`.
    * Port for HTTP health checks: `80`.
    * URL for health checks: `/`.

{% list tabs group=instructions %}

- CLI {#cli}

  Run this command:

  ```bash
  yc load-balancer network-load-balancer create test-load-balancer-2 \
     --listener name=test-listener,`
               `port=80,`
               `target-port=81,`
               `protocol=tcp,`
               `external-ip-version=ipv4 \
     --target-group target-group-id=enpu2l7q9kth********,`
                   `healthcheck-name=http,`
                   `healthcheck-interval=2s,`
                   `healthcheck-timeout=1s,`
                   `healthcheck-unhealthythreshold=2,`
                   `healthcheck-healthythreshold=2,`
                   `healthcheck-http-port=80,`
                   `healthcheck-http-path=/
  ```

- {{ TF }} {#tf}

  1. In the configuration file, describe the resource parameters with the `listener` and `attached_target_group` sections:

     ```hcl
     resource "yandex_lb_network_load_balancer" "foo" {
       name = "test-load-balancer-2"
       deletion_protection = "true"
       listener {
         name        = "test-listener"
         port        = 80
         target_port = 81
         protocol    = "tcp"
         external_address_spec {
           ip_version = "ipv4"
         }
       }
       attached_target_group {
         target_group_id = "enpu2l7q9kth********"
         healthcheck {
           name                = "http"
           interval            = 2
           timeout             = 1
           unhealthy_threshold = 2
           healthy_threshold   = 2
           http_options {
             port = 80
             path = "/"
           }
         }
       }
     }
     ```

     For more information about the resources you can create with {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/lb_network_load_balancer).

  1. Make sure the settings are correct.

     {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Create a network load balancer.

     {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

- API {#api}

  Use the [create](../api-ref/NetworkLoadBalancer/create.md) API method and include the following information in the request body:

  ```api
  {
    "folderId": "<folder_ID>",
    "name": "test-load-balancer-2",
    "type": "EXTERNAL",
    "listenerSpecs": [
      {
        "name": "test-listener",
        "port": "80",
        "protocol": "TCP",
        "targetPort": "81",
        "externalAddressSpec": {
          "ipVersion": "IPV4"
        }
      }
    ],
    "attachedTargetGroups": [
      {
        "targetGroupId": "b7rjtf12qdee********",
        "healthChecks": [
          {
            "name": "http",
            "interval": "2s",
            "timeout": "1s",
            "unhealthyThreshold": "2",
            "healthyThreshold": "2",
            "httpOptions": {
              "port": "80",
              "path": "/"
            }
          }
        ]
      }
    ]
  }
  ```

{% endlist %}
