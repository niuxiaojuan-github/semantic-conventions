# Database

**Status**: [Experimental][DocumentStatus]

**type:** `database`

**Description**: A Database

<!-- semconv database -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `db.entity.parent.id` | string | This attribute is used to describe the parent entity ID  of the current object and consists of server.address,  server.port and db.name or instance.name together. | `db.testdb.com:5236@db2inst1` | Conditionally Required: If applicable. |
| `db.entity.type` | string | This attribute is used to describe the type of the current object. | `DATABASE`; `INSTANCE` | Required |
| `db.version` | string | The version of the database. | `v11.5`; `v8` | Required |
| [`server.address`](../attributes-registry/server.md) | string | Name of the database host. [1] | `example.com`; `10.1.2.80`; `/tmp/my.sock` | Required |
| [`server.port`](../attributes-registry/server.md) | int | Database listen port. [2] | `80`; `8080`; `443` | Required |
| [`service.instance.id`](README.md) | string | This attribute is used to describe the entity ID of the current object  and consists of server.address, server.port, and db.name. [3] | `db.testdb.com:5236@DAMENG` | Conditionally Required: If applicable. |
| [`service.name`](README.md) | string | Logical name of the service. [4] | `shoppingcart` | Required |

**[1]:** When observed from the client side, and when communicating through an intermediary, `server.address` SHOULD represent the server address behind any intermediaries, for example proxies, if it's available.

**[2]:** When observed from the client side, and when communicating through an intermediary, `server.port` SHOULD represent the server port behind any intermediaries, for example proxies, if it's available.

**[3]:** MUST be unique for each instance of the same `service.namespace,service.name` pair (in other words `service.namespace,service.name,service.instance.id` triplet MUST be globally unique). The ID helps to distinguish instances of the same service that exist at the same time (e.g. instances of a horizontally scaled service). It is preferable for the ID to be persistent and stay the same for the lifetime of the service instance, however it is acceptable that the ID is ephemeral and changes during important lifetime events for the service (e.g. service restarts). If the service has no inherent unique ID that can be used as the value of this attribute it is recommended to generate a random Version 1 or Version 4 RFC 4122 UUID (services aiming for reproducible UUIDs may also use Version 5, see RFC 4122 for more recommendations).

**[4]:** MUST be the same for all instances of horizontally scaled services. If the value was not specified, SDKs MUST fallback to `unknown_service:` concatenated with [`process.executable.name`](process.md#process), e.g. `unknown_service:bash`. If `process.executable.name` is not available, the value MUST be set to `unknown_service`.
<!-- endsemconv -->


[DocumentStatus]: https://github.com/open-telemetry/opentelemetry-specification/tree/v1.26.0/specification/document-status.md
