---
editable: false
sourcePath: en/_cli-ref/cli-ref/managed-clickhouse/cli-ref/user/add-quota.md
---

# yc managed-clickhouse user add-quota

Add quota to a ClickHouse user.

#### Command Usage

Syntax: 

`yc managed-clickhouse user add-quota <USER-NAME> [Flags...] [Global Flags...]`

#### Flags

| Flag | Description |
|----|----|
|`--interval`|<b>`duration|int64`</b><br/>Duration of interval for quota in milliseconds. Possible to use time units - "1m30s". Minimal value is 1 second.|
|`--queries`|<b>`int`</b><br/>The total number of queries. 0 - unlimited.|
|`--errors`|<b>`int`</b><br/>The number of queries that threw exception. 0 - unlimited.|
|`--result-rows`|<b>`int`</b><br/>The total number of rows given as the result. 0 - unlimited.|
|`--read-rows`|<b>`int`</b><br/>The total number of source rows read from tables for running the query, on all remote servers. 0 - unlimited.|
|`--execution-time`|<b>`duration|int64`</b><br/>The total query execution time, in milliseconds (wall time). Possible to use time units - "1m30s". 0 - unlimited.|
|`--cluster-id`|<b>`string`</b><br/>ID of the ClickHouse cluster.|
|`--cluster-name`|<b>`string`</b><br/>Name of the ClickHouse cluster.|
|`--async`|Display information about the operation in progress, without waiting for the operation to complete.|

#### Global Flags

| Flag | Description |
|----|----|
|`--profile`|<b>`string`</b><br/>Set the custom configuration file.|
|`--debug`|Debug logging.|
|`--debug-grpc`|Debug gRPC logging. Very verbose, used for debugging connection problems.|
|`--no-user-output`|Disable printing user intended output to stderr.|
|`--retry`|<b>`int`</b><br/>Enable gRPC retries. By default, retries are enabled with maximum 5 attempts.<br/>Pass 0 to disable retries. Pass any negative value for infinite retries.<br/>Even infinite retries are capped with 2 minutes timeout.|
|`--cloud-id`|<b>`string`</b><br/>Set the ID of the cloud to use.|
|`--folder-id`|<b>`string`</b><br/>Set the ID of the folder to use.|
|`--folder-name`|<b>`string`</b><br/>Set the name of the folder to use (will be resolved to id).|
|`--endpoint`|<b>`string`</b><br/>Set the Cloud API endpoint (host:port).|
|`--token`|<b>`string`</b><br/>Set the OAuth token to use.|
|`--impersonate-service-account-id`|<b>`string`</b><br/>Set the ID of the service account to impersonate.|
|`--no-browser`|Disable opening browser for authentication.|
|`--format`|<b>`string`</b><br/>Set the output format: text (default), yaml, json, json-rest.|
|`--jq`|<b>`string`</b><br/>Query to select values from the response using jq syntax|
|`-h`,`--help`|Display help for the command.|
