---
title: Managing object custom metadata in {{ objstorage-full-name }}
description: Follow this guide to manage object custom metadata in an {{ objstorage-name }} bucket.
---

# Managing object custom metadata

When uploading an object to {{ objstorage-name }}, you can provide [custom metadata](../../concepts/object.md#user-meta) in the form of `key-value` pairs.

## Uploading an object with metadata {#load-object-with-meta}

{% list tabs group=instructions %}

- AWS CLI {#cli}

  If you do not have the AWS CLI yet, [install and configure it](../../tools/aws-cli.md).

  In the terminal, run this command:

  ```bash
  aws s3api put-object \
    --bucket <bucket_name> \
    --key <object_key> \
    --body <local_file_path> \
    --metadata "<key_1>"="<value_1>","<key_2>"="<value_2>" \
    --endpoint-url=https://{{ s3-storage-host }}
  ```

  Where:
  * `--bucket`: Name of the bucket you want to upload the object to.
  * `--key`: Bucket object [key](../../concepts/object.md#key).
  * `--body`: Path to the local file you want to upload to the bucket.
  * `--metadata`: Custom metadata provided as comma-separated `key-value` pairs. Keys must only consist of [ASCII characters](https://{{ lang }}.wikipedia.org/wiki/ASCII). The value cannot exceed 2 KB.
  * `--endpoint-url`: {{ objstorage-name }} endpoint.

- API {#api}

  Use the [upload](../../s3/api-ref/object/upload.md) S3 API method, e.g., via `curl`.

  Use `curl` [8.3.0](https://curl.se/changes.html) or higher. For more information, see [Example of using the AWS S3 API](../../api-ref/authentication.md#s3-api-example).

  ```bash
  AWS_KEY_ID="<static_key_ID>"
  AWS_SECRET_KEY="<secret_key>"
  LOCAL_FILE="<local_file_path>"
  BUCKET_NAME="<bucket_name>"
  OBJECT_PATH="<object_key>"
  META_KEY_1="<key_1>"
  META_VALUE_1="<value_1>"
  META_KEY_2="<key_2>"
  META_VALUE_2="<value_2>"

  curl \
    --request PUT \
    --user "${AWS_KEY_ID}:${AWS_SECRET_KEY}" \
    --aws-sigv4 "aws:amz:{{ region-id }}:s3" \
    --upload-file "${LOCAL_FILE}" \
    --header "X-Amz-Meta-${META_KEY_1}: ${META_VALUE_1}" \
    --header "X-Amz-Meta-${META_KEY_2}: ${META_VALUE_2}" \
    --verbose \
    "https://{{ s3-storage-host }}/${BUCKET_NAME}/${OBJECT_PATH}"
  ```

  Where:
  * `AWS_KEY_ID`: Static access key [ID](../../../iam/concepts/authorization/access-key.md#key-id).
  * `AWS_SECRET_KEY`: [Secret key](../../../iam/concepts/authorization/access-key.md#private-key).
  * `LOCAL_FILE`: Path to the local file you want to upload to the bucket.
  * `BUCKET_NAME`: Name of the bucket you want to upload the object to.
  * `OBJECT_PATH`: Bucket object [key](../../concepts/object.md#key).
  * `META_KEY_1` and `META_KEY_1`: Custom metadata keys. Keys must only consist of [ASCII characters](https://{{ lang }}.wikipedia.org/wiki/ASCII).
  * `META_VALUE_1` and `META_VALUE_2`: Custom metadata values. The value cannot exceed 2 KB.

{% endlist %}

## Getting object metadata {#get-object-meta}

{% list tabs group=instructions %}

- {{ yandex-cloud }} CLI {#cli}

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. See the description of the CLI command for getting object metadata:

      ```bash
      yc storage s3api head-object --help
      ```

  1. Get a list of buckets in the default folder:

      ```bash
      yc storage bucket list
      ```

      Result:

      ```text
      +------------------+----------------------+-------------+-----------------------+---------------------+
      |       NAME       |      FOLDER ID       |  MAX SIZE   | DEFAULT STORAGE CLASS |     CREATED AT      |
      +------------------+----------------------+-------------+-----------------------+---------------------+
      | first-bucket     | b1gmit33ngp6******** | 53687091200 | STANDARD              | 2022-12-16 13:58:18 |
      +------------------+----------------------+-------------+-----------------------+---------------------+
      ```

  1. Run this command:

      ```bash
      yc storage s3api head-object \
        --bucket <bucket_name> \
        --key <object_key>
      ```

      Where:

      * `--bucket`: Name of your bucket.
      * `--key`: Object [key](../../concepts/object.md#key).

      Result:

      ```bash
      etag: '"d41d8cd98f00b204e9800998********"'
      request_id: 6428ce25********
      accept_ranges: bytes
      content_type: application/octet-stream
      last_modified_at: "2024-10-08T12:36:36Z"
      server_side_encryption: aws:kms
      sse_kms_key_id: abj497vtg3h0********
      ```

- AWS CLI {#aws-cli}

  If you do not have the AWS CLI yet, [install and configure it](../../tools/aws-cli.md).

  In the terminal, run this command:

  ```bash
  aws s3api head-object \
    --bucket <bucket_name> \
    --key <object_key> \
    --endpoint-url=https://{{ s3-storage-host }}
  ```

  Where:
  * `--bucket`: Name of the bucket containing the object.
  * `--key`: Bucket object [key](../../concepts/object.md#key).
  * `--endpoint-url`: {{ objstorage-name }} endpoint.

- API {#api}

  Use the [getObjectMeta](../../s3/api-ref/object/getobjectmeta.md) S3 API method, e.g., via `curl`.

  Use `curl` [8.3.0](https://curl.se/changes.html) or higher. For more information, see [Example of using the AWS S3 API](../../api-ref/authentication.md#s3-api-example).

  ```bash
  AWS_KEY_ID="<static_key_ID>"
  AWS_SECRET_KEY="<secret_key>"
  BUCKET_NAME="<bucket_name>"
  OBJECT_PATH="<object_key>"

  curl \
    --head \
    --user "${AWS_KEY_ID}:${AWS_SECRET_KEY}" \
    --aws-sigv4 "aws:amz:{{ region-id }}:s3" \
    "https://{{ s3-storage-host }}/${BUCKET_NAME}/${OBJECT_PATH}"
  ```

  Where:
  * `AWS_KEY_ID`: Static access key [ID](../../../iam/concepts/authorization/access-key.md#key-id).
  * `AWS_SECRET_KEY`: [Secret key](../../../iam/concepts/authorization/access-key.md#private-key).
  * `BUCKET_NAME`: Name of the bucket containing the object.
  * `OBJECT_PATH`: Bucket object [key](../../concepts/object.md#key).

{% endlist %}

## Updating object metadata {#update-object-meta}

{% note warning %}

The existing custom metadata will be completely overwritten by the new metadata.

{% endnote %}

{% list tabs group=instructions %}

- AWS CLI {#cli}

  If you do not have the AWS CLI yet, [install and configure it](../../tools/aws-cli.md).

  In the terminal, run this command:

  ```bash
  aws s3api copy-object \
    --bucket <bucket_name> \
    --key <object_key> \
    --copy-source <bucket_name>/<object_key> \
    --metadata "<key_1>"="<value_1>","<key_2>"="<value_2>" \
    --metadata-directive REPLACE \
    --endpoint-url=https://{{ s3-storage-host }}
  ```

  Where:
  * `--bucket`: Name of the bucket where you want to update the object metadata.
  * `--key`: Bucket object [key](../../concepts/object.md#key).
  * `--copy-source`: Path to the object in `<bucket_name>/<object_key>` format.
  * `--metadata`: New custom metadata provided as comma-separated `key-value` pairs. Keys must only consist of [ASCII characters](https://{{ lang }}.wikipedia.org/wiki/ASCII). The value cannot exceed 2 KB.
  * `--metadata-directive`: Parameter specifying the need to overwrite the object metadata with new metadata.
  * `--endpoint-url`: {{ objstorage-name }} endpoint.

- API {#api}

  Use the [copy](../../s3/api-ref/object/copy.md) S3 API method, e.g., via `curl`.

  Use `curl` [8.3.0](https://curl.se/changes.html) or higher. For more information, see [Example of using the AWS S3 API](../../api-ref/authentication.md#s3-api-example).

  ```bash
  AWS_KEY_ID="<static_key_ID>"
  AWS_SECRET_KEY="<secret_key>"
  BUCKET_NAME="<bucket_name>"
  OBJECT_PATH="<object_key>"
  META_KEY_1="<key_1>"
  META_VALUE_1="<value_1>"
  META_KEY_2="<key_2>"
  META_VALUE_2="<value_2>"

  curl \
    --request PUT \
    --user "${AWS_KEY_ID}:${AWS_SECRET_KEY}" \
    --aws-sigv4 "aws:amz:{{ region-id }}:s3" \
    --header "X-Amz-Copy-Source: /${BUCKET_NAME}/${OBJECT_PATH}" \
    --header "X-Amz-Metadata-Directive: REPLACE" \
    --header "X-Amz-Meta-${META_KEY_1}: ${META_VALUE_1}" \
    --header "X-Amz-Meta-${META_KEY_2}: ${META_VALUE_2}" \
    --verbose \
    "https://{{ s3-storage-host }}/${BUCKET_NAME}/${OBJECT_PATH}"
  ```

  Where:
  * `AWS_KEY_ID`: Static access key [ID](../../../iam/concepts/authorization/access-key.md#key-id).
  * `AWS_SECRET_KEY`: [Secret key](../../../iam/concepts/authorization/access-key.md#private-key).
  * `BUCKET_NAME`: Name of the bucket where you want to update the object metadata.
  * `OBJECT_PATH`: Bucket object [key](../../concepts/object.md#key).
  * `META_KEY_1` and `META_KEY_1`: New custom metadata keys. Keys must only consist of [ASCII characters](https://{{ lang }}.wikipedia.org/wiki/ASCII).
  * `META_VALUE_1` and `META_VALUE_2`: New custom metadata values. The value cannot exceed 2 KB.

{% endlist %}