---
editable: false
sourcePath: en/_cli-ref/cli-ref/compute/cli-ref/instance-group/list-logs.md
---

# yc compute instance-group list-logs

List logs for the specified instance group

#### Command Usage

Syntax: 

`yc compute instance-group list-logs <INSTANCE-GROUP-NAME>|<INSTANCE-GROUP-ID> [Flags...] [Global Flags...]`

Aliases: 

- `logs`

#### Flags

| Flag | Description |
|----|----|
|`--id`|<b>`string`</b><br/>instance group id.|
|`--name`|<b>`string`</b><br/>instance group name.|
|`--limit`|<b>`int`</b><br/>The maximum number of items to list. Default is 1000 items|
|`--filter`|<b>`string`</b><br/>Filter expression|
|`--since`|<b>`timestamp`</b><br/>Start timestamp for the event list. Format: timestamp in HH:MM:SS format or RFC-3339, or duration since now.<br/>Examples: '15:04:05', '2006-01-02T15:04:05Z', '2h', '3h30m ago'|
|`--until`|<b>`timestamp`</b><br/>End timestamp for the event list. Format: timestamp in HH:MM:SS format or RFC-3339, or duration since now.<br/>Examples: '15:04:05', '2006-01-02T15:04:05Z', '2h', '3h30m ago'|

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
