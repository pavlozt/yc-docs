---
title: '{{ TF }} reference for {{ mgp-full-name }}'
description: This page provides reference information on the {{ TF }} provider resources and data sources supported for {{ mgp-name }}.
---

# {{ TF }} reference for {{ mgp-full-name }}

{% include [terraform-ref-intro](../_includes/terraform-ref-intro.md) %}

## Resources {#resources}

The following {{ TF }} provider resources are supported for {{ mgp-name }}:

| **{{ TF }} resource** | **{{ yandex-cloud }} resource** |
| --- | --- |
| [yandex_mdb_greenplum_cluster]({{ tf-provider-resources-link }}/mdb_greenplum_cluster) | [Cluster](./concepts/index.md) |

## Data sources {#data-sources}

{{ mgp-name }} supports the following {{ TF }} provider data sources:

| **{{ TF }} data source** | **Description** |
| --- | --- |
| [yandex_mdb_greenplum_cluster]({{ tf-provider-datasources-link }}/mdb_greenplum_cluster) | [Cluster](./concepts/index.md) information |