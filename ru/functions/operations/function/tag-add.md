# Добавить тег версии функции

При создании новой версии ей присваивается [тег](../../concepts/function.md#) по умолчанию — `$latest`.

{% list tabs group=instructions %}

- CLI {#cli}

    {% include [cli-install](../../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

    Чтобы добавить тег версии, выполните команду:

    ```
    yc serverless function version set-tag --id <идентификатор_версии> --tag <тег>
    ```

    Результат:

    ```
    id: b09ch6pmpohf********
    function_id: b097d9ous3ge********
    created_at: "2023-08-22T09:12:38.464Z"
    runtime: python311
    entrypoint: test.handler
    resources:
      memory: "134217728"
    execution_timeout: 5s
    image_size: "4096"
    status: ACTIVE
    tags:
    - beta
    - first
    log_group_id: eolv6578frac********
    ```

- {{ TF }} {#tf}

    {% include [terraform-definition](../../../_tutorials/_tutorials_includes/terraform-definition.md) %}

    {% include [terraform-install](../../../_includes/terraform-install.md) %}

    Чтобы добавить тег версии:

    1. В конфигурационном файле добавьте блок `tags` для ресурса `yandex_function` и укажите список тегов формате `tags = ["<имя_тега>"]`.

       Пример описания функции в конфигурации {{ TF }}:
      
        ```
        resource "yandex_function" "test-function" {
            name               = "test-function"
            description        = "Test function"
            user_hash          = "first-function"
            runtime            = "python311"
            entrypoint         = "main"
            memory             = "128"
            execution_timeout  = "10"
            service_account_id = "<идентификатор_сервисного_аккаунта>"
            tags               = ["my_tag"]
            content {
                zip_filename = "<путь_к_ZIP-архиву>"
            }
        }
        ``` 

        Более подробную информацию о параметрах ресурса `yandex_function` см. в [документации провайдера]({{ tf-provider-resources-link }}/function).

    1. Проверьте конфигурацию командой:
        
       ```
       terraform validate
       ```

       Если конфигурация является корректной, появится сообщение:
        
       ```
       Success! The configuration is valid.
       ```

    1. Выполните команду:

       ```
       terraform plan
       ```
        
       В терминале будет выведен список ресурсов с параметрами. На этом этапе изменения не будут внесены. Если в конфигурации есть ошибки, {{ TF }} на них укажет. 
         
    1. Примените изменения конфигурации:

       ```
       terraform apply
       ```
    1. Подтвердите изменения: введите в терминал слово `yes` и нажмите **Enter**.
      
    Проверить появление тегов можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../../cli/quickstart.md):

    ```
    yc serverless function version list --function-name <имя_функции>
    ```

- API {#api}

    Чтобы добавить тег версии функции, воспользуйтесь методом REST API [setTag](../../functions/api-ref/Function/setTag.md) для ресурса [Function](../../functions/api-ref/Function/index.md) или вызовом gRPC API [FunctionService/SetTag](../../functions/api-ref/grpc/Function/setTag.md).

- {{ yandex-cloud }} Toolkit {#yc-toolkit}

    Добавить тег можно с помощью [плагина {{ yandex-cloud }} Toolkit](https://github.com/yandex-cloud/ide-plugin-jetbrains) для семейства IDE на [платформе IntelliJ](https://www.jetbrains.com/ru-ru/opensource/idea/) от [JetBrains](https://www.jetbrains.com/).

{% endlist %}
