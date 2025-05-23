# DeleteBucketTagging method

Deletes bucket [labels](../../../concepts/tags.md).

{% include [labels-tags](../../../../_includes/storage/labels-tags.md) %}

{% include [s3-api-intro-include](../../../../_includes/storage/s3-api-intro-include.md) %}

## Request {#request}

```http
DELETE /{bucket}?tagging HTTP/2
```

### Path parameters {#path-parameters}

Parameter | Description
--- | ---
`bucket` | Bucket name.

### Request parameters {#request-params}

Parameter | Description
--- | ---
`tagging` | Required parameter that indicates the type of operation.

### Headers {#request-headers}

Use [common headers](../common-request-headers.md) in your requests.

## Response {#response}

### Headers {#response-headers}

Responses can only contain [common headers](../common-response-headers.md).

### Response codes {#response-codes}

For a list of possible responses, see [{#T}](../response-codes.md).

A successful response does not contain any additional data.

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}