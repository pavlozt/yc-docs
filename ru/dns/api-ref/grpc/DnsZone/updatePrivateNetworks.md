---
editable: false
sourcePath: en/_api-ref-grpc/dns/v1/api-ref/grpc/DnsZone/updatePrivateNetworks.md
---

# Cloud DNS API, gRPC: DnsZoneService.UpdatePrivateNetworks

Atomically updates zone private networks

## gRPC request

**rpc UpdatePrivateNetworks ([UpdateDnsZonePrivateNetworksRequest](#yandex.cloud.dns.v1.UpdateDnsZonePrivateNetworksRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## UpdateDnsZonePrivateNetworksRequest {#yandex.cloud.dns.v1.UpdateDnsZonePrivateNetworksRequest}

```json
{
  "dns_zone_id": "string",
  "private_network_id_additions": [
    "string"
  ],
  "private_network_id_deletions": [
    "string"
  ]
}
```

#|
||Field | Description ||
|| dns_zone_id | **string**

ID of the DNS zone which private networks will be updated ||
|| private_network_id_additions[] | **string**

Network IDs to remove ||
|| private_network_id_deletions[] | **string**

Network IDs to add ||
|#

## operation.Operation {#yandex.cloud.operation.Operation}

```json
{
  "id": "string",
  "description": "string",
  "created_at": "google.protobuf.Timestamp",
  "created_by": "string",
  "modified_at": "google.protobuf.Timestamp",
  "done": "bool",
  "metadata": {
    "dns_zone_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "folder_id": "string",
    "created_at": "google.protobuf.Timestamp",
    "name": "string",
    "description": "string",
    "labels": "map<string, string>",
    "zone": "string",
    "private_visibility": {
      "network_ids": [
        "string"
      ]
    },
    "public_visibility": "PublicVisibility",
    "deletion_protection": "bool"
  }
  // end of the list of possible fields
}
```

An Operation resource. For more information, see [Operation](/docs/api-design-guide/concepts/operation).

#|
||Field | Description ||
|| id | **string**

ID of the operation. ||
|| description | **string**

Description of the operation. 0-256 characters long. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| created_by | **string**

ID of the user or service account who initiated the operation. ||
|| modified_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

The time when the Operation resource was last modified. ||
|| done | **bool**

If the value is `false`, it means the operation is still in progress.
If `true`, the operation is completed, and either `error` or `response` is available. ||
|| metadata | **[UpdateDnsZonePrivateNetworksMetadata](#yandex.cloud.dns.v1.UpdateDnsZonePrivateNetworksMetadata)**

Service-specific metadata associated with the operation.
It typically contains the ID of the target resource that the operation is performed on.
Any method that returns a long-running operation should document the metadata type, if any. ||
|| error | **[google.rpc.Status](https://cloud.google.com/tasks/docs/reference/rpc/google.rpc#status)**

The error result of the operation in case of failure or cancellation.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|| response | **[DnsZone](#yandex.cloud.dns.v1.DnsZone)**

The normal response of the operation in case of success.
If the original method returns no data on success, such as Delete,
the response is [google.protobuf.Empty](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#google.protobuf.Empty).
If the original method is the standard Create/Update,
the response should be the target resource of the operation.
Any method that returns a long-running operation should document the response type, if any.

Includes only one of the fields `error`, `response`.

The operation result.
If `done == false` and there was no failure detected, neither `error` nor `response` is set.
If `done == false` and there was a failure detected, `error` is set.
If `done == true`, exactly one of `error` or `response` is set. ||
|#

## UpdateDnsZonePrivateNetworksMetadata {#yandex.cloud.dns.v1.UpdateDnsZonePrivateNetworksMetadata}

#|
||Field | Description ||
|| dns_zone_id | **string**

ID of the DNS zone which private networks was updated ||
|#

## DnsZone {#yandex.cloud.dns.v1.DnsZone}

A DNS zone. For details about the concept, see [DNS zones](/docs/dns/concepts/dns-zone).

#|
||Field | Description ||
|| id | **string**

ID of the DNS zone. Generated at creation time. ||
|| folder_id | **string**

ID of the folder that the DNS zone belongs to. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp. ||
|| name | **string**

Name of the DNS zone.
The name is unique within the folder. ||
|| description | **string**

Description of the DNS zone. ||
|| labels | **object** (map<**string**, **string**>)

DNS zone labels as `key:value` pairs. ||
|| zone | **string**

DNS zone suffix. ||
|| private_visibility | **[PrivateVisibility](#yandex.cloud.dns.v1.PrivateVisibility)**

Privately visible zone settings.
Specifies whether records within the zone are visible from a VPC networks only. ||
|| public_visibility | **[PublicVisibility](#yandex.cloud.dns.v1.PublicVisibility)**

Publicly visible zone settings.
Indicates whether records within the zone are publicly visible. ||
|| deletion_protection | **bool**

Prevents accidental zone removal. ||
|#

## PrivateVisibility {#yandex.cloud.dns.v1.PrivateVisibility}

Configuration for privately visible zones.

#|
||Field | Description ||
|| network_ids[] | **string**

Network IDs. ||
|#

## PublicVisibility {#yandex.cloud.dns.v1.PublicVisibility}

Configuration for publicly visible zones.

#|
||Field | Description ||
|| Empty | > ||
|#