---
editable: false
sourcePath: en/_api-ref-grpc/serverless/containers/v1/containers/api-ref/grpc/Container/deployRevision.md
---

# Serverless Containers Service, gRPC: ContainerService.DeployRevision

Deploys a revision for the specified container.

## gRPC request

**rpc DeployRevision ([DeployContainerRevisionRequest](#yandex.cloud.serverless.containers.v1.DeployContainerRevisionRequest)) returns ([operation.Operation](#yandex.cloud.operation.Operation))**

## DeployContainerRevisionRequest {#yandex.cloud.serverless.containers.v1.DeployContainerRevisionRequest}

```json
{
  "container_id": "string",
  "description": "string",
  "resources": {
    "memory": "int64",
    "cores": "int64",
    "core_fraction": "int64"
  },
  "execution_timeout": "google.protobuf.Duration",
  "service_account_id": "string",
  "image_spec": {
    "image_url": "string",
    "command": {
      "command": [
        "string"
      ]
    },
    "args": {
      "args": [
        "string"
      ]
    },
    "environment": "map<string, string>",
    "working_dir": "string"
  },
  "concurrency": "int64",
  "secrets": [
    {
      "id": "string",
      "version_id": "string",
      "key": "string",
      // Includes only one of the fields `environment_variable`
      "environment_variable": "string"
      // end of the list of possible fields
    }
  ],
  "connectivity": {
    "network_id": "string",
    "subnet_ids": [
      "string"
    ]
  },
  "provision_policy": {
    "min_instances": "int64"
  },
  "scaling_policy": {
    "zone_instances_limit": "int64",
    "zone_requests_limit": "int64"
  },
  "log_options": {
    "disabled": "bool",
    // Includes only one of the fields `log_group_id`, `folder_id`
    "log_group_id": "string",
    "folder_id": "string",
    // end of the list of possible fields
    "min_level": "Level"
  },
  "storage_mounts": [
    {
      "bucket_id": "string",
      "prefix": "string",
      "read_only": "bool",
      "mount_point_path": "string"
    }
  ],
  "mounts": [
    {
      "mount_point_path": "string",
      "mode": "Mode",
      // Includes only one of the fields `object_storage`, `ephemeral_disk_spec`
      "object_storage": {
        "bucket_id": "string",
        "prefix": "string"
      },
      "ephemeral_disk_spec": {
        "size": "int64",
        "block_size": "int64"
      }
      // end of the list of possible fields
    }
  ],
  "runtime": {
    // Includes only one of the fields `http`, `task`
    "http": "Http",
    "task": "Task"
    // end of the list of possible fields
  },
  "metadata_options": {
    "gce_http_endpoint": "MetadataOption",
    "aws_v1_http_endpoint": "MetadataOption"
  }
}
```

#|
||Field | Description ||
|| container_id | **string**

Required field. ID of the container to create a revision for.

To get a container ID, make a [ContainerService.List](/docs/serverless-containers/containers/api-ref/grpc/Container/list#List) request. ||
|| description | **string**

Description of the revision. ||
|| resources | **[Resources](#yandex.cloud.serverless.containers.v1.Resources)**

Required field. Resources allocated to the revision. ||
|| execution_timeout | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Timeout for the execution of the revision.

If the timeout is exceeded, Serverless Containers responds with a 504 HTTP code. ||
|| service_account_id | **string**

ID of the service account to associate with the revision. ||
|| image_spec | **[ImageSpec](#yandex.cloud.serverless.containers.v1.ImageSpec)**

Required field. Image configuration for the revision. ||
|| concurrency | **int64**

The number of concurrent requests allowed per container instance.

The default value is 1. ||
|| secrets[] | **[Secret](#yandex.cloud.serverless.containers.v1.Secret)**

Yandex Lockbox secrets to be used by the revision. ||
|| connectivity | **[Connectivity](#yandex.cloud.serverless.containers.v1.Connectivity)**

Network access. If specified the revision will be attached to specified network/subnet(s). ||
|| provision_policy | **[ProvisionPolicy](#yandex.cloud.serverless.containers.v1.ProvisionPolicy)**

Policy for provisioning instances of the revision.

The policy is only applied when the revision is ACTIVE. ||
|| scaling_policy | **[ScalingPolicy](#yandex.cloud.serverless.containers.v1.ScalingPolicy)**

Policy for scaling instances of the revision. ||
|| log_options | **[LogOptions](#yandex.cloud.serverless.containers.v1.LogOptions)**

Options for logging from the container. ||
|| storage_mounts[] | **[StorageMount](#yandex.cloud.serverless.containers.v1.StorageMount)**

S3 mounts to be used by the revision. ||
|| mounts[] | **[Mount](#yandex.cloud.serverless.containers.v1.Mount)**

Mounts to be used by the revision. ||
|| runtime | **[Runtime](#yandex.cloud.serverless.containers.v1.Runtime)**

The container's execution mode ||
|| metadata_options | **[MetadataOptions](#yandex.cloud.serverless.containers.v1.MetadataOptions)**

Metadata options for the revision. ||
|#

## Resources {#yandex.cloud.serverless.containers.v1.Resources}

Resources allocated to a revision.

#|
||Field | Description ||
|| memory | **int64**

Amount of memory available to the revision, specified in bytes, multiple of 128MB. ||
|| cores | **int64**

Number of cores available to the revision. ||
|| core_fraction | **int64**

Specifies baseline performance for a core in percent, multiple of 5%.
Should be 100% for cores > 1. ||
|#

## ImageSpec {#yandex.cloud.serverless.containers.v1.ImageSpec}

Revision image specification.

#|
||Field | Description ||
|| image_url | **string**

Required field. Image URL, that is used by the revision. ||
|| command | **[Command](#yandex.cloud.serverless.containers.v1.Command)**

Override for the image's ENTRYPOINT. ||
|| args | **[Args](#yandex.cloud.serverless.containers.v1.Args)**

Override for the image's CMD. ||
|| environment | **object** (map<**string**, **string**>)

Additional environment for the container. ||
|| working_dir | **string**

Override for the image's WORKDIR. ||
|#

## Command {#yandex.cloud.serverless.containers.v1.Command}

#|
||Field | Description ||
|| command[] | **string**

Command that will override ENTRYPOINT of an image.

Commands will be executed as is. The runtime will not substitute environment
variables or execute shell commands. If one wants to do that, they should
invoke shell interpreter with an appropriate shell script. ||
|#

## Args {#yandex.cloud.serverless.containers.v1.Args}

#|
||Field | Description ||
|| args[] | **string**

Arguments that will override CMD of an image.

Arguments will be passed as is. The runtime will not substitute environment
variables or execute shell commands. If one wants to do that, they should
invoke shell interpreter with an appropriate shell script. ||
|#

## Secret {#yandex.cloud.serverless.containers.v1.Secret}

Secret that is available to the container at run time.

#|
||Field | Description ||
|| id | **string**

ID of Yandex Lockbox secret. ||
|| version_id | **string**

ID of Yandex Lockbox secret. ||
|| key | **string**

Key in secret's payload, which value to be delivered into container environment. ||
|| environment_variable | **string**

Environment variable in which secret's value is delivered.

Includes only one of the fields `environment_variable`. ||
|#

## Connectivity {#yandex.cloud.serverless.containers.v1.Connectivity}

Revision connectivity specification.

#|
||Field | Description ||
|| network_id | **string**

Network the revision will have access to. ||
|| subnet_ids[] | **string**

The list of subnets (from the same network) the revision can be attached to.

Deprecated, it is sufficient to specify only network_id, without the list of subnet_ids. ||
|#

## ProvisionPolicy {#yandex.cloud.serverless.containers.v1.ProvisionPolicy}

#|
||Field | Description ||
|| min_instances | **int64**

Minimum number of guaranteed provisioned container instances for all zones
in total. ||
|#

## ScalingPolicy {#yandex.cloud.serverless.containers.v1.ScalingPolicy}

#|
||Field | Description ||
|| zone_instances_limit | **int64**

Upper limit for instance count in each zone.
0 means no limit. ||
|| zone_requests_limit | **int64**

Upper limit of requests count in each zone.
0 means no limit. ||
|#

## LogOptions {#yandex.cloud.serverless.containers.v1.LogOptions}

#|
||Field | Description ||
|| disabled | **bool**

Is logging from container disabled. ||
|| log_group_id | **string**

Entry should be written to log group resolved by ID.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| folder_id | **string**

Entry should be written to default log group for specified folder.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| min_level | enum **Level**

Minimum log entry level.

See [LogLevel.Level](/docs/logging/api-ref/grpc/Export/run#yandex.cloud.logging.v1.LogLevel.Level) for details.

- `LEVEL_UNSPECIFIED`: Default log level.

  Equivalent to not specifying log level at all.
- `TRACE`: Trace log level.

  Possible use case: verbose logging of some business logic.
- `DEBUG`: Debug log level.

  Possible use case: debugging special cases in application logic.
- `INFO`: Info log level.

  Mostly used for information messages.
- `WARN`: Warn log level.

  May be used to alert about significant events.
- `ERROR`: Error log level.

  May be used to alert about errors in infrastructure, logic, etc.
- `FATAL`: Fatal log level.

  May be used to alert about unrecoverable failures and events. ||
|#

## StorageMount {#yandex.cloud.serverless.containers.v1.StorageMount}

#|
||Field | Description ||
|| bucket_id | **string**

Required field. S3 bucket name for mounting. ||
|| prefix | **string**

S3 bucket prefix for mounting. ||
|| read_only | **bool**

Is mount read only. ||
|| mount_point_path | **string**

Required field. Mount point path inside the container for mounting. ||
|#

## Mount {#yandex.cloud.serverless.containers.v1.Mount}

Mount contains an information about version's external storage mount

#|
||Field | Description ||
|| mount_point_path | **string**

Required field. The absolute mount point path inside the container for mounting. ||
|| mode | enum **Mode**

Mount's mode

- `MODE_UNSPECIFIED`
- `READ_ONLY`
- `READ_WRITE` ||
|| object_storage | **[ObjectStorage](#yandex.cloud.serverless.containers.v1.Mount.ObjectStorage)**

Object storage mounts

Includes only one of the fields `object_storage`, `ephemeral_disk_spec`.

Target mount option ||
|| ephemeral_disk_spec | **[DiskSpec](#yandex.cloud.serverless.containers.v1.Mount.DiskSpec)**

Working disk (worker-local non-shared read-write NBS disk templates)

Includes only one of the fields `object_storage`, `ephemeral_disk_spec`.

Target mount option ||
|#

## ObjectStorage {#yandex.cloud.serverless.containers.v1.Mount.ObjectStorage}

ObjectStorage as a mount

#|
||Field | Description ||
|| bucket_id | **string**

Required field. ObjectStorage bucket name for mounting. ||
|| prefix | **string**

ObjectStorage bucket prefix for mounting. ||
|#

## DiskSpec {#yandex.cloud.serverless.containers.v1.Mount.DiskSpec}

Disk as a mount

#|
||Field | Description ||
|| size | **int64**

The size of disk for mount in bytes ||
|| block_size | **int64**

Optional block size of disk for mount in bytes ||
|#

## Runtime {#yandex.cloud.serverless.containers.v1.Runtime}

The container's execution mode

#|
||Field | Description ||
|| http | **[Http](#yandex.cloud.serverless.containers.v1.Runtime.Http)**

The classic one. You need to run an HTTP server inside the container.

Includes only one of the fields `http`, `task`. ||
|| task | **[Task](#yandex.cloud.serverless.containers.v1.Runtime.Task)**

We run a process from ENTRYPOINT inside the container for each user request.

Includes only one of the fields `http`, `task`. ||
|#

## Http {#yandex.cloud.serverless.containers.v1.Runtime.Http}

#|
||Field | Description ||
|| Empty | > ||
|#

## Task {#yandex.cloud.serverless.containers.v1.Runtime.Task}

#|
||Field | Description ||
|| Empty | > ||
|#

## MetadataOptions {#yandex.cloud.serverless.containers.v1.MetadataOptions}

#|
||Field | Description ||
|| gce_http_endpoint | enum **MetadataOption**

Enabled access to GCE flavored metadata

- `METADATA_OPTION_UNSPECIFIED`: Option is default
- `ENABLED`: Option is enabled
- `DISABLED`: Option is disabled ||
|| aws_v1_http_endpoint | enum **MetadataOption**

Enabled access to AWS flavored metadata (IMDSv1)

- `METADATA_OPTION_UNSPECIFIED`: Option is default
- `ENABLED`: Option is enabled
- `DISABLED`: Option is disabled ||
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
    "container_revision_id": "string"
  },
  // Includes only one of the fields `error`, `response`
  "error": "google.rpc.Status",
  "response": {
    "id": "string",
    "container_id": "string",
    "description": "string",
    "created_at": "google.protobuf.Timestamp",
    "image": {
      "image_url": "string",
      "image_digest": "string",
      "command": {
        "command": [
          "string"
        ]
      },
      "args": {
        "args": [
          "string"
        ]
      },
      "environment": "map<string, string>",
      "working_dir": "string"
    },
    "resources": {
      "memory": "int64",
      "cores": "int64",
      "core_fraction": "int64"
    },
    "execution_timeout": "google.protobuf.Duration",
    "concurrency": "int64",
    "service_account_id": "string",
    "status": "Status",
    "secrets": [
      {
        "id": "string",
        "version_id": "string",
        "key": "string",
        // Includes only one of the fields `environment_variable`
        "environment_variable": "string"
        // end of the list of possible fields
      }
    ],
    "connectivity": {
      "network_id": "string",
      "subnet_ids": [
        "string"
      ]
    },
    "provision_policy": {
      "min_instances": "int64"
    },
    "scaling_policy": {
      "zone_instances_limit": "int64",
      "zone_requests_limit": "int64"
    },
    "log_options": {
      "disabled": "bool",
      // Includes only one of the fields `log_group_id`, `folder_id`
      "log_group_id": "string",
      "folder_id": "string",
      // end of the list of possible fields
      "min_level": "Level"
    },
    "storage_mounts": [
      {
        "bucket_id": "string",
        "prefix": "string",
        "read_only": "bool",
        "mount_point_path": "string"
      }
    ],
    "mounts": [
      {
        "mount_point_path": "string",
        "mode": "Mode",
        // Includes only one of the fields `object_storage`, `ephemeral_disk_spec`
        "object_storage": {
          "bucket_id": "string",
          "prefix": "string"
        },
        "ephemeral_disk_spec": {
          "size": "int64",
          "block_size": "int64"
        }
        // end of the list of possible fields
      }
    ],
    "runtime": {
      // Includes only one of the fields `http`, `task`
      "http": "Http",
      "task": "Task"
      // end of the list of possible fields
    },
    "metadata_options": {
      "gce_http_endpoint": "MetadataOption",
      "aws_v1_http_endpoint": "MetadataOption"
    }
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
|| metadata | **[DeployContainerRevisionMetadata](#yandex.cloud.serverless.containers.v1.DeployContainerRevisionMetadata)**

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
|| response | **[Revision](#yandex.cloud.serverless.containers.v1.Revision)**

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

## DeployContainerRevisionMetadata {#yandex.cloud.serverless.containers.v1.DeployContainerRevisionMetadata}

#|
||Field | Description ||
|| container_revision_id | **string**

ID of the revision that is being created. ||
|#

## Revision {#yandex.cloud.serverless.containers.v1.Revision}

#|
||Field | Description ||
|| id | **string**

ID of the revision. ||
|| container_id | **string**

ID of the container that the revision belongs to. ||
|| description | **string**

Description of the revision. ||
|| created_at | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Creation timestamp for the revision. ||
|| image | **[Image](#yandex.cloud.serverless.containers.v1.Image)**

Image configuration for the revision. ||
|| resources | **[Resources](#yandex.cloud.serverless.containers.v1.Resources2)**

Resources allocated to the revision. ||
|| execution_timeout | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Timeout for the execution of the revision.

If the timeout is exceeded, Serverless Containers responds with a 504 HTTP code. ||
|| concurrency | **int64**

The number of concurrent requests allowed per container instance. ||
|| service_account_id | **string**

ID of the service account associated with the revision. ||
|| status | enum **Status**

Status of the revision.

- `STATUS_UNSPECIFIED`
- `CREATING`: Revision is being created.
- `ACTIVE`: Revision is currently used by the container.
- `OBSOLETE`: Revision is not used by the container. May be deleted later. ||
|| secrets[] | **[Secret](#yandex.cloud.serverless.containers.v1.Secret2)**

Yandex Lockbox secrets to be used by the revision. ||
|| connectivity | **[Connectivity](#yandex.cloud.serverless.containers.v1.Connectivity2)**

Network access. If specified the revision will be attached to specified network/subnet(s). ||
|| provision_policy | **[ProvisionPolicy](#yandex.cloud.serverless.containers.v1.ProvisionPolicy2)**

Policy for provisioning instances of the revision.

The policy is only applied when the revision is ACTIVE. ||
|| scaling_policy | **[ScalingPolicy](#yandex.cloud.serverless.containers.v1.ScalingPolicy2)**

Policy for scaling instances of the revision. ||
|| log_options | **[LogOptions](#yandex.cloud.serverless.containers.v1.LogOptions2)**

Options for logging from the container. ||
|| storage_mounts[] | **[StorageMount](#yandex.cloud.serverless.containers.v1.StorageMount2)**

S3 mounts to be used by the revision. ||
|| mounts[] | **[Mount](#yandex.cloud.serverless.containers.v1.Mount2)**

Mounts to be used by the revision. ||
|| runtime | **[Runtime](#yandex.cloud.serverless.containers.v1.Runtime2)**

The container's execution mode ||
|| metadata_options | **[MetadataOptions](#yandex.cloud.serverless.containers.v1.MetadataOptions2)**

Metadata options for the revision. ||
|#

## Image {#yandex.cloud.serverless.containers.v1.Image}

Revision image specification.

#|
||Field | Description ||
|| image_url | **string**

Image URL, that is used by the revision. ||
|| image_digest | **string**

Digest of the image. Calculated at creation time. ||
|| command | **[Command](#yandex.cloud.serverless.containers.v1.Command2)**

Override for the image's ENTRYPOINT. ||
|| args | **[Args](#yandex.cloud.serverless.containers.v1.Args2)**

Override for the image's CMD. ||
|| environment | **object** (map<**string**, **string**>)

Additional environment for the container. ||
|| working_dir | **string**

Override for the image's WORKDIR. ||
|#

## Command {#yandex.cloud.serverless.containers.v1.Command2}

#|
||Field | Description ||
|| command[] | **string**

Command that will override ENTRYPOINT of an image.

Commands will be executed as is. The runtime will not substitute environment
variables or execute shell commands. If one wants to do that, they should
invoke shell interpreter with an appropriate shell script. ||
|#

## Args {#yandex.cloud.serverless.containers.v1.Args2}

#|
||Field | Description ||
|| args[] | **string**

Arguments that will override CMD of an image.

Arguments will be passed as is. The runtime will not substitute environment
variables or execute shell commands. If one wants to do that, they should
invoke shell interpreter with an appropriate shell script. ||
|#

## Resources {#yandex.cloud.serverless.containers.v1.Resources2}

Resources allocated to a revision.

#|
||Field | Description ||
|| memory | **int64**

Amount of memory available to the revision, specified in bytes, multiple of 128MB. ||
|| cores | **int64**

Number of cores available to the revision. ||
|| core_fraction | **int64**

Specifies baseline performance for a core in percent, multiple of 5%.
Should be 100% for cores > 1. ||
|#

## Secret {#yandex.cloud.serverless.containers.v1.Secret2}

Secret that is available to the container at run time.

#|
||Field | Description ||
|| id | **string**

ID of Yandex Lockbox secret. ||
|| version_id | **string**

ID of Yandex Lockbox secret. ||
|| key | **string**

Key in secret's payload, which value to be delivered into container environment. ||
|| environment_variable | **string**

Environment variable in which secret's value is delivered.

Includes only one of the fields `environment_variable`. ||
|#

## Connectivity {#yandex.cloud.serverless.containers.v1.Connectivity2}

Revision connectivity specification.

#|
||Field | Description ||
|| network_id | **string**

Network the revision will have access to. ||
|| subnet_ids[] | **string**

The list of subnets (from the same network) the revision can be attached to.

Deprecated, it is sufficient to specify only network_id, without the list of subnet_ids. ||
|#

## ProvisionPolicy {#yandex.cloud.serverless.containers.v1.ProvisionPolicy2}

#|
||Field | Description ||
|| min_instances | **int64**

Minimum number of guaranteed provisioned container instances for all zones
in total. ||
|#

## ScalingPolicy {#yandex.cloud.serverless.containers.v1.ScalingPolicy2}

#|
||Field | Description ||
|| zone_instances_limit | **int64**

Upper limit for instance count in each zone.
0 means no limit. ||
|| zone_requests_limit | **int64**

Upper limit of requests count in each zone.
0 means no limit. ||
|#

## LogOptions {#yandex.cloud.serverless.containers.v1.LogOptions2}

#|
||Field | Description ||
|| disabled | **bool**

Is logging from container disabled. ||
|| log_group_id | **string**

Entry should be written to log group resolved by ID.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| folder_id | **string**

Entry should be written to default log group for specified folder.

Includes only one of the fields `log_group_id`, `folder_id`.

Log entries destination. ||
|| min_level | enum **Level**

Minimum log entry level.

See [LogLevel.Level](/docs/logging/api-ref/grpc/Export/run#yandex.cloud.logging.v1.LogLevel.Level) for details.

- `LEVEL_UNSPECIFIED`: Default log level.

  Equivalent to not specifying log level at all.
- `TRACE`: Trace log level.

  Possible use case: verbose logging of some business logic.
- `DEBUG`: Debug log level.

  Possible use case: debugging special cases in application logic.
- `INFO`: Info log level.

  Mostly used for information messages.
- `WARN`: Warn log level.

  May be used to alert about significant events.
- `ERROR`: Error log level.

  May be used to alert about errors in infrastructure, logic, etc.
- `FATAL`: Fatal log level.

  May be used to alert about unrecoverable failures and events. ||
|#

## StorageMount {#yandex.cloud.serverless.containers.v1.StorageMount2}

#|
||Field | Description ||
|| bucket_id | **string**

Required field. S3 bucket name for mounting. ||
|| prefix | **string**

S3 bucket prefix for mounting. ||
|| read_only | **bool**

Is mount read only. ||
|| mount_point_path | **string**

Required field. Mount point path inside the container for mounting. ||
|#

## Mount {#yandex.cloud.serverless.containers.v1.Mount2}

Mount contains an information about version's external storage mount

#|
||Field | Description ||
|| mount_point_path | **string**

Required field. The absolute mount point path inside the container for mounting. ||
|| mode | enum **Mode**

Mount's mode

- `MODE_UNSPECIFIED`
- `READ_ONLY`
- `READ_WRITE` ||
|| object_storage | **[ObjectStorage](#yandex.cloud.serverless.containers.v1.Mount.ObjectStorage2)**

Object storage mounts

Includes only one of the fields `object_storage`, `ephemeral_disk_spec`.

Target mount option ||
|| ephemeral_disk_spec | **[DiskSpec](#yandex.cloud.serverless.containers.v1.Mount.DiskSpec2)**

Working disk (worker-local non-shared read-write NBS disk templates)

Includes only one of the fields `object_storage`, `ephemeral_disk_spec`.

Target mount option ||
|#

## ObjectStorage {#yandex.cloud.serverless.containers.v1.Mount.ObjectStorage2}

ObjectStorage as a mount

#|
||Field | Description ||
|| bucket_id | **string**

Required field. ObjectStorage bucket name for mounting. ||
|| prefix | **string**

ObjectStorage bucket prefix for mounting. ||
|#

## DiskSpec {#yandex.cloud.serverless.containers.v1.Mount.DiskSpec2}

Disk as a mount

#|
||Field | Description ||
|| size | **int64**

The size of disk for mount in bytes ||
|| block_size | **int64**

Optional block size of disk for mount in bytes ||
|#

## Runtime {#yandex.cloud.serverless.containers.v1.Runtime2}

The container's execution mode

#|
||Field | Description ||
|| http | **[Http](#yandex.cloud.serverless.containers.v1.Runtime.Http2)**

The classic one. You need to run an HTTP server inside the container.

Includes only one of the fields `http`, `task`. ||
|| task | **[Task](#yandex.cloud.serverless.containers.v1.Runtime.Task2)**

We run a process from ENTRYPOINT inside the container for each user request.

Includes only one of the fields `http`, `task`. ||
|#

## Http {#yandex.cloud.serverless.containers.v1.Runtime.Http2}

#|
||Field | Description ||
|| Empty | > ||
|#

## Task {#yandex.cloud.serverless.containers.v1.Runtime.Task2}

#|
||Field | Description ||
|| Empty | > ||
|#

## MetadataOptions {#yandex.cloud.serverless.containers.v1.MetadataOptions2}

#|
||Field | Description ||
|| gce_http_endpoint | enum **MetadataOption**

Enabled access to GCE flavored metadata

- `METADATA_OPTION_UNSPECIFIED`: Option is default
- `ENABLED`: Option is enabled
- `DISABLED`: Option is disabled ||
|| aws_v1_http_endpoint | enum **MetadataOption**

Enabled access to AWS flavored metadata (IMDSv1)

- `METADATA_OPTION_UNSPECIFIED`: Option is default
- `ENABLED`: Option is enabled
- `DISABLED`: Option is disabled ||
|#