# Database Semantic Conventions

# Intruduction

The Semantic Conventions define a common set of (semantic) attributes which provide meaning to data when collecting, producing and consuming it. The benefit to using Semantic Conventions is in following a common naming scheme that can be standardized across a codebase, libraries, and platforms. This allows easier correlation and consumption of data. For more information, see [OpenTelemetry Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/README.md).

OpenTelemetry has defined Semantic Conventions for a couple of areas, database is one of them.  See more information at [Semantic Conventions for Database Calls and Systems](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/database/README.md).

Currently, OpenTelemetry official released [DB Metrics](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/database/database-metrics.md) page only defines metrics specific to SQL and NoSQL clients (connection pools related), there is no semantic conventions defined for database server activities, such as number of sessions, etc. 
We are working with the community to push forward a more comprehensive Semantic Convention with a common description for all databases starting from RDBMS.  

This page tries to describe a semantic convention for the attributes and metrics of generic database activities, the definition will be used to develop generic database sensor. We are considering to propose this to the community.

# Resource attributes

## Database

**Description**: A Database

<!-- semconv database -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `db.entity.parent.id` | string | This attribute is used to describe the parent entity ID  of the current object and consists of server.address,  server.port and db.name or instance.name together. | `db.testdb.com:5236@db2inst1` | Conditionally Required: If applicable. |
| `db.entity.type` | string | This attribute is used to describe the type of the current object. | `DATABASE`; `INSTANCE` | Conditionally Required: If applicable. |
| [`db.name`](../attributes-registry/db.md) | string | This attribute is used to report the name of the database  being accessed. For commands that switch the database, this should be  set to the target database. [1] | `BLUDB`; `DAMENG` | Conditionally Required: If applicable. |
| [`db.system`](../attributes-registry/db.md) | string | An identifier for the database management system (DBMS) product being used. | `db2`; `damengdb` | Required |
| `db.version` | string | The version of the database. | `v11.5`; `v8` | Required |
| [`server.address`](../attributes-registry/server.md) | string | Name of the database host. [2] | `db.testdb.com` | Required |
| [`server.port`](../attributes-registry/server.md) | int | Database listen port. [3] | `50000`; `5236` | Required |
| [`service.instance.id`](README.md) | string | This attribute is used to describe the entity ID of the current object  and consists of server.address, server.port, and db.name. [4] | `db.testdb.com:5236@DAMENG` | Conditionally Required: If applicable. |
| [`service.name`](README.md) | string | This attribute is used to describe the entity name. [5] | `damengdb@DAMENG` | Required |

**[1]:** In some SQL databases, the database name to be used is called "schema name". In case there are multiple layers that could be considered for database name (e.g. Oracle instance name and schema name), the database name to be used is the more specific layer (e.g. Oracle schema name).

**[2]:** The hostname or IP address of the database server.

**[3]:** Listen port of the database server.

**[4]:** MUST be unique for each instance of the same `service.namespace,service.name` pair (in other words `service.namespace,service.name,service.instance.id` triplet MUST be globally unique). The ID helps to distinguish instances of the same service that exist at the same time (e.g. instances of a horizontally scaled service). It is preferable for the ID to be persistent and stay the same for the lifetime of the service instance, however it is acceptable that the ID is ephemeral and changes during important lifetime events for the service (e.g. service restarts). If the service has no inherent unique ID that can be used as the value of this attribute it is recommended to generate a random Version 1 or Version 4 RFC 4122 UUID (services aiming for reproducible UUIDs may also use Version 5, see RFC 4122 for more recommendations).

**[5]:** MUST be the same for all instances of horizontally scaled services. If the value was not specified, SDKs MUST fallback to `unknown_service:` concatenated with [`process.executable.name`](process.md#process), e.g. `unknown_service:bash`. If `process.executable.name` is not available, the value MUST be set to `unknown_service`.
<!-- endsemconv -->

# Common metric instruments

All metrics in `db.database` instruments should be attached to a Database resource and therefore inherit its attributes, like `db.database.system`.

All metrics in `db.instance` instruments should be attached to a [Instance resource](../database/instance-metrics.md) and therefore inherit its attributes, like `db.instance.name`.

<!-- Re-generate TOC with `markdown-toc --no-first-h1 -i` -->

<!-- toc -->

  * [Availability](#availability)
    + [Metric: `db.status`](#metric-dbstatus)
  * [Throughput](#throughput)
  * [Performance](#performance)
  * [Resource Usage](#resource-usage)
  * [Maintenance](#maintenance)

<!-- tocstop -->

## Availability

### Metric: `db.status`

This metric is [required][MetricRequired].

<!-- semconv metric.db.status(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.status` | Gauge | `{status}` | The status of the database. 1 (Active), 0 (Inactive) |
<!-- endsemconv -->

## Throughput

## Performance

## Resource Usage

## Maintenance

# Custom metrics

Please follow the guideline if custom metrics sent, follow this specification to name the custom metrics.
1. [Instrument Naming](https://github.com/open-telemetry/opentelemetry-specification/blob/v1.22.0/specification/common/attribute-naming.md)
2. [Attribute Naming](https://github.com/open-telemetry/opentelemetry-specification/blob/v1.22.0/specification/common/attribute-naming.md)

e.g.
`db.metrics.db_engine.type`,
`db.metrics.data_compress.ratio`

# Reference

[Metrics Data Model](https://opentelemetry.io/docs/specs/otel/metrics/data-model/)

[OpenTelemetry Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/tree/main/docs)

[Resource Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/README.md)

[Trace Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/trace.md)

[Metrics Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/metrics.md)

[General Guidelines](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/metrics.md#general-guidelines)

[Attribute Requirement Levels for Semantic Conventions](https://github.com/open-telemetry/opentelemetry-specification/blob/v1.22.0/specification/common/attribute-requirement-level.md)

[Metric Requirement Levels for Semantic Conventions](https://github.com/open-telemetry/opentelemetry-specification/blob/v1.22.0/specification/metrics/metric-requirement-level.md)


[MetricRequired]: https://github.com/open-telemetry/opentelemetry-specification/tree/v1.26.0/specification/metrics/metric-requirement-level.md#required
[MetricRecommended]: https://github.com/open-telemetry/opentelemetry-specification/tree/v1.26.0/specification/metrics/metric-requirement-level.md#recommended