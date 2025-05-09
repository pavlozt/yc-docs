# selectObjectContent method

Filters and returns the contents of an {{ objstorage-name }} object using an [S3 Select request](../../../concepts/s3-select-language.md).

{% note warning %}

To be able to make S3 Select requests, contact [support](../../../../support/overview.md). You must also have the `s3:GetObject` permission. For more information, see [{#T}](../../../concepts/policy.md).

{% endnote %}

Features of objects you can query:

* Objects in CSV, JSON, and Parquet formats are supported.

* UTF-8 encoded objects are supported.

* GZIP and BZIP2 compression methods are supported for CSV files. For Parquet files, columnar compression with GZIP, Snappy, and ZSTD is supported.

* Objects can be protected by server-side encryption.

For objects that are encrypted with managed encryption keys stored in YC Key Management Service (SSE-KMS), server-side encryption is transparent. This means you do not need to specify anything else.

{% include [s3-api-intro-include](../../../../_includes/storage/s3-api-intro-include.md) %}

## Request {#request}

```
POST /{bucket}/{key}?select&select-type=2 HTTP/2
```

### Path parameters {#path-parameters}

Parameter | Description
----- | -----
`bucket` | Bucket name.
`key` | Object key.


### Request parameters {#request-params}

Parameter | Description
----- | -----
`select` | Required parameter that indicates the type of operation.
`select-type` | Optional parameter that indicates the type of query.


### Headers {#request-headers}

Use the appropriate [common headers](../common-request-headers.md) in your request.


### Data schema {#request-scheme}

Request parameters are provided in XML format:

```
<SelectObjectContentRequest xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <Expression>string</Expression>
   <ExpressionType>string</ExpressionType>
   <RequestProgress>
      <Enabled>boolean</Enabled>
   </RequestProgress>
   <InputSerialization>
      <CompressionType>string</CompressionType>
      <CSV>
         <AllowQuotedRecordDelimiter>boolean</AllowQuotedRecordDelimiter>
         <Comments>string</Comments>
         <FieldDelimiter>string</FieldDelimiter>
         <FileHeaderInfo>string</FileHeaderInfo>
         <QuoteCharacter>string</QuoteCharacter>
         <QuoteEscapeCharacter>string</QuoteEscapeCharacter>
         <RecordDelimiter>string</RecordDelimiter>
      </CSV>
      <JSON>
         <Type>string</Type>
      </JSON>
      <Parquet>
      </Parquet>
   </InputSerialization>
   <OutputSerialization>
      <CSV>
         <FieldDelimiter>string</FieldDelimiter>
         <QuoteCharacter>string</QuoteCharacter>
         <QuoteEscapeCharacter>string</QuoteEscapeCharacter>
         <QuoteFields>string</QuoteFields>
         <RecordDelimiter>string</RecordDelimiter>
      </CSV>
      <JSON>
         <RecordDelimiter>string</RecordDelimiter>
      </JSON>
   </OutputSerialization>
   <ScanRange>
      <End>long</End>
      <Start>long</Start>
   </ScanRange>
</SelectObjectContentRequest>
```

Tag | Description
----- | -----
`SelectObjectContentRequest` | Root-level tag used for providing request parameters.<br/><br/>This tag is required.<br/><br/>Path: `/SelectObjectContentRequest`.
`Expression` | SQL expression used to query object data.<br/><br/>Data type: String.<br/><br/>This tag is required.<br/><br/>Path: `/SelectObjectContentRequest/Expression`.
`ExpressionType` | Expression type used for the request.<br/><br/>Data type: String.<br/><br/>The acceptable value is `SQL`.<br/><br/>This tag is required.<br/><br/>Path: `/SelectObjectContentRequest/ExpressionType`.
`InputSerialization` | Description of data format in the queried object.<br/><br/>Data type: `InputSerialization`.<br/><br/>This tag is required.<br/><br/>Path: `/SelectObjectContentRequest/InputSerialization`.
`OutputSerialization` | Description of the returned data format.<br/><br/>Data type: `OutputSerialization`.<br/><br/>This tag is required.<br/><br/>Path: `/SelectObjectContentRequest/OutputSerialization`.
`RequestProgress` | Tag that indicates whether regular notifications on the request progress are enabled.<br/><br/>Data type: `RequestProgress`.<br/><br/>This tag is optional.<br/><br/>Path: `/SelectObjectContentRequest/RequestProgress`.
`ScanRange` | Tag that specifies the byte range of the object to get the records from. A record is processed when its first byte is within the range. It is used only for objects in CSV format.<br/><br/>Data type: `ScanRange`.<br/><br/>This tag is optional.<br/><br/>Path: `/SelectObjectContentRequest/ScanRange`.

## Response {#response}

### Headers {#response-headers}

Responses can only contain [common headers](../common-response-headers.md).

### Response codes {#response-codes}

For a list of possible responses, see [{#T}](../response-codes.md).

A successful response contains additional data in XML format with the schema described below.

### Data schema {#response-scheme}

```
<Payload>
   <Records>
      <Payload>blob</Payload>
   </Records>
   <Stats>
      <Details>
         <BytesProcessed>long</BytesProcessed>
         <BytesReturned>long</BytesReturned>
         <BytesScanned>long</BytesScanned>
      </Details>
   </Stats>
   <Progress>
      <Details>
         <BytesProcessed>long</BytesProcessed>
         <BytesReturned>long</BytesReturned>
         <BytesScanned>long</BytesScanned>
      </Details>
   </Progress>
   <Cont>
   </Cont>
   <End>
   </End>
</Payload>
```

Tag | Description
----- | -----
`Payload` | Root-level tag used for providing response parameters.<br/><br/>Path: `/Payload`.
`Cont` | Message indicating that the request is being processed.<br/><br/>Path: `/Payload/Cont`.
`End` | Message indicating that the request has been processed.<br/><br/>Path: `/Payload/End`.
`Progress` | Information about the request progress.<br/><br/>Path: `/Payload/Progress`.
`Records` | Request result.<br/>Path: `/Payload/Records`.
`Stats` | Statistics on the processed data, which is sent once at the end of the request.<br/>Path: `/Payload/Stats`.

{% include [the-s3-api-see-also-include](../../../../_includes/storage/the-s3-api-see-also-include.md) %}