---
title: '{{ managed-prometheus-full-name }}'
description: '{{ managed-prometheus-full-name }} is a monitoring system compatible with {{ prometheus-name }}. You can use it to collect, store, and read metrics from your containers, applications, and infrastructure. The system uses the {{ prometheus-name }} data model and the {{ promql-name }} query language. This allows you to work with dashboards existing in {{ grafana-name }}.'
---

# {{ managed-prometheus-full-name }} overview



{{ managed-prometheus-full-name }} is a monitoring system compatible with [{{ prometheus-name }}](https://prometheus.io/docs/introduction/overview/). You can use it to collect, store, and read metrics from your containers, applications, and infrastructure. The system uses the {{ prometheus-name }} data model and the [{{ promql-name }}](https://prometheus.io/docs/prometheus/latest/querying/basics/) query language. This allows you to work with dashboards existing in [{{ grafana-name }}](https://grafana.com/grafana/).

{{ prometheus-name }} provides multiple mechanisms for collecting metrics, including [libraries](https://prometheus.io/docs/instrumenting/clientlibs/) for popular programming languages and support for [exporting metrics](https://prometheus.io/docs/instrumenting/exporters/) from third-party systems, like databases or message queues. {{ managed-prometheus-name }} allows you to use best practices of the {{ prometheus-name }} open source community, while ensuring high scalability, availability, and safety with no need to deploy {{ prometheus-name }} clusters and manage the internal monitoring infrastructure on your own. To get started with {{ managed-prometheus-name }}, just install agents that support the metric [Remote Write](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write) protocol.

{{ managed-prometheus-name }} is based on the system that Yandex uses to monitor internal services. The system automatically scales metric writes, storage, and reads depending on the workload. High availability is ensured thanks to replication of the monitoring data in two availability zones.

## Getting started {#access}

To start working with {{ managed-prometheus-full-name }} in the current folder, you need to create a workspace. Proceed as follows:

1. Open the {{ monitoring-full-name }} [home page]({{ link-monitoring }}).
1. In the left-hand panel, select **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.prometheus.title }}**.
1. Click **Create a workspace**.
1. On the page that opens, you will see links to the endpoints to be used for the current folder.

To enable metric [writes](ingestion/index.md) and [reads](querying/index.md) in {{ prometheus-name }} format, follow the corresponding guides.

## Current features {#features}

{% note info %}

The system functionality will be enhanced in upcoming releases.

{% endnote %}


Feature | {{ prometheus-name }} | {{ managed-prometheus-full-name }}
--- | --- | ---
Collecting metrics | [Scrape](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config), [Pushgateway](https://prometheus.io/docs/instrumenting/pushing/), [Remote Write](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write) | Metric writes via the [Remote Write](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write) protocol are supported. To deliver metrics to {{ managed-prometheus-full-name }}, you can use any compatible metric collection agent, including {{ prometheus-name }} itself.
Long-term metric storage | Not intended for long-term metric storage. [Third-party solutions](https://prometheus.io/docs/prometheus/latest/storage/#existing-integrations) are used for that. | Long-term metric storage is supported. If [decimation](../../concepts/decimation.md) is used, metrics can be stored for an unlimited amount of time.
Reading metrics | Data and metadata reads via the [HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/) are supported. | Data and metadata reads via the [HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/) are supported with some [restrictions](querying/grafana.md#restrictions).
Visualization | [Expression browser](https://prometheus.io/docs/visualization/browser/), [Grafana](https://prometheus.io/docs/visualization/grafana/) | [{{ prometheus-name }} data source](https://grafana.com/docs/grafana/latest/datasources/prometheus/) is supported.
Aggregation | Aggregation via [recording rules](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/) is supported. | Existing [recording rules](recording-rules.md) (`recording rules`) in YAML format are supported. To upload and manage the files, use the {{ monitoring-name }} UI and API.
Alerting | Aggregation via [alerting rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/) is supported. | Existing [alerting rules](alerting-rules.md) (`alerting rules`) in YAML format are supported. To upload and manage the files, use the {{ monitoring-name }} UI and API.
Integration options | Client [libraries](https://prometheus.io/docs/instrumenting/clientlibs/) and [exporters](https://prometheus.io/docs/instrumenting/exporters/). | Existing libraries and exporters can be used.

{% include [alerting-rules-preview](../../../_includes/monitoring/alerting-rules-preview.md) %}

## Current limitations {#restrictions}

* The `NaN` value is not supported and is treated as a missing point.
* The `+Inf`/`-Inf` values may be processed incorrectly.
* For alerting rules (`alerting rules`), the only supported channels are [email](https://prometheus.io/docs/alerting/latest/configuration/#email_config) and [Telegram](https://prometheus.io/docs/alerting/latest/configuration/#telegram_config). [Dynamic routing](https://prometheus.io/docs/alerting/latest/configuration/#route) is not supported.
* `staleness markers`, `exemplars`, and `native histograms` are not supported.

## Quotas and limits {#limits}

{{ managed-prometheus-full-name }} has the following limits:

{% include [quotes-limits-def.md](../../../_includes/quotes-limits-def.md) %}

If you need more resources, contact support at [{{ link-support-mail }}](mailto:{{ link-support-mail }}) and tell us which quotas you want increased and by how much.

### Quotas per endpoint {#quotas-per-endpoint}

Type of limit | Value
----- | -----
Maximum data write speed in [Remote Write](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write) format | 1,000 requests/s and 80 MB/s
Number of metrics per write request | 10,000
Number of [Remote Read API](https://prometheus.io/docs/prometheus/latest/querying/remote_read_api) read requests per second | 200
Number of [HTTP API](https://prometheus.io/docs/prometheus/latest/querying/api/) read requests per second | 200
Number of unique metrics | 20,000,000
Maximum retention period for [expired metrics](../../concepts/ttl.md) ^1^ | 60 days

^1^ A metric expires and is removed if not getting new values for 60 days. Metrics that keep getting new values are stored indefinitely.

Here is what you can do in the **{{ ui-key.yacloud_monitoring.aside-navigation.menu-item.prometheus.title }}** > Workspace > **General information** tab:

* View the number of written metrics.
* Go to the page with service [dashboards](../../concepts/visualization/dashboard) and view the number of requests, errors, and HTTP status codes per second for various endpoints.

### Limits per endpoint {#limits-per-endpoint}

Type of limit | Value
----- | -----
Number of unique labels per metric | 29

{% include [trademark](../../../_includes/monitoring/trademark.md) %}
