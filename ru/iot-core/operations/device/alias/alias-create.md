# Создание алиаса в {{ iot-name }}

Алиас привязан к конкретному устройству, поэтому для создания алиаса вам надо [узнать идентификатор или имя устройства](../device-list.md).

{% include [monitoring-topic](../../../../_includes/iot-core/monitoring-topic.md) %}

{% list tabs group=instructions %}

- Консоль управления {#console}

   Чтобы создать алиас:

   1. В [консоли управления]({{ link-console-main }}) выберите каталог, в котором вы хотите создать алиас.
   1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_iot-core }}**.
   1. Выберите в списке нужный реестр.
   1. В левой части окна выберите раздел **{{ ui-key.yacloud.iot.label_devices }}**.
   1. Нажмите значок ![image](../../../../_assets/console-icons/ellipsis.svg) справа от имени нужного устройства, в выпадающем списке выберите **{{ ui-key.yacloud.common.edit }}**.
   1. Добавьте алиас:
      1. Нажмите кнопку **{{ ui-key.yacloud.iot.button_add-alias }}**.
      1. Заполните поля: введите алиас (например, `events`) и тип топика после `$devices/<идентификатор_устройства>` (например, `events`).<br/>Вы сможете использовать алиас `events` вместо топика `$devices/<идентификатор_устройства>/events`.
      1. Повторите действия для каждого добавляемого алиаса.
   1. Нажмите кнопку **{{ ui-key.yacloud.common.save }}**.

- CLI {#cli}
    
    {% include [cli-install](../../../../_includes/cli-install.md) %}
    
    {% include [default-catalogue](../../../../_includes/default-catalogue.md) %}
    
    Создайте алиас: 
    
    ```bash
    yc iot device add-topic-aliases arenak5ciqss********
      --topic-aliases commands='$devices/arenak5ciqss********/commands'
    ```

	  Результат:
	  
    ```bash
    id: arenak5ciqss********
    registry_id: arenou2oj4ct********
    created_at: "2019-09-16T12:32:48.911Z"
    name: second
    topic_aliases:
      commands: $devices/arenak5ciqss********/commands
    ``` 

    Также вы можете добавить алиас при [создании устройства](../device-create.md). Для этого вместо уникального идентификатора в топике устройства указывается `{id}`, так как уникальный идентификатор еще неизвестен:
    
    ```bash
    yc iot device create
      --registry-name <имя_реестра>
      --name <имя_устройства>
      --topic-aliases <имя_алиаса>='$devices/{id}/<events,_state,_commands_или_config>'
    ```

- {{ TF }} {#tf}

  {% include [terraform-definition](../../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

  {% include [terraform-install](../../../../_includes/terraform-install.md) %}

  Чтобы добавить алиас устройству, созданному с помощью {{ TF }}:

  1. Опишите в конфигурационном файле параметры ресурсов, которые необходимо создать:

     * `yandex_iot_core_device` — параметры устройства:
       * `registry_id` — [идентификатор реестра](../../registry/registry-list.md#registry-list), в котором создано устройство.
       * `name` — [имя устройства](../device-list.md#device-list).
       * `description` — описание устройства.
       * `aliases` — алиасы топиков.

      Пример структуры ресурса в конфигурационном файле:

      ```hcl
      resource "yandex_iot_core_device" "my_device" {
        registry_id = "<идентификатор_реестра>"
        name        = "<имя_устройства>"
        description = "test device for terraform provider documentation"

        aliases = {
          "some-alias1/subtopic" = "$devices/{id}/events/somesubtopic",
          "some-alias2/subtopic" = "$devices/{id}/events/aaa/bbb",
        }
      ...
      }
      ```

      Более подробную информацию о параметрах ресурса `yandex_iot_core_device` в {{ TF }}, см. в [документации провайдера]({{ tf-provider-resources-link }}/iot_core_device).
  1. В командной строке перейдите в папку, где вы отредактировали конфигурационный файл.
  1. Проверьте корректность конфигурационного файла с помощью команды:

      ```bash
      terraform validate
      ```

      Если конфигурация является корректной, появится сообщение:
     
      ```bash
      Success! The configuration is valid.
      ```

  1. Выполните команду:

      ```bash
      terraform plan
      ```

      В терминале будет выведен список ресурсов с параметрами. На этом этапе изменения не будут внесены. Если в конфигурации есть ошибки, {{ TF }} на них укажет.
  1. Примените изменения конфигурации:

      ```bash
      terraform apply
      ```

  1. Подтвердите изменения: введите в терминале слово `yes` и нажмите **Enter**.

      Проверить алиасы устройства можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../../../cli/quickstart.md):

      ```bash
      yc iot device get <имя_устройства>
      ```

- API {#api}

  Чтобы создать алиас, воспользуйтесь методом REST API [create](../../../api-ref/Device/create.md) для ресурса [Device](../../../api-ref/Device/index.md) или вызовом gRPC API [DeviceService/Create](../../../api-ref/grpc/Device/create.md).

{% endlist %}