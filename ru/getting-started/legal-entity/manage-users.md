---
title: Инструкции для юридических лиц по управлению пользователями
description: Из статьи вы узнаете, как добавить пользователей к созданной организации, назначить администратора и определить роли пользователей для юридических лиц.
---


# Управление пользователями

С помощью инструкций в этом пункте чек-листа вы добавите пользователей к созданной организации, назначите администратора и определите роли пользователей. 

## Добавьте пользователя Яндекса {#add-user-yandex}

Если у сотрудников компании есть аккаунты на Яндексе (например, `login@yandex.ru`), они могут использовать эти аккаунты для доступа к сервисам {{ yandex-cloud }}, подключенным к вашей организации.

Чтобы добавить аккаунты сотрудников в организацию:

{% list tabs group=instructions %}

- Интерфейс {{ cloud-center }} {#cloud-center}

  {% include [add-yandex-user](../../_includes/organization/add-yandex-user.md) %}

- Консоль управления {#console}

    1. Войдите в [консоль управления]({{ link-console-main }}) с учетной записью администратора облака.

    1. В списке слева выберите нужное облако. Пример:

        ![image](../../_assets/resource-manager/switch-cloud-n-n.png)

    1. В правом верхнем углу нажмите на значок ![icon-users](../../_assets/console-icons/ellipsis.svg) и выберите **{{ ui-key.yacloud.common.resource-acl.button_invite-users }}**.

    1. Введите почтовые адреса пользователей, которых вы хотите пригласить в организацию (например, `{{login-example}}`).

        Приглашения можно отправлять на любые адреса электронной почты. Приглашенный пользователь сможет выбрать нужный аккаунт на Яндексе, когда примет приглашение.

    1. Нажмите **{{ ui-key.yacloud_components.organization.action_send-invitation }}**.

{% endlist %}

Пользователи будут подключены к организации, как только примут отправленное им приглашение и выберут аккаунт для входа в организацию.

Чтобы получить доступ к сервисам, которые подключены к организации, приглашенным пользователям будет достаточно войти в свой аккаунт на Яндексе.

## Добавьте федеративных пользователей {#add-user-sso}

Для добавления федеративных пользователей вам необходимо знать Name ID пользователей, которые возвращает сервер поставщика удостоверений (IdP) вместе с ответом об успешной аутентификации. Обычно это основной email пользователя. Если вы не знаете, что возвращает сервер в качестве Name ID, обратитесь к администратору, который настраивал аутентификацию в вашей федерации.

Чтобы добавить пользователей федерации в организацию:

{% include notitle [add-user-sso](../../_includes/organization/add-user-sso.md) %}

## Назначьте роли пользователям {#add-role}

{% list tabs group=instructions %}

- Консоль управления {#console}

  {% include [grant-role-console](../../_includes/grant-role-console.md) %}


- CLI {#cli}

  1. Выберите роль в [справочнике ролей {{ yandex-cloud }}](../../iam/roles-reference.md).
  1. [Получите идентификатор пользователя](../../organization/operations/users-get.md).
  1. Назначьте роль с помощью команды:

    ```bash
    yc <SERVICE-NAME> <RESOURCE> add-access-binding <RESOURCE-NAME>|<RESOURCE-ID> \
      --role <ROLE-ID> \
      --subject userAccount:<USER-ACCOUNT-ID>
    ```

    где:

    * `<SERVICE-NAME>` — имя сервиса, на чей ресурс назначается роль, например `resource-manager`.
    * `<RESOURCE>` — категория ресурса, например `cloud`.
    * `<RESOURCE-NAME>` — имя ресурса. Вы можете указать ресурс по имени или идентификатору.
    * `<RESOURCE-ID>` — идентификатор ресурса.
    * `<ROLE-ID>` — идентификатор роли, например `{{ roles-cloud-owner }}`.
    * `<USER-ACCOUNT-ID>` — идентификатор аккаунта пользователя, которому назначается роль.

    Например, назначьте роль `viewer` на [облако](../../resource-manager/concepts/resources-hierarchy.md#folder) `mycloud`:

    ```bash
    $ yc resource-manager cloud add-access-binding mycloud \
      --role viewer \
      --subject userAccount:aje6o61dvog2h6g9a33s
    ```

- API {#api}

  Воспользуйтесь методом `updateAccessBindings` для соответствующего ресурса.

  1. Выберите роль в [справочнике ролей {{ yandex-cloud }}](../../iam/roles-reference.md).
  1. [Получите идентификатор пользователя](../../organization/operations/users-get.md).
  1. Сформируйте тело запроса, например в файле `body.json`. В свойстве `action` укажите `ADD`, а в свойстве `subject` - тип `userAccount` и идентификатор пользователя:

    **body.json:**
    ```json
    {
      "accessBindingDeltas": [{
        "action": "ADD",
        "accessBinding": {
          "roleId": "editor",
          "subject": {
            "id": "gfei8n54hmfhuk5nogse",
            "type": "userAccount"
            }
          }
        }
      ]
    }
    ```

  1. {% include [grant-role-folder-via-curl-step](../../_includes/iam/grant-role-folder-via-curl-step.md) %} 
  
{% endlist %}


## Назначьте администратора {#admin}

Чтобы дать пользователю права на управление организацией, назначьте ему одну из ролей:

* `organization-manager.admin` — роль администратора организации.
  
  Роль дает возможность редактировать настройки организации, создавать федерации удостоверений, добавлять и удалять пользователей, назначать других администраторов, управлять ресурсами облаков организации.

* `organization-manager.organizations.owner` — роль владельца организации.
  
  Роль дает возможность назначать владельцев организации, а также пользоваться всеми полномочиями администратора.
  
  По умолчанию владелец организации — это пользователь, который ее создал.

* `organization-manager.viewer` — роль дает возможность просматривать настройки организации, но не редактировать их.