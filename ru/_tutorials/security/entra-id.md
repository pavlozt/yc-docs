# Сопоставление групп пользователей в {{ microsoft-idp.entra-id-full }}

Вы можете использовать сервис [{{ microsoft-idp.entra-id-full }}](https://www.microsoft.com/ru-ru/security/business/identity-access/microsoft-entra-id) (бывший {{ microsoft-idp.azure-ad-legacy }}) для аутентификации пользователей в организации.

Чтобы настроить сопоставление (mapping) групп пользователей в {{ microsoft-idp.entra-id-short }} и групп пользователей в [федерации удостоверений](../../organization/concepts/add-federation.md):

1. [Начните настройку приложения в Azure](#azure-settings-begin).
1. [Создайте федерацию в {{ org-full-name }}](#create-federation).
1. [Добавьте SAML-сертификат приложения в федерацию](#add-certificate).
1. [Завершите настройку приложения](#azure-settings-end).
1. [Настройте сопоставление групп на стороне приложения](#azure-mapping).
1. [Настройте сопоставление групп на стороне федерации](#org-mapping).
1. [Проверьте работу аутентификации](#test-auth).

## Перед началом работы {#before-you-begin}

Убедитесь, что у вас есть доступ к следующим службам на [портале Azure](https://portal.azure.com/):

* Корпоративные приложения.
* {{ microsoft-idp.entra-id-full }}.

## Начните настройку приложения в Azure {#azure-settings-begin}

В роли поставщика удостоверений (IdP) выступает приложение в Microsoft Azure с настроенной технологией единого входа (Single Sign-On, SSO). Чтобы создать приложение и начать его настройку:

1. [Перейдите на портал Azure](https://portal.azure.com/).
1. В разделе **Службы Azure** выберите **Корпоративные приложения**.
1. На панели слева выберите раздел **Корпоративные приложения** → **Все приложения**.
1. Нажмите кнопку **Новое приложение**.
1. На странице **Обзор коллекции {{ microsoft-idp.entra-full }}** нажмите кнопку **Создайте собственное приложение**.
1. В открывшемся окне:
    1. Введите название приложения, например `yandex-cloud-saml`.
    1. Выберите опцию **Интеграция с любыми другими приложениями, которых нет в коллекции (вне коллекции)**.
    1. Нажмите кнопку **Создать**.

    После создания приложения откроется его страница.

1. На панели слева выберите раздел **Единый вход**.
1. Выберите режим единого входа **SAML**.

    Откроется страница **Вход на основе SAML**.

1. Скачайте SAML-сертификат приложения, который используется для подписи сообщений от {{ microsoft-idp.entra-id-short }}:

    1. Найдите блок **Сертификаты SAML** → **Сертификат для подписи маркера**.
    1. Скачайте сертификат по ссылке в поле **Сертификат (Base64)**.

1. Сохраните реквизиты, которые потребуются в дальнейшем при настройке федерации удостоверений:

    1. Найдите блок **Настройка yandex-cloud-saml**.

        Если вы выбрали другое имя приложения, то имя блока будет отличаться от приведенного.

    1. Сохраните необходимые реквизиты:

        * **URL-адрес входа** следующего вида:

            ```text
            https://login.microsoftonline.com/<идентификатор_тенанта>/saml2
            ```

        * **Идентификатор {{ microsoft-idp.entra-full }}** следующего вида:

            ```text
            https://sts.windows.net/<идентификатор_тенанта>/
            ```

{% note info %}

Настройка входа на основе SAML для приложения будет продолжена после создания федерации удостоверений.

Не закрывайте вкладку браузера, в которой выполняется настройка.

{% endnote %}

## Создайте федерацию {{ org-full-name }} {#create-federation}

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Перейдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. На панели слева выберите ![icon-federation](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. В правом верхнем углу страницы нажмите кнопку ![Circles3Plus](../../_assets/console-icons/circles-3-plus.svg) **{{ ui-key.yacloud_org.form.federation.action.create }}**. В открывшемся окне:

      1. Задайте имя федерации, например `demo-federation`. Имя должно быть уникальным в каталоге.

      1. При необходимости добавьте описание.

      1. В поле **{{ ui-key.yacloud_org.entity.federation.field.cookieMaxAge }}** укажите время, в течение которого браузер не будет требовать у пользователя повторной аутентификации.

      1. В поле **{{ ui-key.yacloud_org.entity.federation.field.issuer }}** вставьте идентификатор {{ microsoft-idp.entra-full }}, [полученный в ходе настройки приложения Azure](#azure-settings-begin).

      1. В поле **{{ ui-key.yacloud_org.entity.federation.field.ssoUrl }}** вставьте URL-адрес входа, полученный в ходе настройки приложения Azure.

      1. Включите опцию **{{ ui-key.yacloud_org.entity.federation.field.autocreateUsers }}**, чтобы автоматически добавлять пользователя в организацию после аутентификации. Если опция отключена, федеративных пользователей потребуется [добавить вручную](../../organization/operations/add-account.md#add-user-sso).

          {% include [fed-users-note](../../_includes/organization/fed-users-note.md) %}

      1. (Опционально) Чтобы все запросы аутентификации от {{ yandex-cloud }} содержали цифровую подпись, включите опцию **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**. Потребуется установить SAML-сертификат {{ yandex-cloud }} на стороне поставщика удостоверений.

          {% include [download-saml-cert-when-creating-fed](../../_includes/organization/download-saml-cert-when-creating-fed.md) %}

          {% include [setup-cert-for-idp](../../_includes/organization/setup-cert-for-idp.md) %}

          Сертификат потребуется в дальнейшем при настройке входа на основе SAML для приложения Azure.

      1. {% include [forceauthn-option-enable](../../_includes/organization/forceauthn-option-enable.md) %}

      1. Нажмите кнопку **{{ ui-key.yacloud_org.form.federation.create.action.create }}**.

{% endlist %}

## Добавьте SAML-сертификат приложения Azure в федерацию {#add-certificate}

Чтобы при аутентификации сервис {{ org-name }} мог проверить SAML-сертификат приложения, добавьте сертификат в федерацию:

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. На панели слева выберите ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Нажмите на строку с федерацией, для которой нужно добавить сертификат — `demo-federation`.

  1. Внизу страницы в блоке **{{ ui-key.yacloud_org.page.federation.section.certificates }}** нажмите кнопку **{{ ui-key.yacloud_org.entity.certificate.action.add }}**.

  1. Введите название и описание сертификата.

  1. В поле **{{ ui-key.yacloud_org.component.form-file-upload.field.method }}** выберите `{{ ui-key.yacloud_org.component.form-file-upload.method.manual }}` и вставьте содержимое [полученного ранее сертификата](#azure-settings-begin).

  1. Нажмите кнопку **{{ ui-key.yacloud_org.actions.add }}**.

{% endlist %}

## Завершите настройку приложения Azure {#azure-settings-end}

1. Перейдите на вкладку браузера, в которой выполнялась настройка входа на основе SAML для приложения `yandex-cloud-saml`.
1. Укажите URL для перенаправления:

    1. Найдите блок **Базовая конфигурация SAML**.
    1. Нажмите кнопку **Изменить** в блоке.
    1. Укажите один и тот же URL для перенаправления в полях **Идентификатор (сущности)** и **URL-адрес ответа (URL-адрес службы обработчика утверждений)**.

        URL для перенаправления имеет вид:

        ```text
        https://{{ auth-host }}/federations/<идентификатор_федерации>
        ```

        {% cut "Как получить идентификатор федерации" %}

        {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

        {% endcut %}

    1. Нажмите кнопку **Сохранить** на панели справа.

1. (Опционально) Если при [создании федерации](#create-federation) в {{ org-full-name }} вы включили опцию **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**, то добавьте скачанный ранее SAML-сертификат {{ yandex-cloud }} в приложение:

    1. Найдите блок **Сертификаты SAML** → **Сертификаты для проверки (необязательно)** и нажмите кнопку **Изменить**.
    1. Включите опцию **Требовать сертификаты для проверки**.
    1. Нажмите кнопку **Отправить сертификат**.
    1. Загрузите нужный сертификат в формате PEM.
    
        Если вы не скачивали SAML-сертификат при создании федерации, вы можете скачать его на странице сведений о федерации в {{ org-full-name }}, нажав кнопку ![ArrowDownToLine](../../_assets/console-icons/arrow-down-to-line.svg) **{{ ui-key.yacloud_org.page.federation.action.download-cert }}** в поле **{{ ui-key.yacloud_org.entity.federation.field.encryptedAssertions }}**.
    1. Нажмите кнопку **Сохранить** на панели справа.

1. Нажмите кнопку **Сохранить**.

## Настройте сопоставление групп на стороне приложения Azure {#azure-mapping}

### Создайте пользователя {#create-user}

1. [Перейдите на портал Azure](https://portal.azure.com/).
1. В разделе **Службы Azure** выберите **{{ microsoft-idp.entra-id-full }}**.
1. На панели слева выберите раздел **Пользователи** → **Все пользователи**.
1. Нажмите кнопку **Новый пользователь**. Выберите пункт **Создание нового пользователя** из выпадающего меню.
1. Перейдите на вкладку **Основные сведения**.
1. В поле **Имя субъекта-пользователя** укажите логин пользователя (например `az_demo_user`) в комбинации с доменом (например `example.com`).
1. В поле **Псевдоним почты** укажите адрес электронной почты. По умолчанию псевдоним совпадает с именем пользователя.

    Вы можете указать другой псевдоним:

    1. Отключите опцию **Сформировать на основе имени субъекта-пользователя**.
    1. Укажите нужный псевдоним.

    Например можно использовать псевдоним `ivan_ivanov` для пользователя `az_demo_user@example.com`.

1. В поле **Отображаемое имя** укажите имя пользователя, которое будет отображаться в интерфейсе, например `Ivan Ivanov`.
1. В поле **Пароль** укажите пароль пользователя, который будет использоваться при первом входе. По умолчанию пароль генерируется автоматически.

    Вы можете указать пароль вручную:

    1. Отключите опцию **Автоматическое создание пароля**.
    1. Укажите нужный пароль.

1. Убедитесь, что опция **Учетная запись включена** на вкладке **Основные сведения** включена.
1. Нажмите кнопку **Проверить и создать**.

### Создайте группу и добавьте в нее пользователя {#create-group}

1. [Перейдите на портал Azure](https://portal.azure.com/).
1. В разделе **Службы Azure** выберите **{{ microsoft-idp.entra-id-full }}**.
1. Создайте группу:

    1. На панели слева выберите раздел **Группы** → **Все группы**.
    1. Нажмите кнопку **Создать группу**.
    1. Из выпадающего списка **Тип группы** выберите пункт `Группа безопасности`.
    1. В поле **Имя группы** укажите имя группы, например `az_demo_group`.
    1. В поле **Члены** нажмите ссылку **Нет выбранных участников**.
    1. В открывшемся окне отметьте пользователя `az_demo_user@example.com` и нажмите кнопку **Выбрать**.
    1. Нажмите кнопку **Создать**.

1. Получите идентификатор созданной группы:

    1. На панели слева выберите раздел **Группы** → **Все группы**.
    1. Найдите в списке группу `az_demo_group` и скопируйте ее идентификатор из столбца **ИД объекта**.

        Идентификатор имеет вид `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`.

### Настройте доступы для группы {#configure-azure-group-access}

Настройте приложение так, чтобы созданная группа имела к нему доступ:

1. [Перейдите на портал Azure](https://portal.azure.com/).
1. В разделе **Службы Azure** выберите **Корпоративные приложения**.
1. На панели слева выберите раздел **Корпоративные приложения** → **Все приложения**.
1. Выберите созданное ранее приложение `yandex-cloud-saml`.
1. На панели слева выберите **Пользователи и группы**.
1. Нажмите **Добавить пользователя или группу**.
1. В поле **Группы** нажмите **Не выбрано**.
1. В открывшемся окне отметьте группу `az_demo_group` и нажмите кнопку **Выбрать**.
1. Нажмите кнопку **Назначить**.
1. Нажмите кнопку **Сохранить**.

### Настройте сопоставление групп {#map-azure-group}

1. [Перейдите на портал Azure](https://portal.azure.com/).
1. В разделе **Службы Azure** выберите **Корпоративные приложения**.
1. На панели слева выберите раздел **Корпоративные приложения** → **Все приложения**.
1. Выберите созданное ранее приложение `yandex-cloud-saml`.
1. На панели слева выберите раздел **Единый вход**.
1. Найдите блок **Атрибуты и утверждения** и нажмите кнопку **Изменить**. Далее будут настроены нужные утверждения (claims).
1. Нажмите кнопку **Добавить утверждение о группе**.
1. В блоке **Какие группы, связанные с пользователем, следует возвратить в утверждении?** выберите опцию `Группы безопасности`.
1. Из выпадающего списка **Атрибут источника** выберите пункт `Идентификатор группы`.
1. Раскройте блок **Дополнительные параметры** и измените параметры:

    1. Включите опцию **Изменение имени утверждения группы**.
    1. В поле **Имя (необязательно)** введите `member`.

1. Нажмите кнопку **Сохранить** на панели справа.
1. Нажмите кнопку **Сохранить**.

## Настройте сопоставление групп на стороне федерации {#org-mapping}

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  1. Войдите в сервис [{{ org-full-name }}]({{ link-org-cloud-center }}).

  1. [Создайте группу пользователей](../../organization/operations/create-group.md) `yc-demo-group` в {{ org-name }} и [выдайте ей права](../../organization/operations/access-group.md) на просмотр ресурсов в облаке или отдельном каталоге (роль `viewer`).

  1. На панели слева выберите ![VectorSquare](../../_assets/console-icons/vector-square.svg) **{{ ui-key.yacloud_org.pages.federations }}**.

  1. Выберите созданную ранее федерацию `demo-federation` и перейдите на вкладку **{{ ui-key.yacloud_org.form.group-mapping.note.tab-idp }}**.

  1. Включите опцию **{{ ui-key.yacloud_org.form.group-mapping.field.idp }}**.

  1. Нажмите кнопку **{{ ui-key.yacloud_org.form.group-mapping.create.add }}**.

  1. В поле **{{ ui-key.yacloud_org.form.group-mapping.note.group-name }}** введите идентификатор группы `az_demo_group`, [полученный ранее в {{ microsoft-idp.entra-id-short }}](#create-group).

     {% note warning %}

     При [настройке сопоставления групп на стороне Azure](#map-azure-group) был выбран идентификатор группы в качестве атрибута источника.

     Поэтому необходимо ввести именно идентификатор группы, а не ее имя.

     {% endnote %}

  1. В поле **{{ ui-key.yacloud_org.form.group-mapping.note.iam-group }}** выберите из списка имя группы в {{ org-full-name }} — `yc-demo-group`.

  1. Нажмите кнопку **{{ ui-key.yacloud_org.actions.save-changes }}**.

- {{ TF }} {#tf}

  1. Опишите в конфигурационном файле {{ TF }} параметры создаваемых ресурсов:

      ```hcl
      # Создание группы пользователей
      resource "yandex_organizationmanager_group" "my-group" {
        name            = "yc-demo-group"
        organization_id = "demo-federation"
      }

      # Назначение роли viewer на каталог
      resource "yandex_resourcemanager_folder_iam_member" "viewers" {
        folder_id = "<идентификатор_каталога>"
        role      = "viewer"
        member    = "group:${yandex_organizationmanager_group.my-group.id}"
      }

      # Включение сопоставления групп федеративных пользователей
      resource "yandex_organizationmanager_group_mapping" "my_group_map" {
        federation_id = "demo-federation"
        enabled       = true
      }

      # Настройка сопоставления групп федеративных пользователей
      resource "yandex_organizationmanager_group_mapping_item" "group_mapping_item" {
        federation_id     = "demo-federation"
        internal_group_id = yandex_organizationmanager_group.my-group.id
        external_group_id = "<идентификатор_ группы_az_demo_group>"

        depends_on = [yandex_organizationmanager_group_mapping.group_mapping]
      }
      ```

      Где:
      * `folder_id` — каталог, на который назначается роль.
      * `external_group_id` — идентификатор группы `az_demo_group`, [полученный ранее в {{ microsoft-idp.entra-id-short }}](#create-group).

         {% note warning %}

         При [настройке сопоставления групп на стороне Azure](#map-azure-group) был выбран идентификатор группы в качестве атрибута источника.

         Поэтому необходимо ввести именно идентификатор группы, а не ее имя.

         {% endnote %}

      Подробнее см. в описаниях ресурсов [yandex_organizationmanager_group_mapping]({{ tf-provider-resources-link }}/organizationmanager_group_mapping) и [yandex_organizationmanager_group_mapping_item]({{ tf-provider-resources-link }}/organizationmanager_group_mapping_item) в документации провайдера {{ TF }}.

  1. Создайте ресурсы:

     {% include [terraform-validate-plan-apply](../../_tutorials/_tutorials_includes/terraform-validate-plan-apply.md) %}

{% endlist %}

## Проверьте работу аутентификации {#test-auth}

1. Откройте браузер в гостевом режиме или режиме инкогнито.

1. Перейдите по URL для входа в консоль:

    ```text
    https://{{ console-host }}/federations/<идентификатор_федерации>
    ```

    {% cut "Как получить идентификатор федерации" %}

    {% include [get-federation-id](../../_includes/organization/get-federation-id.md) %}

    {% endcut %}

    Если все настроено правильно, браузер перенаправит вас на страницу аутентификации в {{ microsoft-idp.entra-id-short }}.

1. Введите реквизиты пользователя `az_demo_user@example.com`, [созданного ранее в {{ microsoft-idp.entra-id-short }}](#create-user), и нажмите кнопку **Sign in**.

    После успешной аутентификации IdP-сервер перенаправит вас по URL `https://{{ auth-host }}/federations/<идентификатор_федерации>`, который вы указали в настройках SAML для приложения Azure, а после — на главную страницу [консоли управления]({{ link-console-main }}).

1. Убедитесь, что пользователь, от имени которого был выполнен вход, входит в группу `yc-demo-group` и у него есть права на просмотр ресурсов в соответствии с ролью, назначенной для группы.
