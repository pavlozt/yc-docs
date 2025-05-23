
# Общие вопросы про {{ iam-name }}

#### Для чего нужен сервис {{ iam-full-name }}? {#usage}

{% include notitle [iam-def](../../_includes/iam-def.md) %}

{{ iam-short-name }} позволяет вам:
* приглашать новых пользователей в облако и удалять их из него;
* управлять правами доступа на ресурсы, назначая и отзывая роли;
* создавать [сервисные аккаунты](../../iam/concepts/users/service-accounts.md) — специальные учетные записи для управления ресурсами {{ yandex-cloud }} через API;
* получать IAM-токен, необходимый для авторизации через API.

Другие сервисы {{ yandex-cloud }} используют API сервиса {{ iam-short-name }}, чтобы предоставить вам больше возможностей для управления доступом к ресурсам этих сервисов. Например, {{ compute-full-name }} предоставляет дополнительную роль `compute.images.user` для контроля доступа к образам дисков.

#### Как начать работу с {{ iam-short-name }}? {#start}

Для начала работы с {{ iam-short-name }} необходимо зарегистрироваться в {{ yandex-cloud }}. После регистрации вы сможете использовать функции {{ iam-short-name }}.

В разделе [{#T}](../../iam/quickstart.md) узнайте, как добавить нового пользователя в свое облако и назначить ему роль.

#### Сколько стоит использование сервиса {{ iam-short-name }}? {#price}

Использование сервиса {{ iam-short-name }} не тарифицируется.

{% include [logs](../logs.md) %}
