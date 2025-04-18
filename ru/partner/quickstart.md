# Как стать участником партнерской программы

{% include [referral-program-closure](../_includes/partner/referral-program-closure.md) %}

Партнерская программа позволяет вам зарабатывать и развивать собственный бизнес, решая задачи клиентов на базе платформы {{ yandex-cloud }}. Вы можете стать партнером в рамках программы [прямого](./program/var.md) или [реферального](./program/referral.md) партнерства, а также участвовать в обеих программах одновременно.

Участие в партнерской программе позволит вам:

* продвигать собственные решения, построенные на базе {{ yandex-cloud }};
* продавать решения для новых заказчиков и увеличивать потребление у текущих клиентов;
* закрывать комплексные проекты, оказывая профессиональные услуги.



Получение статуса партнера состоит из нескольких шагов.

## Шаг 1. Перейдите на страницу онбординга {#step-1}

Порядок действий для получения партнерского статуса описан на странице онбординга:

1. На [странице партнерской программы]({{ link-cloud-partners-landing }}) нажмите кнопку [**Подать заявку**]({{ link-cloud-partners }}welcome).
1. Ознакомьтесь с процессом получения статуса партнера и преимуществами партнерской программы, а затем нажмите карточку **Давайте начнем!**.

## Шаг 2. Пройдите авторизацию {#step-2}

Для авторизации потребуется аккаунт Яндекс ID или SSO-аккаунт в {{ yandex-cloud }}. Вы сможете авторизоваться любым из предложенных способов:

* Использовать существующий аккаунт. Вам будет предложен список ваших недавно использованных аккаунтов {{ yandex-cloud }}. На следующем шаге вы сможете выбрать, какую организацию в этом аккаунте использовать для работы.
* Создать новый аккаунт Яндекс ID. Для вас автоматически будет создана организация {{ yandex-cloud }}.
* Войти через SSO. Используйте, если у вас настроена [федерация удостоверений](../organization/concepts/add-federation.md) в {{ yandex-cloud }}. Организация, в рамках которой была создана федерация удостоверений, будет использована для работы с партнерским порталом.

## Шаг 3. Оставьте заявку на партнерство {#step-3}

В [заявке на партнерство]({{ link-cloud-partners }}onboarding-new) необходимо заполнить контактные данные и информацию о вашей компании для первичной проверки.

1. Убедитесь, что в выпадающем списке вверху страницы выбрана именно та организация {{ yandex-cloud }}, с которой вы планируете работать в партнерской программе.

   * Если в организации есть платежный аккаунт, который соответствует [требованиям](#restrictions), вы сможете использовать его для передачи информации о компании.
   * Если платежного аккаунта нет, в блоке **Данные о компании** вы увидите соответствующее предупреждение и сможете создать его на [шаге 5](#step-5).

1. Укажите информацию о компании: ваши **Контактные данные** и **Данные о компании**. Заполнение **Данных о компании** зависит от наличия платежного аккаунта:

   * есть платежный аккаунт — выберите его из списка и проверьте, что данные указаны корректно. Выбрав аккаунт на этом шаге, вы больше не сможете его изменить.
   * нет платежного аккаунта — укажите ИНН юридического лица. Появится карточка с информацией о юридическом лице, нажмите на нее и убедитесь, что в блоке **Дополнительные данные** все данные отображены корректно.

1. Нажмите кнопку **Отправить заявку**.

## Шаг 4. Выберите тип партнерства {#step-4}

1. Посмотрите до конца информационное видео о партнерской программе {{ yandex-cloud }}.
1. После просмотра видео вы сможете выбрать тип партнерства (можно выбрать оба варианта):

   * [Реферальный партнер](program/referral.md) — привлекает клиентов в {{ yandex-cloud }} по реферальной ссылке за партнерскую комиссию в 10%. Документооборот и проектные работы на стороне {{ yandex-cloud }}.

     {% note info %}

     Для одобрения заявки на реферальное партнерство [укажите расчетный счет](../billing/qa/billing-account.md#checking-account) в платежном аккаунте.

     {% endnote %}

   * [Прямой партнер](program/var.md) — реализует комплексные проекты на базе сервисов {{ yandex-cloud }}: создает и поддерживает инфраструктуру заказчика, ведет документооборот, развивает свою экспертизу и продукты.

1. Укажите **Тип налогообложения компании** и нажмите **Продолжить**.
1. Заполните анкету с вопросами о профилировании вашей компании. Нажмите кнопку **Отправить**.

## Шаг 5. Выберите платежный аккаунт {#step-5}

Если на [шаге 3](#step-3) вы уже указали платежный аккаунт, переходите к следующему шагу. Если платежный аккаунт не был указан, на этом шаге вы сможете его создать.

Требования к платежному аккаунту: {#restrictions}

* тип платежного аккаунта: [Бизнес-аккаунт](../billing/concepts/billing-account.md#ba-types);
* бизнес-аккаунт оформлен на юридическое лицо, резидента РФ;
* способ оплаты — **{{ ui-key.yacloud_billing.billing.account.create-new.payment-type_label_invoice }}**.

1. Нажмите кнопку **Создать платежный аккаунт**.

1. На странице **Общая информация** укажите:

   * **Имя аккаунта** — укажите имя платежного аккаунта;
   * **Страна** — **Россия**;
   * **Плательщик** — будет создан новый плательщик, поле заполнится автоматически;
   * **Тип аккаунта** — **Юр. лицо или ИП**;
   * **Способ оплаты** — **Банковский перевод**.

   Нажмите **Вперед**.

1. Заполните **Данные о компании**: убедитесь, что в заполненных автоматически полях указана корректная информация и заполните недостающие обязательные поля. Нажмите кнопку **Далее**.

1. На странице **Контакты** будут указаны контакты из [шага 3](#step-3). Если плательщиком в платежном аккаунте должен быть другой человек, скорректируйте информацию на этой странице. Нажмите кнопку **Создать**.

1. Заполните [форму](https://forms.yandex.ru/surveys/10033352.3c456c5feb5c56ed6c3112834db3233af1434edc/?language=ru&resident_ru=ru_ru) для верификации созданного платежного аккаунта.

1. [Переведите](../billing/operations/activate-commercial.md) платежный аккаунт из режима пробного периода в платную версию. При переходе в платную версию гранты сохраняются на аккаунте и могут быть использованы для оплаты сервисов.

   {% note info %}

   При переходе из пробного режима в платный никаких дополнительных платежей или взносов не требуется. Счета на оплату будут приходить вам только в случае появления тарифицируемых ресурсов в облаке, которое привязано к платежному аккаунту.

   {% endnote %}

## Шаг 6. Получите подтверждение {{ yandex-cloud }} {#step-6}

На странице подтверждения заявки вы сможете ознакомиться с ее данными и отслеживать статус. Дождитесь обратной связи от менеджера и согласования заявки — оно может занять до 7 рабочих дней.

## Шаг 7. Получите статус партнера {#step-7}

Если вы соответствуете [требованиям](https://yandex.ru/legal/cloud_partnership_requirements/) к партнеру, то после выполнения описанных выше шагов вы станете участником партнерской программы {{ yandex-cloud }} и сможете работать на [партнерском портале]({{ link-cloud-partners }}).

Подробнее о работе на портале см. [{#T}](portal.md).


## Что дальше {#whats-next}

* Ознакомьтесь с условиями и порядком участия в [программе прямого партнерства](program/var.md).
* Изучите основные [термины и определения](./terms.md).
* Узнайте о [технологических специализациях](./specializations/index.md).