---
title: Создать автоматически масштабируемую группу виртуальных машин
description: Следуя данной инструкции, вы сможете создать автоматически масштабируемую группу виртуальных машин.
---

# Создать автоматически масштабируемую группу виртуальных машин


Вы можете создать [автоматически масштабируемую](../../concepts/instance-groups/scale.md#auto-scale) [группу однотипных ВМ](../../concepts/instance-groups/index.md). Управление размером такой группы [ВМ](../../concepts/vm.md) будет осуществляться автоматически.

{% include [warning.md](../../../_includes/instance-groups/warning.md) %}

{% include [sa.md](../../../_includes/instance-groups/sa.md) %}

Чтобы иметь возможность создавать, обновлять и удалять ВМ в группе [назначьте](../../../iam/operations/sa/assign-role-for-sa.md) сервисному аккаунту роль [compute.editor](../../security/index.md#compute-editor).

Чтобы создать автоматически масштабируемую группу ВМ:

{% list tabs group=instructions %}

- Консоль управления {#console}

  {% include [create-autoscaled-group-via-concole.md](../../../_includes/instance-groups/create-autoscaled-group-via-concole.md) %}

- CLI {#cli}

  {% include [cli-install.md](../../../_includes/cli-install.md) %}

  {% include [default-catalogue.md](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды [CLI](../../../cli/) для создания группы ВМ:

     ```bash
     {{ yc-compute-ig }} create --help
     ```

  1. Проверьте, есть ли в [каталоге](../../../resource-manager/concepts/resources-hierarchy.md#folder) [сети](../../../vpc/concepts/network.md#network):

     ```bash
     yc vpc network list
     ```

     Если ни одной сети нет, [создайте ее](../../../vpc/operations/network-create.md).
  1. Выберите один из публичных образов {{ marketplace-full-name }} (например, [CentOS 7](/marketplace/products/yc/centos-7)).

     {% include [standard-images.md](../../../_includes/standard-images.md) %}

  1. Создайте YAML-файл с произвольным именем, например `specification.yaml`.
  1. Опишите в созданном файле:
     * Общую информацию о группе ВМ:

       ```yaml
       name: first-autoscaled-group
       service_account_id: <идентификатор_сервисного_аккаунта>
       description: "Эта группа ВМ создана с помощью YAML-файла конфигурации."
       ```

       Где:
       * `name` — произвольное имя группы ВМ. Имя должно быть уникальным в рамках каталога. Имя может содержать строчные буквы латинского алфавита, цифры и дефисы. Первый символ должен быть буквой. Последний символ не может быть дефисом. Максимальная длина имени — 63 символа.
       * `service_account_id` — идентификатор [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md).

          Чтобы иметь возможность создавать, обновлять и удалять ВМ в группе [назначьте](../../../iam/operations/sa/assign-role-for-sa.md) сервисному аккаунту роль [compute.editor](../../security/index.md#compute-editor).

         {% include [sa-dependence-brief](../../../_includes/instance-groups/sa-dependence-brief.md) %}

       * `description` — произвольное описание группы ВМ.
     * [Шаблон ВМ](../../concepts/instance-groups/instance-template.md), например:

       ```yaml
       instance_template:
         platform_id: standard-v3
         resources_spec:
           memory: 2g
           cores: 2
         boot_disk_spec:
           mode: READ_WRITE
           disk_spec:
             image_id: fdvk34al8k5n********
             type_id: network-hdd
             size: 32g
         network_interface_specs:
           - network_id: c64mknqgnd8a********
             primary_v4_address_spec: {}
             security_group_ids:
               - enps0ar5s3ti********
         scheduling_policy:
           preemptible: false
       ```

       {% include [default-unit-size](../../../_includes/instance-groups/default-unit-size.md) %}

       Где:
       * `platform_id` — идентификатор [платформы](../../concepts/vm-platforms.md).
       * `memory` — количество памяти (RAM).
       * `cores` — количество ядер процессора (vCPU).
       * `mode` — режим доступа к [диску](../../concepts/disk.md).
         * `READ_ONLY` — доступ на чтение.
         * `READ_WRITE` — доступ на чтение и запись.
       * `image_id` — идентификатор публичного образа. Его можно посмотреть в [консоли управления]({{ link-console-main }}) при создании ВМ или в [{{ marketplace-name }}](/marketplace) на странице образа в блоке **Идентификаторы продукта**.
       * `type_id` — тип диска.
       * `size` — размер диска.
       * `network_id` — идентификатор сети `default-net`.
       * `primary_v4_address_spec` — спецификация версии интернет протокола IPv4. Вы можете предоставить публичный доступ к ВМ группы, указав версию IP для [публичного IP-адреса](../../../vpc/concepts/address.md#public-addresses). Подробнее читайте в разделе [{#T}](../../concepts/instance-groups/instance-template.md#instance-template).
       * `security_group_ids` — список идентификаторов [групп безопасности](../../../vpc/concepts/security-groups.md).
       * `scheduling_policy` — конфигурация политики планирования.
       * `preemptible` — флаг, указывающий создавать [прерываемые ВМ](../../concepts/preemptible-vm.md).
         * `true` — будет создана прерываемая ВМ.
         * `false` (по умолчанию) — обычная.

         Создавая группу прерываемых ВМ учитывайте, что ВМ будут останавливаться спустя 24 часа непрерывной работы, а могут быть остановлены еще раньше. При этом возможна ситуация, что ВМ не смогут сразу перезапуститься их из-за нехватки ресурсов. Это может произойти, если резко возрастет потребление вычислительных ресурсов в {{ yandex-cloud }}.
     * [Политики](../../concepts/instance-groups/policies/index.md):

       ```yaml
       deploy_policy:
         max_unavailable: 1
         max_expansion: 0
       scale_policy:
         auto_scale:
           initial_size: 5
           max_size: 15
           min_zone_size: 3
           measurement_duration: 30s
           warmup_duration: 60s
           stabilization_duration: 120s
           cpu_utilization_rule: 0.75
       allocation_policy:
         zones:
           - zone_id: {{ region-id }}-a
       ```

       Где:
       * `deploy_policy` — [политика развертывания](../../concepts/instance-groups/policies/deploy-policy.md) ВМ в группе.
       * `scale_policy` — [политика масштабирования](../../concepts/instance-groups/policies/scale-policy.md) ВМ в группе.
       * `allocation_policy` — [политика распределения](../../concepts/instance-groups/policies/allocation-policy.md) ВМ по [зонам доступности](../../../overview/concepts/geo-scope.md).

     Полный код файла `specification.yaml`:

     ```yaml
     name: first-autoscaled-group
     service_account_id: <идентификатор_сервисного_аккаунта>
     description: "Эта группа ВМ создана с помощью YAML-файла конфигурации."
     instance_template:
       platform_id: standard-v3
       resources_spec:
         memory: 2g
         cores: 2
       boot_disk_spec:
         mode: READ_WRITE
         disk_spec:
           image_id: fdvk34al8k5n********
           type_id: network-hdd
           size: 32g
       network_interface_specs:
         - network_id: c64mknqgnd8a********
           primary_v4_address_spec: {}
           security_group_ids:
             - enps0ar5s3ti********
     deploy_policy:
       max_unavailable: 1
       max_expansion: 0
     scale_policy:
       auto_scale:
         initial_size: 5
         max_size: 15
         min_zone_size: 3
         measurement_duration: 30s
         warmup_duration: 60s
         stabilization_duration: 120s
         cpu_utilization_rule:
           utilization_target: 75
     allocation_policy:
       zones:
         - zone_id: {{ region-id }}-a
     ```

  1. Создайте группу ВМ в каталоге по умолчанию:

     ```bash
     {{ yc-compute-ig }} create --file specification.yaml
     ```

     Данная команда создаст автоматически масштабируемую группу ВМ со следующими характеристиками:
     * С именем `first-autoscaled-group`.
     * С OC CentOS 7.
     * В сети `default-net`.
     * В зоне доступности `{{ region-id }}-a`.
     * С 2 vCPU и 2 ГБ RAM.
     * С сетевым [HDD-диском](../../concepts/disk.md#disks-types) объемом 32 ГБ.

- {{ TF }} {#tf}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  1. Опишите в конфигурационном файле параметры ресурсов, которые необходимо создать:

     ```hcl
     resource "yandex_iam_service_account" "ig-sa" {
       name        = "ig-sa"
       description = "Сервисный аккаунт для управления группой ВМ."
     }

     resource "yandex_resourcemanager_folder_iam_member" "compute_editor" {
       folder_id = "<идентификатор_каталога>"
       role      = "compute.editor"
       member    = "serviceAccount:${yandex_iam_service_account.ig-sa.id}"
     }

     resource "yandex_compute_instance_group" "ig-1" {
       name               = "autoscaled-ig"
       folder_id          = "<идентификатор_каталога>"
       service_account_id = "${yandex_iam_service_account.ig-sa.id}"
       deletion_protection = "<защита_от_удаления>"
       instance_template {
         platform_id = "standard-v3"
         resources {
           memory = <объем_RAM_ГБ>
           cores  = <количество_ядер_vCPU>
         }

         boot_disk {
           mode = "READ_WRITE"
           initialize_params {
             image_id = "<идентификатор_образа>"
           }
         }

         network_interface {
           network_id         = "${yandex_vpc_network.network-1.id}"
           subnet_ids         = ["${yandex_vpc_subnet.subnet-1.id}"]
           security_group_ids = ["<список_идентификаторов_групп_безопасности>"]
         }

         metadata = {
           ssh-keys = "<имя_пользователя>:<содержимое_SSH-ключа>"
         }
       }

       scale_policy {
         auto_scale {
           initial_size           = 3
           measurement_duration   = 60
           cpu_utilization_target = 75
           min_zone_size          = 3
           max_size               = 15
           warmup_duration        = 60
           stabilization_duration = 120
         }
       }

       allocation_policy {
         zones = ["{{ region-id }}-a"]
       }

       deploy_policy {
         max_unavailable = 1
         max_expansion   = 0
       }
     }

     resource "yandex_vpc_network" "network-1" {
       name = "network1"
     }

     resource "yandex_vpc_subnet" "subnet-1" {
       name           = "subnet1"
       zone           = "{{ region-id }}-a"
       network_id     = "${yandex_vpc_network.network-1.id}"
       v4_cidr_blocks = ["192.168.10.0/24"]
     }
     ```

     Где:
     * `yandex_iam_service_account` — описание [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md). Все операции с группой ВМ выполняются от имени сервисного аккаунта.
     * `yandex_resourcemanager_folder_iam_member` — описание прав доступа к [каталогу](../../../resource-manager/concepts/resources-hierarchy.md#folder), которому принадлежит сервисный аккаунт. Чтобы иметь возможность создавать, обновлять и удалять ВМ в группе, назначьте сервисному аккаунту роль [compute.editor](../../security/index.md#compute-editor).
     * `yandex_compute_instance_group` — описание группы ВМ.
       * Общая информация о группе ВМ:
         * `name` — имя группы ВМ.
         * `folder_id` — идентификатор каталога.
         * `service_account_id` — идентификатор [сервисного аккаунта](../../../iam/concepts/users/service-accounts.md).
         * `deletion_protection` — защита группы ВМ от удаления: `true` или `false`. Пока опция включена, группу ВМ удалить невозможно. Значение по умолчанию `false`.
       * [Шаблон ВМ](../../concepts/instance-groups/instance-template.md):
         * `platform_id` — [платформа](../../concepts/vm-platforms.md).
         * `resources` — количество ядер vCPU и объем RAM, доступные ВМ. Значения должны соответствовать выбранной платформе.
         * `boot_disk` — настройки загрузочного [диска](../../concepts/disk.md).
           * Идентификатор выбранного образа. Вы можете получить идентификатор образа из [списка публичных образов](../images-with-pre-installed-software/get-list.md).
           * Режим доступа к диску: `READ_ONLY` (чтение) или `READ_WRITE` (чтение и запись).
         * `network_interface` — настройка [сети](../../../vpc/concepts/network.md#network). Укажите идентификаторы сети, [подсети](../../../vpc/concepts/network.md#subnet) и [групп безопасности](../../../vpc/concepts/security-groups.md).
         * `metadata` — в [метаданных](../../concepts/vm-metadata.md) необходимо передать открытый ключ для [SSH-доступа](../../../glossary/ssh-keygen.md) на ВМ. Подробнее в разделе [{#T}](../../concepts/vm-metadata.md).
       * [Политики](../../concepts/instance-groups/policies/index.md):
         * `deploy_policy` — [политика развертывания](../../concepts/instance-groups/policies/deploy-policy.md) ВМ в группе.
         * `scale_policy` — [политика масштабирования](../../concepts/instance-groups/policies/scale-policy.md) ВМ в группе.
         * `allocation_policy` — [политика распределения](../../concepts/instance-groups/policies/allocation-policy.md) ВМ по [зонам доступности](../../../overview/concepts/geo-scope.md).
     * `yandex_vpc_network` — описание облачной сети.
     * `yandex_vpc_subnet` — описание подсети, к которой будет подключена группа ВМ.

       {% note info %}

       Если у вас уже есть подходящие ресурсы (сервисный аккаунт, облачная сеть и подсеть), описывать их повторно не нужно. Используйте их имена и идентификаторы в соответствующих параметрах.

       {% endnote %}

     Более подробную информацию о ресурсах, которые вы можете создать с помощью {{ TF }}, см. в [документации провайдера]({{ tf-provider-link }}).
  1. Создайте ресурсы:

     {% include [terraform-validate-plan-apply](../../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

     После этого в указанном каталоге будут созданы все требуемые ресурсы. Проверить появление ресурсов и их настройки можно в [консоли управления]({{ link-console-main }}).

- API {#api}

  Воспользуйтесь методом REST API [create](../../instancegroup/api-ref/InstanceGroup/create.md) для ресурса [InstanceGroup](../../instancegroup/api-ref/InstanceGroup/index.md) или вызовом gRPC API [InstanceGroupService/Create](../../instancegroup/api-ref/grpc/InstanceGroup/create.md).

{% endlist %}

{% include [password-reset-note](../../../_includes/compute/password-reset-note.md) %}