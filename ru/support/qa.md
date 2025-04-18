---
title: Вопросы и ответы про техническую поддержку {{ yandex-cloud }}
description: На странице представлены вопросы и ответы про техническую поддержку {{ yandex-cloud }}.
---

# Вопросы и ответы про техническую поддержку {{ yandex-cloud }}

{% note info %}

В этом разделе собраны советы и рекомендации по работе со службой технической поддержки. Для решения проблем, связанных с сервисами {{ yandex-cloud }}, перейдите в [Центр Поддержки]({{ link-console-support }}).

{% endnote %}

#### Как обратиться в службу технической поддержки? {#support-channels}

Вы можете обратиться в службу технической поддержки в разделе [Поддержка]({{ link-console-support }}) в консоли управления.

#### Как обратиться в службу технической поддержки, если не получается войти в консоль управления? {#requesting-support-without-ui}

Если вы не можете получить доступ к вашему аккаунту на Яндексе, воспользуйтесь [инструкциями по решению проблем](https://yandex.ru/support/passport/troubleshooting/problems.html) в Справке Яндекс ID.

Если вы успешно авторизовались с аккаунтом Яндекса, но не можете подключиться к консоли управления {{ yandex-cloud }}, обратитесь в службу технической поддержки по электронной почте [{{ link-support-mail }}](mailto:{{ link-support-mail }}).

#### Какие категории вопросов и задач можно решать в рамках технической поддержки? {#support-cases-types}

В зависимости от тарифного плана, вы можете обращаться в поддержку с разными вопросами. Подробное описание тарифов приведено в разделе [{#T}](overview.md).

Вне зависимости от тарифного плана вы можете запрашивать у технической поддержки:

* [логи сервисов](request.md#logs), относящиеся к вашим ресурсам и действиям в {{ yandex-cloud }};
* [ваши персональные данные](request.md#personal), которые хранит Яндекс.

#### Как быстро служба технической поддержки отреагирует на обращение? {#reaction-time}

Служба технической поддержки отвечает на запросы 24 часа в сутки, 7 дней в неделю.

Срок обработки конкретного запроса зависит от тарифного плана. Подробное описание тарифов приведено в разделе [{#T}](overview.md).

#### Как быстро служба технической поддержки решит выявленную проблему? {#resolution-time}

Время решения индивидуально для каждого обращения и зависит от характера проблемы. Проблемы в работе приложений и сервисов могут иметь различные причины. Это усложняет оценку времени их решения. Служба технической поддержки будет тесно сотрудничать с вами, чтобы максимально быстро выявить причины неполадок и устранить их.

#### Сколько раз можно обращаться в техническую поддержку? {#support-requests-limit}

Вы можете обращаться в техническую поддержку неограниченное количество раз.

#### Как получить помощь в решении архитектурных задач? {#help-with-arch-tasks}

За помощью в решении архитектурных задач вы можете обратиться через форму обратной связи на странице поддержки в [центре поддержки]({{ link-console-support }}).

#### На какое программное обеспечение сторонних производителей распространяется техническая поддержка {{ yandex-cloud }}? {#supported-third-party-sw}


Техническая поддержка включает диагностирование проблем в работе стороннего программного обеспечения, используемого в связке с облачной инфраструктурой, и помощь в решении выявленных проблем в ограниченном объеме. Инженеры технической поддержки могут помочь с установкой, базовой настройкой и диагностированием следующего ПО:

- [Операционные системы](https://yandex.cloud/ru/marketplace?categories=os) на виртуальных машинах {{ yandex-cloud }}, которые были созданы из предоставленных {{ yandex-cloud }} публичных образов.
- БД и системы управления кластерами, созданные в сервисах:
    - [{{ mkf-full-name }}](../managed-kafka/index.yaml)
    - [{{ mch-full-name }}](../managed-clickhouse/index.yaml)
    - [{{ mgp-full-name }}](../managed-greenplum/index.yaml)
    - [{{ managed-k8s-full-name }}](../managed-kubernetes/index.yaml)
    - [{{ mmg-full-name }}](../managed-mongodb/index.yaml)
    - [{{ mmy-full-name }}](../managed-mysql/index.yaml)
    - [{{ mos-full-name }}](../managed-opensearch/index.yaml)
    - [{{ mrd-full-name }}](../managed-redis/index.yaml)
    - [{{ mpg-full-name }}](../managed-postgresql/index.yaml)



#### Что случится, если вы не сможете решить мою проблему с программным обеспечением стороннего производителя? {#unresolved-third-party-issues}

Если служба технической поддержки не сможет решить проблему с программным обеспечением стороннего производителя, вам нужно будет обратиться в техническую поддержку этого производителя. В некоторых случаях обращение в техническую поддержку требует действующего договора на техническую поддержку с этими производителями или их партнерами.

#### Как изменить тарифный план технической поддержки? {#change-pricing-plan}

{% include [change-plan-roles](../_includes/support/pricing-roles.md) %}

См. детали в инструкции [Изменение тарифного плана](support-center.md#change-pricing).
Ознакомьтесь с [Порядком оказания технической поддержки](overview.md). Повышение тарифного плана происходит сразу, а понижение — первого числа следующего месяца

{% include [clickhouse-disclaimer](../_includes/clickhouse-disclaimer.md) %}