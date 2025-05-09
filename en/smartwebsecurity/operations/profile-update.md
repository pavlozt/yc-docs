---
title: How to edit basic parameters of a {{ sws-full-name }} security profile
description: Follow this guide to edit basic settings in a {{ sws-full-name }} security profile.
---

# Editing basic parameters in a security profile

{% list tabs group=instructions %}

- Management console {#console}

  1. In the [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) containing the [security profile](../concepts/profiles.md).
  1. From the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_smartwebsecurity }}**.
  1. In the row with the profile you need, click ![options](../../_assets/console-icons/ellipsis.svg) and select **{{ ui-key.yacloud.smart-web-security.overview.action_edit-profile }}**.
  1. In the window that opens, edit the following parameters:

      * **{{ ui-key.yacloud.common.name }}**.
      * **{{ ui-key.yacloud.common.description }}**.
      * [**{{ ui-key.yacloud.component.label-set.label_labels }}**](../../resource-manager/concepts/labels.md). To add a label, click **{{ ui-key.yacloud.component.label-set.button_add-label }}**.
      * **{{ ui-key.yacloud.smart-web-security.form.label_default-action }}**: `{{ ui-key.yacloud.smart-web-security.form.label_action-deny }}` or `{{ ui-key.yacloud.smart-web-security.form.label_action-allow }}`.
      * **{{ ui-key.yacloud.smart-web-security.form.label_arl-profile }}**: Select or create an [ARL profile](../concepts/arl.md).
      * Select or create a [{{ captcha-name }}](../../smartcaptcha/) to verify suspicious requests.
      * {% include [profile-inspect-request](../../_includes/smartwebsecurity/profile-inspect-request.md) %}
  1. Optionally, enable or disable the use of HTTP request information to improve machine learning models under **{{ ui-key.yacloud.component.disallow-data-processing.title_ml-model-training }}**.
  1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  1. View the description of the [CLI](../../cli/quickstart.md) command for editing [security profile](../concepts/profiles.md) basic parameters:

     ```bash
     yc smartwebsecurity security-profile update --help
     ```

  1. To view a list of current security profiles in the default folder, run this command:

     {% include [security-profile-list-command](../../_includes/smartwebsecurity/security-profile-list-command.md) %}

  1. To edit basic parameters for a security profile, run this command:

     ```bash
     yc smartwebsecurity security-profile update \
        --name <security_profile_name> \
        --new-name <new_security_profile_name> \
        --description "<profile_description>" \
        --labels <label_1_key>=<label_1_value>,<label_2_key>=<label_2_value>,...,<label_n_key>=<label_n_value> \
        --default-action <action> \
        --captcha-id <captcha_ID> \
        --security-rules-file <path_to_file_with_security_rules>
     ```

     Where:

     * `--name`: Security profile name. This is a required parameter. Instead of the security profile name, you can provide its ID in the `--id` parameter.
     * `--new-name`: New name for the security profile. This is an optional parameter if the profile name remains unchanged.
     * `--description`: Text description of the security profile. This is an optional parameter.
     * `--labels`: List of [labels](../../resource-manager/concepts/labels.md) to add to the profile in `KEY=VALUE` format. This is an optional parameter, e.g., `--labels foo=baz,bar=baz'`.
     * `--default-action`: Action to apply to traffic not covered by the other rules. This is an optional parameter. The default value is `allow`, which allows all requests to {{ sws-full-name }}. To block requests, set the parameter to `deny`.
     * `--captcha-id`: ID of the CAPTCHA in [{{ captcha-name }}](../../smartcaptcha/) to verify suspicious requests. This is an optional parameter.
     * `--security-rules-file`: Path to the [YAML](https://en.wikipedia.org/wiki/YAML) file with security rule description. This is an optional parameter. For example:

         {% include [profile-create-yaml-example](../../_includes/smartwebsecurity/profile-create-yaml-example.md) %}

     Result:

     ```text
     id: fev6q4qqnn2q********
     folder_id: b1g07hj5r6i********
     cloud_id: b1gia87mbaom********
     name: my-sample-profile
     new-name: my-update-profile
     description: "my update description"
     labels: label1=value1,label2=value2
     default_action: DENY
     created_at: "2024-07-25T19:21:05.039610Z"
     ```

  For more information about the `yc smartwebsecurity security-profile update` command, see the [CLI reference](../../cli/cli-ref/smartwebsecurity/cli-ref/security-profile/update.md).


- {{ TF }} {#tf}

  {% include [terraform-definition](../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../_includes/terraform-install.md) %}

  To update the parameters of a {{ sws-full-name }} security profile created using {{ TF }}:

  1. Open the {{ TF }} configuration file and edit the fragment with the profile description.

     {% cut "Example of security profile description in the {{ TF }} configuration" %}
     
     ```hcl
     resource "yandex_sws_security_profile" "demo-profile-simple" {
       name                             = "<security_profile_name>"
       default_action                   = "DENY"
       captcha_id                       = "<captcha_ID>"
       advanced_rate_limiter_profile_id = "<ARL_profile_ID>"

       # Smart Protection rule
       security_rule {
         name     = "smart-protection"
         priority = 99999

         smart_protection {
           mode = "API"
         }
       }

       #Basic rule
       security_rule {
         name = "base-rule-geo"
         priority = 100000
         rule_condition {
           action = "ALLOW"
           condition {
             source_ip {
               geo_ip_match {
                 locations = ["ru", "kz"]
               }
             }
           }
         }
       }

       # WAF profile rule
       security_rule {
         name     = "waf"
         priority = 88888

         waf {
           mode           = "API"
           waf_profile_id = "<WAF_profile_ID>"
         }
       }
     }
     ```

     {% endcut %}

      For more information about the `yandex_sws_security_profile` parameters in {{ TF }}, see the [relevant {{ TF }} article]({{ tf-provider-resources-link }}/sws_security_profile).

  1. Apply the changes:

       {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

  You can check the resources' updates using the [management console]({{ link-console-main }}) or this [CLI](../../cli/) command:

  ```bash
  yc smartwebsecurity security-profile get <security_profile_ID>
  ```

- API {#api}

  {% include [api-profile-update](../../_includes/smartwebsecurity/api-profile-update.md) %}

{% endlist %}

### See also {#see-also}

* [{#T}](rule-add.md)
* [{#T}](rule-update.md)
* [{#T}](host-connect.md)
* [{#T}](profile-delete.md)
