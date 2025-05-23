---
title: Чеклист безопасности аутентификации и авторизации
description: В этой статье вы ознакомитесь с содержимым чеклиста безопасности аутентификации, авторизации и контроля доступа к ресурсам {{ yandex-cloud }}.
---

# Чеклист безопасности аутентификации и авторизации

В разделе представлены рекомендации по использованию возможностей сервисов {{ yandex-cloud }} для обеспечения безопасности аутентификации, авторизации и контроля доступа к ресурсам.

## Аккаунты {#accounts}

&#x2713; **Защита аккаунтов на Яндексе**:

   * Включите для своего аккаунта Яндекс ID и аккаунтов пользователей вашей организации [двухфакторную аутентификацию](https://yandex.ru/support/passport/authorization/twofa.html).
   * Держите в секрете ваш [OAuth-токен](../../iam/concepts/authorization/oauth-token.md). Если кто-то мог узнать ваш OAuth-токен, [отзовите его](https://yandex.ru/dev/oauth/doc/dg/reference/token-invalidate-docpage/) и выпустите новый. Старайтесь не использовать OAuth-токен для аутентификации, если можно использовать IAM-токен. OAuth-токен действует 1 год, а IAM-токен — {{ iam-token-lifetime }}.

&#x2713; **Федерация удостоверений (Single Sign-On, SSO)**: для централизованного управления учетными данными используйте [SAML-совместимые федерации удостоверений](../../organization/concepts/add-federation.md). С помощью федераций удостоверений компания может настроить Single Sign-On аутентификацию в {{ yandex-cloud }} через свой сервер-поставщик удостоверений — так ваши сотрудники смогут получить доступ к сервисам {{ yandex-cloud }} с помощью своих корпоративных аккаунтов и вы сможете управлять пользователями в консоли управления {{ yandex-cloud }}. Настройте двухфакторную авторизацию на стороне поставщика удостоверений.

## Роли и ресурсы {#resources-and-roles}

&#x2713; **Принцип минимальных привилегий**: [назначайте](../../iam/operations/roles/grant.md) сервисные роли (например, `compute.images.user`) вместо примитивных (`auditor`, `viewer`, `editor`, `admin`), см. [список всех ролей](../../iam/roles-reference.md).

   * Назначайте только роли, которые необходимы сейчас. Не назначайте роли, которые могут потребоваться только в будущем.
   * Помните, что когда вы назначаете роль на каталог, облако или организацию, разрешения этой роли [унаследуют все вложенные ресурсы](../../iam/concepts/access-control/index.md#inheritance).
   * Назначайте роль [администратора](../../iam/roles-reference.md#admin) только тем пользователям, которые должны управлять доступом к ресурсам. 
   * Назначайте роль [владельца](../../resource-manager/security/index.md#resource-manager-clouds-owner) облака или организации только тем, кто должен совершать любые действия с ресурсами. Администратор может лишить прав доступа другого администратора, а владелец — отозвать у другого владельца его роль.
   * Назначайте пользователям сервисные и примитивные роли уровня `editor`, чтобы создавать и удалять ресурсы.
   * Применяйте [имперсонацию](../../iam/concepts/access-control/index.md#impersonation) — пользователи могут выполнять необходимые действия с ресурсами облака от имени сервисного аккаунта. Используйте сервисные аккаунты с нужными ролями вместо назначения ролей отдельным пользователям. Такой подход применяется, чтобы временно расширить права пользователя, не прибегая к генерации статических учетных данных.

&#x2713; **Использование роли {{ roles-auditor }} для исключения доступа к данным пользователей**: для пользователей, которые не нуждаются в доступе к данным (таких как внешние подрядчики или аудиторы), назначьте роль `{{ roles-auditor }}` — роль с минимальными привилегиями и без доступа к данным сервисов. Использование роли `{{ roles-auditor }}` по умолчанию позволяет более избирательно управлять доступом и реализовывать принцип минимальных привилегий.

&#x2713; **Защита billing.accounts.owner**: после выполнения первоначальных операций не используйте учетную запись с этой ролью. Для управления платежным аккаунтом назначьте роль `admin`, `editor` или `viewer` на платежный аккаунт выделенному сотруднику организации с федеративным аккаунтом.

&#x2713; **Защита organization-manager.organizations.owner**: передайте роль `organization-manager.organizations.owner` федеративному аккаунту, затем удалите из организации аккаунт на Яндексе с этой ролью. Чтобы минимизировать риски возможных сбоев в работе федерации, выполните шаги, описанные в статье [Удаление аккаунта на Яндексе из организации](../operations/account-deletion.md).

&#x2713; **Использование корректной ресурсной модели**: при разработке модели доступа для вашей инфраструктуры используйте следующий подход:

   * Группировать ресурсы по назначению и помещать их в отдельные каталоги. Для наиболее строгой изоляции — в отдельные облака.
   * Все критичные ресурсы помещайте в отдельные каталоги или облака. К критичным относятся в том числе ресурсы, которые связаны с обработкой платежных данных, персональных данных, данных коммерческой тайны.
   * Общие ресурсы (например, сеть и группы безопасности) поместите в отдельный каталог для разделяемых ресурсов (в случае разделения компонентов по каталогам).

## Сервисные аккаунты {#service-accounts}

&#x2713; **Использование имперсонации, где это возможно**: [имперсонация](../../iam/operations/sa/set-access-bindings.md#impersonation) позволяет пользователю выполнять действия от имени сервисного аккаунта и предоставляет возможность временно расширить права, не прибегая к генерации статических учетных данных.

&#x2713; **Использование сервисного аккаунта для операций изнутри виртуальной машины**: [привяжите к ВМ сервисный аккаунт](../../compute/operations/vm-connect/auth-inside-vm.md). Тогда для аутентификации не нужно будет хранить ключи сервисного аккаунта на виртуальной машине — IAM-токен будет доступен [по ссылке на сервис метаданных](../../compute/operations/vm-connect/auth-inside-vm.md#auth-inside-vm).

&#x2713; **Использование IAM-токена для аутентификации**: если вам нужно использовать статические учетные данные, рассмотрите использование [IAM-токена](../../iam/concepts/authorization/iam-token.md). Срок жизни ключей неограничен, а IAM-токен действует {{ iam-token-lifetime }}. Если IAM-токен оказался [скомпрометирован](../../iam/operations/compromised-credentials.md) или вы не планируете больше его использовать, [отзовите](../../iam/operations/iam-token/revoke-iam-token.md) его.

&#x2713; **Использование сервисных аккаунтов**: используйте [сервисные аккаунты](../../iam/concepts/users/service-accounts.md) для автоматизации работы с {{ yandex-cloud }}. Вы можете проверить дату и время последней аутентификации на странице с информацией о сервисном аккаунте в консоли управления — эта информация позволяет отслеживать случаи несанкционированного использования сервисных аккаунтов.

&#x2713; **Отдельные сервисные аккаунты для выполнения разных задач**: так вы сможете назначить на сервисные аккаунты только те роли, которые необходимы. В любой момент вы сможете отозвать роли у сервисного аккаунта или удалить его, не затронув другие аккаунты.

&#x2713; **Контроль использования ключей**: в [консоли управления]({{ link-console-main }}) на странице с информацией о сервисном аккаунте для каждого ключа отображаются дата и время его последнего использования. Эта информация позволяет отслеживать случаи несанкционированного использования ключей, а также удалять неиспользуемые ключи без риска нарушить работу сервисов {{ yandex-cloud }}.

&#x2713; **Хранение ключей сервисного аккаунта в секрете**: в случае использования статических ключей, [храните их в секретах {{ lockbox-name }}](../../lockbox/tutorials/static-key-in-lockbox/index.md).

&#x2713; **Периодическая ротация ключей сервисных аккаунтов**: ключи без срока действия ([авторизованные ключи](../../iam/concepts/authorization/key.md) и [статические ключи](../../iam/concepts/authorization/access-key.md)) нужно [ротировать самостоятельно](../../iam/operations/compromised-credentials.md#key-reissue). Дату создания можно проверить в свойствах ключа. Рекомендуется ротировать ключи как минимум раз в 90 дней.

## Секреты {#secrets}

&#x2713; **Поиск секретов {{ yandex-cloud }} в открытых источниках**: {{ yandex-cloud }} может искать несколько типов секретов в открытых источниках: API-ключи, {{ iam-short-name }} Cookies, {{ iam-short-name }}-токены, статические ключи доступа, OAuth-токены и серверные ключи {{ captcha-name }}. [Подробнее о поиске различных типов секретов](../operations/search-secrets.md).

&#x2713; **Обработка секретов, попавших в открытый доступ**: если секреты были скомпрометированы, отзовите и перевыпустите секреты, проверьте наличие несанкционированных действий и удалите несанкционированные ресурсы. Сообщите об инциденте в [службу технической поддержки]({{ link-console-support }}) и защитите секреты от уязвимостей. [Подробнее о плане действий по обработке секретов](../../iam/operations/compromised-credentials.md).

&#x2713; **Использование секрета {{ lockbox-name }} для хранения ключей доступа и токенов**: сохраняйте ключи и токены в [секретах {{ lockbox-name }}](../../lockbox/tutorials/static-key-in-lockbox/index.md) и используйте полезную нагрузку этих секретов, когда нужно применить ключ или токен.

&#x2713; **Использование API-ключей с ограниченным доступом**: создавайте API-ключи с минимально необходимыми [областями](../../iam/concepts/authorization/api-key.md#scoped-api-keys) и сроком действия для работы с перечнем необходимых сервисов, чтобы снизить риск несанкционированного использования ключей.
