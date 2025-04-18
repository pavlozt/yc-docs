---
title: Рекомендации по использованию {{ managed-k8s-full-name }}
description: Используйте эти рекомендации, если вашим приложениям требуется высокая доступность и отказоустойчивость, масштабирование нагрузки и изоляция ресурсов.
---

# Рекомендации по использованию {{ managed-k8s-name }}


Используйте эти рекомендации для ваших `PRODUCTION`-приложений, которым требуется:
* Высокая доступность и [отказоустойчивость](../../architecture/fault-tolerance.md).
* Масштабирование нагрузки.
* Изоляция ресурсов.

## Высокая доступность и отказоустойчивость {#high-availability}

* Используйте [релизный канал](../concepts/release-channels-and-updates.md) `REGULAR` или `STABLE`. Для всех версий {{ k8s }} доступны [NodeLocal DNS](../tutorials/node-local-dns.md) и [Pod Topology Constraints](https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/).

{% note tip %}

Используйте релизный канал `RAPID` для тестовых окружений, чтобы быстрее тестировать обновления {{ k8s }} и {{ managed-k8s-name }}.

{% endnote %}

* Обновляйте [кластер](./index.md#kubernetes-cluster) и [группы узлов](./index.md#node-group) вручную. Для этого отключите автоматические обновления [мастера](../operations/kubernetes-cluster/kubernetes-cluster-update.md) и [групп узлов](../operations/node-group/node-group-update.md).
* Выбирайте [высокодоступный](../concepts/index.md#master) тип мастера, размещенный в трех зонах. [Сервисы](service.md) {{ k8s }} будут доступны в случае сбоя на уровне [зоны доступности](../../overview/concepts/geo-scope.md). [Соглашение об уровне обслуживания](https://yandex.ru/legal/cloud_sla_kb/) сервиса {{ managed-k8s-name }} распространяется на конфигурацию с высокодоступным мастером, размещенным в трех зонах.
* Разворачивайте сервисы типа `Deployment` и `StatefulSet` в нескольких экземплярах в разных зонах доступности. Используйте стратегии [Pod Topology Constraints](https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/) и [AntiAffinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity), чтобы обеспечить высокую доступность сервисов и эффективное потребление ресурсов кластера {{ k8s }}.

  Для всех стратегий используйте комбинации меток:
  * `topology.kubernetes.io/zone`, чтобы сервисы сохраняли доступность в случае отказа зоны доступности.
  * `kubernetes.io/hostname`, чтобы сервисы сохраняли доступность в случае отказа узла кластера.

  {% note tip %}

  Протестируйте стратегии на тестовом окружении перед внедрением в `PRODUCTION`.

  {% endnote %}

## Масштабирование нагрузки {#scaling}

Используйте эти рекомендации, если нагрузка на ваш кластер {{ managed-k8s-name }} постоянно растет:
* Для повышения надежности кластера [создавайте группы узлов](../operations/node-group/node-group-create.md) с [автоматическим масштабированием](autoscale.md) в нескольких зонах доступности.
* Для снижения нагрузки на {{ k8s }} DNS используйте [NodeLocal DNS](../tutorials/node-local-dns.md). Если кластер содержит более 50 узлов, используйте [автоматическое масштабирование DNS](../tutorials/dns-autoscaler.md).
* Чтобы снизить горизонтальный трафик внутри кластера, используйте [Сетевой балансировщик нагрузки](../operations/create-load-balancer.md) и [правило `externalTrafficPolicy:Local`](../operations/create-load-balancer.md#advanced), если это возможно.
* Заранее продумайте требования к хранилищам узлов:
  * Изучите [лимиты дисков](../../compute/concepts/limits.md) для {{ compute-full-name }}.
  * Проведите нагрузочное тестирование дисковой подсистемы в тестовом окружении.
* Для снижения задержки при высоких показателях IOPS используйте [нереплицируемые диски](../../compute/concepts/disk.md#disks_types).

## Изоляция ресурсов {#isolation}

Применяйте эти рекомендации для приложений, использующих общие ресурсы кластера {{ k8s }}.

Настройте значения `limits` и `requests` для всех сервисов кластера:

```yaml
---
...
containers:
...
  resources:
    limits:
      cpu: 250m
      memory: 128Mi
    requests:
      cpu: 100m
      memory: 64Mi
...
```

Укажите доступность vCPU в тысячных долях, а RAM — в мегабайтах. Сервис не превысит лимиты vCPU и RAM, указанные в значениях `limits`. Настройка `requests` позволяет масштабировать узлы кластера при помощи автоматического масштабирования.

Чтобы автоматически управлять ресурсами подов, настройте политики {{ k8s }}:
* [Quality of Service for Pods](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) для создания подов различных классов доступности.
* [Limit Ranges](https://kubernetes.io/docs/concepts/policy/limit-range/) для установки лимитов на уровне [пространства имен](../concepts/index.md#namespace).