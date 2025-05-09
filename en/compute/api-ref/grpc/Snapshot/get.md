---
editable: false
sourcePath: en/_api-ref-grpc/compute/v1/api-ref/grpc/Snapshot/get.md
---

# Compute Cloud API, gRPC: SnapshotService.Get

Returns the specified Snapshot resource.

To get the list of available Snapshot resources, make a [List](/docs/compute/api-ref/grpc/Snapshot/list#List) request.

## gRPC request

**rpc Get ([GetSnapshotRequest](#yandex.cloud.compute.v1.GetSnapshotRequest)) returns ([Snapshot](#yandex.cloud.compute.v1.Snapshot))**

## GetSnapshotRequest {#yandex.cloud.compute.v1.GetSnapshotRequest}

```json
{
  "snapshot_id": "string"
}
```

#|
||Field | Description ||
|| snapshot_id | **string**

Required field. ID of the Snapshot resource to return.
To get the snapshot ID, use a [SnapshotService.List](/docs/compute/api-ref/grpc/Snapshot/list#List) request. ||
|#

## Snapshot {#yandex.cloud.compute.v1.Snapshot}

```json
{
  "id": "string",
  "folder_id": "string",
  "created_at": "google.protobuf.Timestamp",
  "name": "string",
  "description": "string",
  "labels": "map<string, string>",
  "storage_size": "int64",
  "disk_size": "int64",
  "product_ids": [
    "string"
  ],
  "status": "Status",
  "source_disk_id": "string",
  "hardware_generation": {
    // Includes only one of the fields `legacy_features`, `generation2_features`
    "legacy_features": {
      "pci_topology": "PCITopology"
    },
    "generation2_features": "Generation2HardwareFeatures"
    // end of the list of possible fields
  },
  "kms_key": {
    "key_id": "string",
    "version_id": "string"
  }
}
```

A Snapshot resource. For more information, see [Snapshots](/docs/compute/concepts/snapshot).

#|
||Field | Description ||
|| id | **string**

ID of the snapshot. ||
|| folder_id | **string**

ID of the folder that the snapshot belongs to. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)** ||
|| name | **string**

Name of the snapshot. 1-63 characters long. ||
|| description | **string**

Description of the snapshot. 0-256 characters long. ||
|| labels | **object** (map<**string**, **string**>)

Resource labels as `key:value` pairs. Maximum of 64 per resource. ||
|| storage_size | **int64**

Size of the snapshot, specified in bytes. ||
|| disk_size | **int64**

Size of the disk when the snapshot was created, specified in bytes. ||
|| product_ids[] | **string**

License IDs that indicate which licenses are attached to this resource.
License IDs are used to calculate additional charges for the use of the virtual machine.

The correct license ID is generated by the platform. IDs are inherited by new resources created from this resource.

If you know the license IDs, specify them when you create the image.
For example, if you create a disk image using a third-party utility and load it into Object Storage, the license IDs will be lost.
You can specify them in the [yandex.cloud.compute.v1.ImageService.Create](/docs/compute/api-ref/grpc/Image/create#Create) request. ||
|| status | enum **Status**

Current status of the snapshot.

- `STATUS_UNSPECIFIED`
- `CREATING`: Snapshot is being created.
- `READY`: Snapshot is ready to use.
- `ERROR`: Snapshot encountered a problem and cannot operate.
- `DELETING`: Snapshot is being deleted. ||
|| source_disk_id | **string**

ID of the source disk used to create this snapshot. ||
|| hardware_generation | **[HardwareGeneration](#yandex.cloud.compute.v1.HardwareGeneration)**

If specified, forces the same HardwareGeneration features to be applied to the instance
created using this snapshot as source for the boot disk. Otherwise the current default will be used. ||
|| kms_key | **[KMSKey](#yandex.cloud.compute.v1.KMSKey)**

Key encryption key info. ||
|#

## HardwareGeneration {#yandex.cloud.compute.v1.HardwareGeneration}

A set of features, specific to a particular Compute hardware generation.
They are not necessary supported by every host OS or distro, thus they are fixed to an image
and are applied to all instances created with it as their boot disk image.
These features significantly determine how the instance is created, thus cannot be changed after the fact.

#|
||Field | Description ||
|| legacy_features | **[LegacyHardwareFeatures](#yandex.cloud.compute.v1.LegacyHardwareFeatures)**

Includes only one of the fields `legacy_features`, `generation2_features`. ||
|| generation2_features | **[Generation2HardwareFeatures](#yandex.cloud.compute.v1.Generation2HardwareFeatures)**

Includes only one of the fields `legacy_features`, `generation2_features`. ||
|#

## LegacyHardwareFeatures {#yandex.cloud.compute.v1.LegacyHardwareFeatures}

A first hardware generation, by default compatible with all legacy images.
Allows switching to PCI_TOPOLOGY_V2 and back.

#|
||Field | Description ||
|| pci_topology | enum **PCITopology**

- `PCI_TOPOLOGY_UNSPECIFIED`
- `PCI_TOPOLOGY_V1`
- `PCI_TOPOLOGY_V2` ||
|#

## Generation2HardwareFeatures {#yandex.cloud.compute.v1.Generation2HardwareFeatures}

A second hardware generation, which by default assumes PCI_TOPOLOGY_V2
and UEFI boot (with UEFI related features).

#|
||Field | Description ||
|| Empty | > ||
|#

## KMSKey {#yandex.cloud.compute.v1.KMSKey}

#|
||Field | Description ||
|| key_id | **string**

ID of KMS symmetric key ||
|| version_id | **string**

Version of KMS symmetric key ||
|#