# Создание сайта на базе «1С-Битрикс»


[«1С-Битрикс: Управление сайтом»](https://www.1c-bitrix.ru/products/cms/) — это система управления веб-проектами от компании «1С-Битрикс». С его помощью вы можете достаточно просто управлять структурой и содержимым вашего сайта, не обладая специальными знаниями программирования и верстки. Техническую часть работы за вас выполнит «1С-Битрикс: Управление сайтом».

Из этого руководства вы узнаете, как развернуть и настроить сайт, используя шаблон информационного портала на платформе «1С-Битрикс». В процессе настройки вы создадите [виртуальную машину](../../compute/concepts/vm.md) в инфраструктуре {{ yandex-cloud }}, на которой развернете [образ](../../compute/concepts/image.md) платформы «1С-Битрикс» и требуемые для нее сервисы. В качестве базы данных вами будет развернут [кластер](../../managed-mysql/concepts/index.md) [{{ mmy-full-name }}](../../managed-mysql/) с возможностью обеспечения его отказоустойчивости.

Используемые ресурсы для правильной работы «1С-Битрикс»:
* ВМ на базе [Ubuntu 22.04 LTS](/marketplace/products/yc/ubuntu-22-04-lts) с доступом во внешнюю [сеть](../../vpc/concepts/network.md#network), на которой будет установлен «1С-Битрикс».
* Кластер {{ mmy-name }}, являющийся БД для сайта «1С-Битрикс».

Вы можете создать инфраструктуру для сайта на базе «1С-Битрикс» с помощью одного из инструментов:
* [Консоль управления](../../tutorials/web/bitrix-website/console.md) — используйте этот способ, чтобы пошагово создать инфраструктуру в консоли управления {{ yandex-cloud }}.
* [{{ TF }}](../../tutorials/web/bitrix-website/terraform.md) — используйте этот способ, чтобы упростить создание ресурсов и управление ими, используя подход «инфраструктура как код» (IaC). Скачайте пример конфигурации {{ TF }} с репозитория GitHub, а затем разверните инфраструктуру с помощью [{{ TF }}-провайдера {{ yandex-cloud }}]({{ tf-docs-link }}).
