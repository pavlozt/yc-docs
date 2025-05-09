# Virtual environment configuration requirements

## 3. Secure virtual environment configuration {#virtualenv-safe-config}


This section recommends customers on how to configure secure {{ yandex-cloud }} services and employ additional virtual environment data protection tools.

### Overview {#general}

#### 3.1 A serial console is either used under control or not used {#serial-console}

On VMs, access to the serial console is disabled by default. For risks of using the serial console, see [{#T}](../../../compute/operations/serial-console/index.md) in the {{ compute-full-name }} documentation.

When working with a serial console:

* Make sure that critical data is not output to the serial console.
* If you enable SSH access to the serial console, make sure that both the credentials management and the password used for logging in to the operating system locally are compliant with regulator standards. For example, in an infrastructure for storing payment card data, passwords must meet the PCI DSS requirements: they must contain both letters and numbers, be at least 7 characters long, and be changed at least once every 90 days.

{% note info %}

According to the PCI DSS standard, access to a VM via a serial console is considered "non-console", and {{ yandex-cloud }} uses TLS encryption for it.

{% endnote %}

We do not recommend using access to the serial console unless it is absolutely necessary.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the folder to check the VMs in.
  1. In the list of services, select **{{ compute-name }}**.
  1. Open the settings of all the necessary VMs.
  1. Under **Access**, find the **Additional** parameter.
  1. **Serial console access** must be disabled.
  1. If it is disabled for all the VMs, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:
    
     ```bash
     yc organization-manager organization list
     ```

  1. Find the VM with access to the serial console enabled:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for VM_ID in $(yc compute instance list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do echo "VM_ID: " && yc compute instance get --id=$VM_ID --full --format=json | jq -r '. | select(.metadata."serial-port-enable"=="1")' | jq -r '.id' && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done;
     done
     ```

  1. If an empty value is set in VM_ID next to FOLDER_ID, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

If the serial console should not be used on the VM, disable it.

#### 3.2 A benchmark image is used for VM deployment {#standard-image}

When deploying virtual machines, we recommend:

* Preparing a VM image whose system settings correspond to your information security policy. You can create an image using Packer. See [{#T}](../../../tutorials/infrastructure-management/packer-quickstart.md).
* Use this image to create a virtual machine or [instance group](../../../compute/concepts/instance-groups/index.md).
* Look up the virtual machine's information to check that it was created using this image.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the folder to check the VMs in.
  1. In the list of services, select **{{ compute-name }}**.
  1. Go to the **Disks** tab.
  1. Open the settings of all disks.
  1. Under **Source**, find the **Identifier** parameter.
  1. If every disk displays the ID of your benchmark image, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for the VM disks that do not contain the ID of your benchmark image:

     ```bash
     export ORG_ID=<organization ID>
     export IMAGE_ID=<ID of your benchmark image>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DISK_ID in $(yc compute disk list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do echo "DISK_ID: " && yc compute disk get --id=$DISK_ID \
     --format=json | jq -r --arg IMAGE_ID $IMAGE_ID '. | select(."source_image_id"==$IMAGE_ID | not)' | jq -r '.id' && echo "FOLDER_ID: " $FOLDER_ID && echo "-----"
     done;
     done;
     done
     ```

  1. If an empty value is set in DISK_ID next to FOLDER_ID, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

1. Find out why these VM disks use an image different from the benchmark one.
1. Recreate the VMs with the appropriate image.

#### 3.3 {{ TF }} is used in accordance with best information security practices {#tf-using}

With {{ TF }}, you can manage a cloud infrastructure using configuration files. If you change the files, {{ TF }} will automatically detect which part of your configuration is already deployed, and what should be added or removed. For more information, see [{#T}](../../../tutorials/infrastructure-management/terraform-quickstart.md).

We do not recommend using private information in {{ TF }} configuration files, such as passwords, secrets, personal data, payment system data, etc. Instead, you should use services to store and use secrets in the configuration, such as: [HashiCorp Vault](/marketplace/products/yc/vault-yckms) from {{ marketplace-name }} or [Lockbox](/services/lockbox) (to transfer secrets to the target object without using {{ TF }}).

If you still need to enter private information in the configuration, take the following security measures:

* Specify [sensitive = true](https://www.terraform.io/docs/language/values/outputs.html#sensitive-suppressing-values-in-cli-output) for private information to prevent outputting it to the console when running the `terraform plan` and `terraform apply` commands.
* Use [terraformremotestate](https://www.terraform.io/docs/language/state/remote.html). We recommend [uploading](../../../tutorials/infrastructure-management/terraform-state-storage.md) a {{ TF }} state to {{ objstorage-name }} and [setting up](https://github.com/yandex-cloud-examples/yc-terraform-state) configuration locks using {{ ydb-name }} to prevent simultaneous edits by administrators.
* Use the [mechanism for transferring secrets to {{ TF }} via env](https://www.terraform.io/docs/cli/config/environment-variables.html#tf_var_name) instead of plain text or use built-in KeyManagementService features for [encrypting data in {{ TF }}](../../../kms/tutorials/terraform-secret.md) using a separate file with private data. [Learn more about this technique](https://blog.gruntwork.io/a-comprehensive-guide-to-managing-secrets-in-your-terraform-code-1d586955ace1#3073).

For more information about {{ objstorage-name }} security, see [{{ objstorage-name }}](#objstorage) below.

{% note info %}

When a configuration is deployed, you can delete the configuration file with private data.

{% endnote %}

Scan your Terraform manifests using [Checkov](https://github.com/bridgecrewio/checkov) with {{ yandex-cloud }} support.

* [Example: Scanning .tf files with Checkov](https://github.com/yandex-cloud/yc-solution-library-for-security/tree/master/terraform-sec/checkov-yc)
* [Example: Storing a {{ TF }} state in {{ objstorage-name }}](https://github.com/yandex-cloud-examples/yc-terraform-state).

{% list tabs group=instructions %}

- Manual check {#manual}

  Collect data about using the {{ TF }} best security practices from different points.

{% endlist %}

#### 3.4. Integrity control is performed {#integrity-control}

##### 3.4.1 File integrity control {#file-integrity-control}

Numerous information security standards require integrity control of critical files. To do this, you can use free host-based solutions:

* [Wazuh](https://documentation.wazuh.com/current/learning-wazuh/detect-fs-changes.html)
* [Osquery](https://osquery.readthedocs.io/en/stable/deployment/file-integrity-monitoring/)

Paid solutions are also available in Yandex Cloud marketplace, such as [Kaspersky Security](/marketplace/products/kaspersky/kaspersky-linux-hybrid-cloud-security-byol).

{% list tabs group=instructions %}

- Manual check {#manual}

  Collect data about using integrity control from different points.

{% endlist %}

##### 3.4.2 Integrity control of a VM runtime environment {#vm-integrity-control}

To control a VM's runtime environment (e.g., to enable access from the VM to a secure repository only when running it in the YC CLI cloud), you can use the [identity document](../../../compute/concepts/metadata/identity-document.md) mechanism. When you create a VM, an identity document that stores information about the VM is generated. It contains the IDs of the VM, [{{ marketplace-full-name }}](/marketplace) product, disk image, etc. This document is signed with a {{ yandex-cloud }} certificate. The document and its [signature](../../../compute/concepts/metadata/identity-document.md#signed-identity-documents) are available to VM processes through the metadata service. Thus, the processes identify the VM runtime environment, disk image, etc., to restrict access to the resources under monitoring.

{% list tabs group=instructions %}

- Manual check {#manual}

  Make sure that critical VMs have identity documents signed.

{% endlist %}

#### 3.5 Principles of protection against side-channel attacks are followed {#side-chanel-protection}

To ensure the best protection against CPU level side-channel attacks (such as Spectre or Meltdown):

* Use full-core virtual machines (instances with a CPU share of 100%).
* Install updates for your operating system and kernel that ensure side-channel attack protection (for example, [Kernelpage-tableisolation for Linux](https://en.wikipedia.org/wiki/Kernel_page-table_isolation) or applications built using [Retpoline](https://en.wikipedia.org/wiki/Spectre_%28security_vulnerability%29)).

We recommend that you use [dedicated hosts](../../../compute/concepts/dedicated-host.md) for the most security-critical resources.

[Learn more](https://www.youtube.com/watch?v=VSP_cp6vDQQ&list=PL1x4ET76A10a9Jr6six11s0kRxeQ3fgom&index=17) about side-channel attack protection in cloud environments.

### {{ objstorage-full-name }} {#objstorage}

#### 3.6 No public access to a {{ objstorage-name }} bucket is allowed {#bucket-access}

We recommend assigning minimum roles for a bucket using {{ iam-short-name }} and supplementing or itemizing them using a bucket policy (for example, to restrict access to the bucket by IP, grant granular permissions for objects, and so on).

Access to {{ objstorage-name }} resources is verified at three levels:

* [{{ iam-short-name }} verification](../../../iam/concepts)
* [Bucket policy](../../../storage/concepts/policy.md)
* [Access Control Lists (ACLs)](../../../storage/concepts/acl.md)

**Verification procedure:**

1. If a request passes the {{ iam-short-name }} check, the next step is the bucket policy check.
1. Bucket policy rules are checked in the following order:

   1. If the request meets at least one of the Deny rules, access is denied.
   1. If the request meets at least one of the Allow rules, access is allowed.
   1. If the request does not meet any of the rules, access is denied.

1. If the request fails the {{ iam-short-name }} or bucket policy check, access verification is performed based on an object's ACL.

In {{ iam-short-name }}, a bucket inherits the same access permissions as those of the folder and cloud where it is located. For more information, see [{#T}](../../../storage/concepts/acl.md#inheritance). Therefore, we recommend that you only assign the minimum required roles to certain buckets or objects in {{ objstorage-name }}.

Bucket policies are used for additional data protection, for example, to restrict access to a bucket by IP, issue granular permissions to objects, and so on.

With ACLs, you can grant access to an object bypassing {{ iam-short-name }} verification and bucket policies. We recommend setting strict ACLs for buckets.

 [Example of a secure {{ objstorage-name }} configuration: {{ TF }}](https://github.com/yandex-cloud-examples/yc-s3-secure-bucket)

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the buckets in.
  1. From the list of services, select **{{ objstorage-name }}**.
  1. Click the three dots next to each bucket and check its ACL for `allUsers` and `allAuthenticatedUsers`.
  1. Open the bucket and check the ACL of each of its objects for `allUsers` and `allAuthenticatedUsers`.
  1. Check that the object **Read access** section has the **Public** parameter enabled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. [Configure](../../../storage/tools/aws-cli.md) the AWS CLI to work with a cloud.
  1. Run the command below to check the bucket ACL for `allUsers` and `allAuthenticatedUsers`:

     ```bash
     aws --endpoint-url=https://{{ s3-storage-host }} s3api get-bucket-acl  <name of your bucket>
     ```

{% endlist %}

**Guides and solutions to use:** 

If public access is enabled, [remove](../../../iam/operations/roles/revoke.md) it or perform access control (grant permission to access public data consciously).

#### 3.7 {{ objstorage-name }} uses Bucket Policies {#bucket-policy}

[Bucket policies](../../../storage/concepts/policy.md) set permissions for actions with buckets, objects, and object groups. A policy applies when a user makes a request to a resource. As a result, the request is either executed or rejected.

Bucket Policy [examples](../../../storage/concepts/policy.md#config-examples):

* Policy that only enables object download from a specified range of IP addresses.
* Policy that prohibits downloading objects from the specified IP address.
* Policy that provides different users with full access only to certain folders, with each user being able to access their own.
* Policy that gives each user and service account full access to a folder named the same as the user ID or service account ID.

We recommend making sure that your {{ objstorage-name }} bucket uses at least one policy.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the bucket policies in.
  1. In the list of services, select {{ objstorage-name }}.
  1. Go to **Bucket policy**.
  1. Make sure that at least one policy is enabled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. [Configure](../../../storage/tools/aws-cli.md) the AWS CLI to work with a cloud.
  1. Run the command below to check the bucket ACL for `allUsers` and `allAuthenticatedUsers`:

     ```bash
     aws --endpoint-url=https://{{ s3-storage-host }} s3api get-bucket-policy --bucket <name of your bucket>
     ```

{% endlist %}

**Guides and solutions to use:**

[Enable](../../../storage/concepts/policy.md#config-examples) the required policy.

#### 3.8 In {{ objstorage-name }}, the Object locks feature is enabled {#object-lock}

When processing critical data in buckets, you must ensure that data is protected from deletion and that versions are backed up. This can be achieved by versioning and lifecycle management mechanisms, as well as by using object locks.

Bucket versioning allows keeping a version history of an object. Each version is a complete copy of the object and occupies space in {{ objstorage-name }}. Using version control protects your data from both accidental user actions and application faults.

If you delete or modify an object with versioning enabled, a new version of the object with a new ID is effectively created. In the case of deletion, the object becomes unreadable, but its version is kept and can be restored.

For more information about setting up versioning, see the {{ objstorage-name }} documentation, [Bucket versioning](../../../storage/concepts/versioning.md).

For more information about lifecycles, see the {{ objstorage-name }} documentation, [Bucket object lifecycles](../../../storage/concepts/lifecycles.md) and [Bucket object lifecycle configuration](../../../storage/s3/api-ref/lifecycles/xml-config.md).

In addition, to protect object versions against deletion, use [object locks](../../../storage/concepts/object-lock.md). For more information about object lock types and how to enable them, see the documentation.

The storage period of critical data in a bucket is determined by the customer's information security requirements and the information security standards. For example, the PCI DSS standard states that audit logs should be stored for at least one year and be available online for at least three months.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the buckets in.
  1. From the list of services, select **{{ objstorage-name }}**.
  1. Open the settings of all buckets.
  1. Go to the **Versioning** tab and make sure it is enabled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. [Configure](../../../storage/tools/aws-cli.md) the AWS CLI to work with a cloud.
  1. Run the command below to check whether versioning is enabled:

     ```bash
     aws --endpoint https://{{ s3-storage-host }} \
     s3api get-bucket-versioning \
     --bucket <name of your bucket>
     ```

  1. Run the command below to check whether versioning is enabled:

     ```bash
     aws --endpoint-url=https://{{ s3-storage-host }}/ \
     s3api get-object-lock-configuration \
     --bucket <name of your bucket>
     ```

{% endlist %}

**Guides and solutions to use:**

If public access is enabled, remove it or use access control (by only enabling it when necessary and if approved).

#### 3.9 In {{ objstorage-name }}, logging of actions with buckets is enabled {#bucket-logs}

When using {{ objstorage-name }} to store critical data, be sure to enable logging of actions with buckets. For more information, see the {{ objstorage-name }} documentation, [Logging actions with a bucket](../../../storage/concepts/server-logs.md).

This makes sure that data-plane logs with the following objects are written: PUT, DELETE, GET, POST, OPTIONS, HEAD.

In a similar way, you can request [writing](../../../audit-trails/concepts/events.md#objstorage) these logs to {{ at-short-name }}, except reads. In the future, all these logs will be written to {{ at-short-name }}.

You can also analyze {{ objstorage-name }} logs in {{ datalens-short-name }}. For more information, see [Analyzing {{ objstorage-name }} logs using {{ datalens-short-name }}](../../../tutorials/datalens/storage-logs-analysis.md).

**Guides and solutions to use:**

You can only use {{ TF }}/API to check if logging is enabled by following the [instructions](../../../storage/operations/buckets/enable-logging.md).

#### 3.10 In {{ objstorage-name }}, Cross-Origin Resource Sharing (CORS) is set up {#cors}

If you need [cross-domain requests](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) to objects in buckets, you should configure the Cross-Origin Resource Sharing (CORS) policy in accordance with your corporate information security requirements. For more information, see the {{ objstorage-name }} documentation, [CORS configuration of buckets](../../../storage/s3/api-ref/cors/xml-config.md).

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the buckets in.
  1. From the list of services, select **{{ objstorage-name }}**.
  1. Open the settings of all buckets.
  1. Go to the **CORS** tab and make sure that the configuration is set up. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

[Set up](../../../storage/s3/api-ref/cors/xml-config.md) CORS. 

#### 3.11 Use {{ sts-full-name }} to get access keys to {{ objstorage-name }} {#use-sts-for-storage-keys}

[{{ sts-full-name }}](../../../iam/concepts/authorization/sts.md): {{ iam-full-name }} component to get _temporary access keys_ compatible with [AWS S3 API](../../../storage/s3/index.md).

Temporary access keys as an authentication method are only supported in [{{ objstorage-full-name }}](../../../storage/index.yaml).

With temporary keys, you can set up granular access to [buckets](../../../storage/concepts/bucket.md) for multiple users with a single [service account](../../../iam/concepts/users/service-accounts.md). The service account permissions must include all the permissions you want to grant using temporary keys.

A temporary access key is created based on a [static key](../../../iam/concepts/authorization/access-key.md), but, unlike it, it has a limited lifetime and access permissions. Access permissions and lifetime are set for each temporary key individually. The maximum key lifetime is 12 hours.

To set up access permissions for the key, you need an [access policy](../../../storage/security/policy.md) in JSON format based on [this schema](../../../storage/s3/api-ref/policy/scheme.md).

Temporary {{ sts-name }} keys inherit the access permissions of the service account but are limited by the access policy. If you set up a temporary key's access policy to allow operations not allowed for the service account, such operations will not be performed.

**Guides and solutions to use:**

[Create](../../../iam/operations/sa/create-sts-key.md) a temporary access key using {{ sts-name }}.

#### 3.12 Pre-signed URLs are generated for isolated cases of access to specific objects in {{ objstorage-name }} private buckets {#use-presigned-urls}

{{ objstorage-name }} incorporates several access management mechanisms. To learn how these mechanisms interact, see [{#T}](../../../storage/security/overview.md).

With pre-signed URLs, any web user can perform various operations in Object Storage, such as:
* Downloading an object
* Uploading an object
* Creating a bucket

[A pre-signed URL](../../../storage/concepts/pre-signed-urls.md) is a URL containing request authorization data in its parameters. Pre-signed URLs can be created by users with static access keys.

We recommend using pre-signed URLs to users who are not authorized in the [cloud](../../../resource-manager/concepts/resources-hierarchy.md#cloud) but need access to specific objects in the bucket. This way you follow the principle of least privilege and avoid opening access to all the objects in the bucket.

**Guides and solutions to use:**

[Create](../../../storage/concepts/pre-signed-urls.md#creating-presigned-url) a pre-signed URL and communicate it to the user.

### Managed Services for Databases {#managed-databases}

#### 3.13 A security group is assigned in managed databases {#db-security-group}

We recommend prohibiting internet access to databases that contain critical data, in particular PCI DSS data or private data. Configure security groups to only allow connections to the DBMS from particular IP addresses. To do this, follow the steps in [Creating a security group](../../../vpc/operations/security-group-create.md). You can specify a security group in the cluster settings or when creating the cluster in the network settings section.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the databases in.
  1. In the list of services, select a service or services with managed databases.
  1. In the object settings, find the **Security group** parameter and make sure that at least one security group is assigned.
  1. If the parameters of each object have at least one security group set, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. Run the command below to search for Managed PostgreSQL DBs with no SG:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-postgresql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-postgresql cluster get --id=$DB_ID --format=json | jq -r '. | select(.security_group_ids | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. The output should return an empty string. Otherwise, proceed to "Guides and solutions to use".

- Checking if managed databases have SGs {#db-check}

  1. Run the command below to search for Managed MySQL DBs with no SG:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql cluster get --id=$DB_ID --format=json | jq -r '. | select(.security_group_ids | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. The output should return an empty string. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

If any databases without security groups are found, assign them or enable the **Default security group** [functionality](../../../vpc/concepts/security-groups.md#default-security-group.md).

#### 3.14 No public IP address is assigned in managed databases {#db-ip}

Assigning a public IP to a managed database raises information security risks. We do not recommend assigning an external IP unless it is absolutely necessary.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the databases in.
  1. In the list of services, select a service or services with managed databases.
  1. In the object settings, go to the **Hosts** tab.
  1. If the parameters of each object have the **Public access** option disabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for managed DB clusters with public IPs:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql hosts list --cluster-id=$DB_ID --format=json | jq -r '.[] | select(.assign_public_ip)' | jq -r '.cluster_id' 
     done;
     done;
     done
     ```

  1. If an empty string is output, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:** 

Disable public access if it is not required.

#### 3.15 The deletion protection feature is enabled {#deletetion-protection}

In {{ yandex-cloud }} managed databases, you can enable deletion protection. The deletion protection feature safeguards the cluster against accidental deletion by a user. Even if it is enabled, one can still connect to the cluster manually and delete the data.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the databases in.
  1. In the list of services, select a service or services with managed databases.
  1. In the object settings, go to the **Advanced settings** tab.
  1. If the parameters of each object have the **Deletion protection** option enabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for managed DB clusters with deletion protection disabled:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql cluster get --id=$DB_ID --format=json | jq -r '. | select(.deletion_protection | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. The output should return an empty string. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

1. In the management console, select the cloud or folder to enable deletion protection in.
1. In the list of services, select a service or services with managed databases.
1. In the object settings, go to the **Advanced settings** tab.
1. In the object parameters, enable **Deletion protection**.

#### 3.16 The setting that enables access from {{ datalens-short-name }} is not activated unless needed {#db-datalens-access}

Do not enable access to databases containing critical data from the management console, [{{ datalens-short-name }}](../../../datalens), or other services unless you have to. Access from {{ datalens-short-name }} may be required for data analysis and visualization. For such access, the {{ yandex-cloud }} service network is used, with authentication and TLS encryption. You can enable and disable access from {{ datalens-short-name }} or other services in the cluster settings or when creating it in the advanced settings section.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the databases in.
  1. In the list of services, select a service or services with managed databases.
  1. In the object settings, go to the **Advanced settings** tab.
  1. If the parameters of each object have **Access from {{ datalens-short-name }}** disabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Find managed DB clusters with access from {{ datalens-short-name }} enabled:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql cluster get --id=$DB_ID --format=json | jq -r '. | select(.config.access.data_lens)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. The output should return an empty string. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

1. In the management console, select the cloud or folder to disable access from {{ datalens-short-name }} in.
1. In the list of services, select a service or services with managed databases.
1. In the object settings, go to the **Advanced settings** tab.
1. In the object parameters, disable **Access from {{ datalens-short-name }}**.

#### 3.17 Access from the management console is disabled in managed databases {#db-console-access}

You may need access to the database from the management console to send [SQL queries](../../..//managed-postgresql/operations/web-sql-query.md) to the database and visualize the data structure.

We recommend that you enable this type of access only if needed, because it raises information security risks. In normal mode, use a standard DB connection as a DB user.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the databases in.
  1. In the list of services, select a service or services with managed databases.
  1. In the object settings, go to the **Advanced settings** tab.
  1. If the parameters of each object have **Access from the management console** disabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for managed DB clusters with access from the management console enabled:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql cluster get --id=$DB_ID --format=json | jq -r '. | select(.config.access.web_sql)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. If an empty string is output, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:** 

1. In the management console, select the cloud or folder to disable access from the management console in.
1. In the list of services, select a service or services with managed databases.
1. In the object settings, go to the **Advanced settings** tab.
1. In the object parameters, disable **Access from console**.

### {{ sf-name }} and {{ api-gw-full-name }} {#functions-api-gateway}

#### 3.18 {{ serverless-containers-short-name }}/{{ sf-name }} uses the internal {{ vpc-short-name }} network {#vpc-functions}

By default, the function is invoked in the isolated IPv4 network with the NAT gateway enabled. For this reason, only public IPv4 addresses are available. You cannot fix the address.

Networking between two functions, as well as between functions and user resources, is limited:

* Incoming connections are not supported. For example, you cannot access the internal components of a function over the network, even if you know the IP address of its instance.
* Outgoing connections are supported via TCP, UDP, and ICMP. For example, a function can access a {{ compute-full-name }} VM or a {{ ydb-full-name }} DB on the user's network.
* Function is cross-zoned: you cannot explicitly specify a subnet or select an availability zone to run a function.

If necessary, you can specify a cloud network in the function settings. In which case:

* The function will be executed in the specified cloud network. 
* While being executed, the function will get an IP address in the relevant subnet and access to all the network resources.
* The function will have access not only to the internet but also to user resources located in the specified network, such as databases, virtual machines, etc. 
* The function will have an IP address within the `198.19.0.0/16` range when accessing user resources.

You can only specify a single network for functions, containers, and API gateways that reside in the same cloud. 

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the functions in.
  1. In the list of services, select {{ sf-name }}.
  1. Open all the functions.
  1. In the object settings, go to the **Edit function version** tab.
  1. If the parameters of each object have **Network — {{ vpc-short-name }}** set, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. Run the command below to search for any cloud functions that have no network settings specified in {{ vpc-short-name }}:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for VER in $(yc serverless function version list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc serverless function version get $VER --format=json | jq -r '. | select(.connectivity.network_id | not)' | jq -r '.id' 
     done;
     done;
     done
     ```

  1. If an empty string is output, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:** 

1. Select the cloud or folder to check the functions in.
1. Select **{{ sf-name }}** in the list of services.
1. Open the function.
1. In the object settings, go to the **Edit function version** tab.
1. Set **Network — {{ vpc-short-name }}**.

For more information about tracking function versions, see [{#T}](../../../functions/concepts/backup.md).

#### 3.19 Functions are configured for access differentiation, secret and environment variable management, and DBMS connection {#function-access-and-env}

In cases where the use of public functions is not explicitly required, we recommend that you use private functions. For more information about setting up access to functions, see [Managing function access permissions](../../../functions/operations/function/function-public.md). We recommend using private functions and assigning rights to invoke functions to specific cloud users.

A [service account](../../../iam/concepts/users/service-accounts.md) is an account that can be used by programs or functions to manage resources in {{ yandex-cloud }}. If the function version was created with a service account, you can [get](../../../functions/operations/function-sa.md) an IAM token for service account from the function invocation context.

Make sure to assign [roles](../../../iam/concepts/access-control/roles.md) to the service account. A role is a set of permissions to perform operations with the cloud's resources. A function automatically inherits roles assigned for a folder, cloud, or organization. However, they do not appear in the list of assigned roles.

Do not store secrets and variables in the function code. Use [{{ lockbox-full-name }}](../../../lockbox/index.yaml) to store and rotate secrets. You can transmit a {{ lockbox-name }} secret to a function in the environment variable.

For the function to get access to the secret, edit its parameters to specify a service account with the following roles assigned:

* `lockbox.payloadViewer` [for a secret](../../../lockbox/operations/secret-access.md).
* `kms.keys.encrypterDecrypter` [for an encryption key](../../../kms/operations/key-access.md) if the secret was created using a [{{ kms-full-name }}](../../../kms/index.yaml) encryption key.

A {{ lockbox-name }} secret provided to a function is cached in {{ sf-name }}. After you revoke a service account's access to a secret, the function may continue to store the secret for up to 5 minutes.

Transmitting secrets creates a new function version. You cannot transmit secrets to an existing function version. 

You can add other environment variables when creating a function version. The maximum size of environment variables, including their names, is limited to 4 KB.

You cannot calculate environment variables. Environment variable values are string constants. You can only calculate these within function code. You can retrieve environment variables using standard programming language tools.

You can access the DB cluster hosts from the function only via the [SSL protocol](https://ru.wikipedia.org/wiki/SSL). To do this, [create](../../../functions/operations/database-connection.md) a connection to the {{ mpg-full-name }} or {{ mch-full-name }} cluster hosts that are not configured for public access. Use a service account with a role assigned and enable access for functions on the DBMS side.

**Guides and solutions to use:**

* [Disable](../../../functions/operations/function/function-private.md) public access to a function.
* [View](../../../functions/operations/function/role-list.md) a list of roles assigned to a function.
* [Get](../../../functions/operations/function-sa.md) a service account IAM token using a function.
* [Revoke](../../../functions/operations/function/role-revoke.md) a role assigned to a function.
* [Connect](../../../functions/operations/database-connection.md) to a database from a function.

For more information about roles and resources you can assign roles for in {{ sf-name }}, see [{#T}](../../../functions/security/index.md).

#### 3.20 Side-channel attacks in {{ sf-name }} are addressed {#side-channel-attacks}

Hosts and hypervisors running {{ sf-name }} contain all the applicable updates for side-channel attack protection. However, keep in mind that functions execute untrusted customer code. {{ yandex-cloud }} security experts believe that side-channel attacks are unlikely in the context of functions, but this risk must be accounted for, particularly in the overall threats and risk analysis model employed by the PCI DSS infrastructure.

{% list tabs group=instructions %}

- Manual check {#manual}

  Make sure the most critical systems do not use {{ sf-name }} or this is considered in the risk analysis model.

{% endlist %}

#### 3.21 Aspects of time synchronization in {{ sf-name }} are addressed {#ntp-functions}

{{ sf-name }} does not guarantee time synchronization prior to or during execution of requests by functions. To get a function log with exact timestamps on the {{ sf-name }} side, use a cloud logging service. For more information on function logging, see [{#T}](../../../functions/concepts/logs.md).

#### 3.22 Aspects of header management in {{ sf-name }} are addressed {#http-functions}

If the function is called to process an HTTP request, the returned result should be a JSON document containing the HTTP response code, response headers, and response content. {{ sf-name }} automatically processes this JSON document and returns data in a standard HTTP response to the user. It is the customer's responsibility to manage the response headers according to the regulatory requirements and the threat model. For more information on how to process an HTTP request, refer to the {{ sf-name }} manual, [Invoking a function in {{ sf-name }}](../../../functions/concepts/function-invoke.md).

You can run a function by specifying the `?integration=raw` string query parameter. When invoked this way, a function cannot parse or set HTTP headers:

* HTTPS request body content is provided as the first argument (without converting to a JSON structure).
* HTTPS request body content is the same as the function's response (without converting and checking the structure); the HTTP response status is `200`.

The request must be a JSON structure which contains:

* `httpMethod`: HTTP method: `DELETE`, `GET`, `HEAD`, `OPTIONS`, `PATCH`, `POST`, or `PUT`.
* `headers`: Dictionary of strings with HTTP request headers and their values. If the same header is provided multiple times, the dictionary contains the last provided value.
* `multiValueHeaders`: Dictionary with HTTP request headers and lists of their values. It contains the same keys as the `headers` dictionary; however, if any of the headers was repeated multiple times, its list will contain all the values provided for this header. If the header was provided only once, it gets included into this dictionary and its list will contain only one value.
* `queryStringParameters`: Dictionary with the query parameters. If the same parameter is specified multiple times, the dictionary will contain the last specified value.
* `multiValueQueryStringParameters`: Dictionary with the list of all specified values for each query parameter. If the same parameter is specified multiple times, the dictionary will contain all the specified values.
* `requestContext`: Request context.

For the purpose of debugging a function, you can use special requests that return the JSON structure of the request and the result you need for debugging. For more information, see [function debugging](../../../functions/concepts/function-invoke.md#example).

### {{ ydb-name }} {#ydb-access}

#### 3.23 Recommendations for using confidential data in {{ ydb-short-name }} are addressed {#ydb-confidential-data}

It is prohibited to use confidential data for names of databases, tables, columns, folders, and so on. It is prohibited to send critical data, e.g., payment card details, to {{ ydb-name }} (both Dedicated and Serverless) as clear text. Prior to sending data, be sure to encrypt it at the application level. For this you can use the KMS service or any other method compliant with the regulator standard. For data where the storage period is known in advance, we recommend that you configure the [Time To Live]({{ ydb.docs }}/concepts/ttl) option.

#### 3.24 Recommendations for SQL injection protection in {{ ydb-short-name }} are addressed {#ydb-sql-injection}

When working with the database, use [parameterized prepared statements]({{ ydb.docs }}/reference/ydb-sdk/example/#param-queries) to protect against SQL injection. If the application dynamically generates query templates, you must prevent the injection of untrusted user input into the SQL query template.

#### 3.25 No public access to {{ ydb-short-name }} is allowed {#ydb-public}

When accessing the database in dedicated mode, we recommend that you use it inside {{ vpc-short-name }}, disabling public access to it from the internet. In serverless mode, the database can be accessed from the internet. You must therefore take this into account when modeling threats to your infrastructure. For more information about the operating modes, see the [Serverless and dedicated modes](../../../ydb/concepts/serverless-and-dedicated.md) section in the {{ ydb-name }} documentation.

When setting up database permissions, use the principle of least privilege.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the database in. 
  1. In the list of services, select **{{ ydb-name }}**.
  1. Open all the databases.
  1. In the database settings, go to the **Network** tab.
  1. If the parameters of each object have the **Public IP addresses** option disabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for managed DB clusters with public IPs:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for DB_ID in $(yc managed-mysql cluster list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc managed-mysql hosts list --cluster-id=$DB_ID --format=json | jq -r '.[] | select(.assign_public_ip)' | jq -r '.cluster_id' 
     done;
     done;
     done
     ```

  1. The output should return an empty string. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:** 

Disable public access if it is not required.

#### 3.26 Recommendations for {{ ydb-short-name }} backups are addressed {#ydb-backup}

When creating [on-demand backups](../../../ydb/pricing/serverless.md), make sure that the backup data is properly protected.

When creating backups on demand in {{ objstorage-name }}, follow the recommendations in the {{ objstorage-name }} subsection above (for example, use the built-in bucket encryption feature).

### {{ container-registry-full-name }} {#container-registry}

#### 3.27 ACL by IP address is set up for {{ container-registry-full-name }} {#acl-container-registry}

We recommend that you limit access to your {{ container-registry-short-name }} to specific IPs.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the registry in.
  1. In the list of services, select **{{ container-registry-short-name }}**.
  1. In the settings of the specific registry, go to the **Access for IP address** tab.
  1. If specific IPs to allow access for are set in the parameters, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for CRs that are not filtered by IP:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for CR in $(yc container registry list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); do yc container registry list-ip-permissions --id=$CR --format=json | jq -r '.[] | select(.ip)' | jq -r '.action' && echo $CR "IF ACTION PULL/PUSH exist before CR then OK"
     done;
     done;
     done
     ```

  1. If PULL/PUSH is output before each registry ID, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

Specify the IP addresses for registry access.

### {{ cos-full-name }} {#container-solution}

We do not recommend that you use privileged containers to run loads that process untrusted user input. Privileged containers should be used for the purposes of administering VMs or other containers.

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the VMs in.
  1. In the list of services, select **{{ compute-short-name }}**.
  1. Open the settings of a specific VM with a **Container Optimized Image**.
  1. In the Docker container's **Settings**, find the **Privileged mode** parameter.
  1. If it is disabled, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Run the command below to search for CRs that are not filtered by IP:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for VM_ID in $(yc compute instance list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc compute instance get --id=$VM_ID --full --format=json | jq -r '. | select(.metadata."docker-container-declaration")| .metadata."docker-container-declaration" | match("privileged: true") | .string' && echo $VM_ID
     done;
     done;
     done
     ```

  1. If there is no `privileged: true` in front of each VM ID, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

1. In the management console, select the cloud or folder to check the VMs in.
1. In the list of services, select **{{ compute-short-name }}**.
1. Open the settings of a specific VM with a **Container Optimized Image**.
1. In the Docker container's Settings, disable the **Privileged mode** parameter.

#### 3.28 A {{ certificate-manager-full-name }} certificate is valid for at least 30 days {#certificate-validity}

You can use {{ certificate-manager-full-name }} to manage TLS certificates for your API gateways in the API Gateway, as well as your websites and buckets in {{ objstorage-name }}. {{ alb-name }} is integrated with {{ certificate-manager-short-name }} for storing and installing certificates. We recommend that you use {{ certificate-manager-short-name }} to obtain your certificates and rotate them automatically.

When using TLS in your application, we recommend that you limit the list of your trusted root certificate authorities (root CA).

When using certificate pinning, keep in mind that Let's Encrypt certificates are [valid for 90 days](https://letsencrypt.org/docs/faq/#what-is-the-lifetime-for-let-s-encrypt-certificates-for-how-long-are-they-valid).

We recommend that you update certificates in advance if they are not [updated automatically](../../../certificate-manager/concepts/challenges.md#auto).

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the VMs in.
  1. In the list of services, select **{{ certificate-manager-full-name }}**.
  1. Open the settings of each certificate and find the **End date** parameter.
  1. If the parameter shows that the certificate will be valid for at least 30 days more, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

- Performing a check via the CLI {#cli}

  1. See what organizations are available to you and write down the ID you need:

     ```bash
     yc organization-manager organization list
     ```

  1. Search for any of your organization's certificates with the end date:

     ```bash
     export ORG_ID=<organization ID>
     for CLOUD_ID in $(yc resource-manager cloud list --organization-id=${ORG_ID} --format=json | jq -r '.[].id');
     do for FOLDER_ID in $(yc resource-manager folder list --cloud-id=$CLOUD_ID --format=json | jq -r '.[].id'); 
     do for CERT_ID in $(yc certificate-manager certificate list --folder-id=$FOLDER_ID --format=json | jq -r '.[].id'); \
     do yc certificate-manager certificate get --id $CERT_ID --format=json | jq -r '. | "Date of the end " + .not_after + " --- Cert_ID " + .id'
     done;
     done;
     done
     ```

  1. If there is no `privileged: true` in front of each VM ID, the recommendation is fulfilled. Otherwise, proceed to "Guides and solutions to use".

{% endlist %}

**Guides and solutions to use:**

Update the certificate or set up auto updates.

### {{ mgl-full-name }} {#git-lab-service}

#### 3.29 {{ GL }} instance security setup guidelines are followed {#git-lab-secure}

See the recommendations [here](../../../managed-gitlab/concepts/security.md#secure-instance). 

{% list tabs group=instructions %}

- Manual check {#manual}

  Run a manual check.

{% endlist %}

#### 3.30 Antivirus protection is used {#antivirus}

Make sure to provide anti-malware protection within your scope of responsibility. You can use a variety of solutions from our partners in [{{ marketplace-full-name }}](/marketplace).
[Antivirus solution images](/marketplace/products/kaspersky/kaspersky-linux-hybrid-cloud-security-byol) are available in {{ marketplace-full-name }}. License types and other required information are available in the product descriptions.

{% list tabs group=instructions %}

- Manual check {#manual}

  Make sure that critical systems are protected with antivirus solutions.

{% endlist %}

**Guides and solutions to use:**

Follow the vendor guide to install the AV solution.

#### 3.31 {{ managed-k8s-full-name }} security guidelines are used {#k8s-security}

Check the recommendations in [{#T}](../../../security/domains/kubernetes.md).

#### 3.32 {{ oslogin }} is used for connection to a VM or {{ k8s }} node {#os-login-onto-hosts}

[{{ oslogin }}](../../../organization/concepts/os-login.md) is a convenient way to manage connections to [VMs](../../../compute/concepts/vm.md) and {{ managed-k8s-full-name }} [cluster](../../../managed-kubernetes/concepts/index.md#kubernetes-cluster) nodes via SSH through the [YC CLI](../../../cli/quickstart.md) or via a standard SSH client with an SSH certificate or SSH key, which you first need to add to the {{ oslogin }} profile of organization user or [service account](../../../iam/concepts/users/service-accounts.md) in {{ org-full-name }}.

{{ oslogin }} links the account of a virtual machine or {{ k8s }} node user with that of an organization or service account user. To manage access to virtual machines and {{ k8s }} nodes, [enable](../../../organization/operations/os-login-access.md) the OS Login access option at the organization level and then [activate](../../../compute/operations/vm-connect/enable-os-login.md) {{ oslogin }} access on each virtual machine or {{ k8s }} node separately.

Thus, you can easily manage access to virtual machines and {{ k8s }} nodes by assigning appropriate roles to users or service accounts. If you revoke the roles from a user or service account, they will lose access to all virtual machines and {{ k8s }} nodes with {{ oslogin }} access enabled.

**Guides and solutions to use:**

* [Enabling {{ oslogin }} access at the organization level](../../../organization/operations/os-login-access.md).
* [Setting up {{ oslogin }} access on an existing VM](../../../compute/operations/vm-connect/enable-os-login.md).
* [Connect to the virtual machine via {{ oslogin }}](../../../compute/operations/vm-connect/os-login.md).
* [Connecting to a {{ k8s }} node via {{ oslogin }}](../../../managed-kubernetes/operations/node-connect-oslogin.md).

#### 3.33 Vulnerability scanning is performed at the cloud IP level {#ip-level}

We recommend that customers should scan their hosts for vulnerabilities by themselves. Cloud resources support the installation of custom virtual images of vulnerability scanners or software agents on hosts. There are many paid and free scanning solutions on the market.

Network scanners scan hosts that are accessible over a network. Generally, authentication can be configured on network scanners. 

Examples of free network scanners:
- [Nmap](https://nmap.org/)
- [OpenVAS](https://www.openvas.org/)
- [OWASP ZAP](https://www.zaproxy.org/)

Example of a free scanner operating as an agent on hosts: [Wazuh](https://documentation.wazuh.com/current/user-manual/capabilities/vulnerability-detection/how_it_works.html). Wazuh can also be used as a host-based intrusion detection system (IDS).

You can also use a [solution](/marketplace/products/scanfactory/scanfactory-saas) from {{ marketplace-name }}.

{% list tabs group=instructions %}

- Manual check {#manual}

  Run a manual check.

{% endlist %}

#### 3.34 External security scans are performed according to the cloud rules {#external-security-scans}

Customers hosting their own software in {{ yandex-cloud }} can perform external security scans for the hosted software, including penetration tests. You can run your own scans or use contractors. For more information, see [Rules for performing external security scans](../../../security/compliance/pentest.md).

{% list tabs group=instructions %}

- Manual check {#manual}

  Run a manual check.

{% endlist %}

#### 3.35 The process of security updates is set up {#security-updates}

Customers must perform security updates themselves within their [scope of responsibility](../../../security/respons.md). Various automated tools are available for centralized automated OS and software updates.

{{ yandex-cloud }} publishes security bulletins to notify customers of newly discovered vulnerabilities and security updates.

### Backups {#backup}

#### 3.36 Use {{ backup-short-name }} or scheduled snapshots {#snapshot}

Make sure to back up all VMs in your organization using one of these options:
* Scheduled snapshots
* {{ backup-short-name }}

{% list tabs group=instructions %}

- Performing a check in the management console {#console}

  1. In the management console, select the cloud or folder to check the VMs in.
  1. In the list of services, select {{ compute-short-name }}.
  1. Make sure that the scheduled snapshot policy is set up on the VMs.
  1. In the list of services, select {{ backup-short-name }}.
  1. Make sure that it is enabled.

{% endlist %}