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
    + [Metric: `db.instance.count`](#metric-dbinstancecount)
    + [Metric: `db.instance.active.count`](#metric-dbinstanceactivecount)
  * [Throughput](#throughput)
    + [Metric: `db.session.count`](#metric-dbsessioncount)
    + [Metric: `db.session.active.count`](#metric-dbsessionactivecount)
    + [Metric: `db.transaction.count`](#metric-dbtransactioncount)
    + [Metric: `db.transaction.rate`](#metric-dbtransactionrate)
    + [Metric: `db.transaction.latency`](#metric-dbtransactionlatency)
    + [Metric: `db.sql.count`](#metric-dbsqlcount)
    + [Metric: `db.sql.rate`](#metric-dbsqlrate)
    + [Metric: `db.sql.latency`](#metric-dbsqllatency)
    + [Metric: `db.io.read.rate`](#metric-dbioreadrate)
    + [Metric: `db.io.write.rate`](#metric-dbiowriterate)
    + [Metric: `db.task.wait_count`](#metric-dbtaskwait_count)
    + [Metric: `db.task.avg_wait_time`](#metric-dbtaskavg_wait_time)
  * [Performance](#performance)
    + [Metric: `db.cache.hit`](#metric-dbcachehit)
    + [Metric: `db.sql.elapsed_time`](#metric-dbsqlelapsed_time)
    + [Metric: `db.lock.count`](#metric-dblockcount)
    + [Metric: `db.lock.time`](#metric-dblocktime)
  * [Resource Usage](#resource-usage)
    + [Metric: `db.disk.usage`](#metric-dbdiskusage)
    + [Metric: `db.disk.utilization`](#metric-dbdiskutilization)
    + [Metric: `db.cpu.utilization`](#metric-dbcpuutilization)
    + [Metric: `db.mem.utilization`](#metric-dbmemutilization)
    + [Metric: `db.tablespace.size`](#metric-dbtablespacesize)
    + [Metric: `db.tablespace.used`](#metric-dbtablespaceused)
    + [Metric: `db.tablespace.utilization`](#metric-dbtablespaceutilization)
    + [Metric: `db.tablespace.max`](#metric-dbtablespacemax)
  * [Maintenance](#maintenance)
    + [Metric: `db.backup.cycle`](#metric-dbbackupcycle)

<!-- tocstop -->

## Availability

### Metric: `db.status`

This metric is [required][MetricRequired].

<!-- semconv metric.db.status(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.status` | Gauge | `{status}` | The status of the database. 1 (Active), 0 (Inactive) |
<!-- endsemconv -->

### Metric: `db.instance.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.instance.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.instance.count` | UpDownCounter | `{instance}` | The total number of instances of database. |
<!-- endsemconv -->

### Metric: `db.instance.active.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.instance.active.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.instance.active.count` | UpDownCounter | `{instance}` | The total number of active instances of database. |
<!-- endsemconv -->

## Throughput

### Metric: `db.session.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.session.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.session.count` | UpDownCounter | `{session}` | The number of database sessions. |
<!-- endsemconv -->

### Metric: `db.session.active.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.session.active.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.session.active.count` | UpDownCounter | `{session}` | The number of active database sessions. |
<!-- endsemconv -->

### Metric: `db.transaction.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.transaction.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.transaction.count` | Gauge | `{transaction}` | The number of transactions per second. |
<!-- endsemconv -->

### Metric: `db.transaction.rate`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.transaction.rate(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.transaction.rate` | Gauge | `{transaction}` | The number of transactions per second. |
<!-- endsemconv -->

### Metric: `db.transaction.latency`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.transaction.latency(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.transaction.latency` | Gauge | `s` | The average transaction latency. |
<!-- endsemconv -->

### Metric: `db.sql.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.sql.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.sql.count` | UpDownCounter | `{sql}` | The number of SQLs |
<!-- endsemconv -->

### Metric: `db.sql.rate`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.sql.rate(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.sql.rate` | Gauge | `{sql}` | The number of SQL per second. |
<!-- endsemconv -->

### Metric: `db.sql.latency`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.sql.latency(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.sql.latency` | Gauge | `s` | The average SQL latency. |
<!-- endsemconv -->

### Metric: `db.io.read.rate`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.io.read.rate(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.io.read.rate` | Gauge | `By` | The physical read per second. |
<!-- endsemconv -->

### Metric: `db.io.write.rate`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.io.write.rate(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.io.write.rate` | Gauge | `By` | The physical write per second. |
<!-- endsemconv -->

### Metric: `db.task.wait_count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.task.wait_count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.task.wait_count` | UpDownCounter | `{task}` | Number of waiting tasks. |
<!-- endsemconv -->

### Metric: `db.task.avg_wait_time`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.task.avg_wait_time(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.task.avg_wait_time` | Gauge | `s` | Average task wait time. |
<!-- endsemconv -->

## Performance

### Metric: `db.cache.hit`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.cache.hit(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.cache.hit` | Gauge | `1` | The cache hit ratio/percentage |
<!-- endsemconv -->

<!-- semconv metric.db.cache.hit(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `type` | string | The type of cache. | `Default`; `Keep` | Recommended |
<!-- endsemconv -->

### Metric: `db.sql.elapsed_time`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.sql.elapsed_time(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.sql.elapsed_time` | UpDownCounter | `s` | The elapsed time in second of the query |
<!-- endsemconv -->

<!-- semconv metric.db.sql.elapsed_time(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `sql_id` | string | The sql statement id. | `128364` | Required |
| `sql_text` | string | The text of sql statement. | `select 1 from dual` | Recommended |
<!-- endsemconv -->

### Metric: `db.lock.count`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.lock.count(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.lock.count` | UpDownCounter | `{lock}` | The number of database locks. |
<!-- endsemconv -->

<!-- semconv metric.db.lock.count(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `type` | string | The type of lock. | `Transaction`; `Object` | Recommended |
<!-- endsemconv -->

### Metric: `db.lock.time`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.lock.time(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.lock.time` | UpDownCounter | `s` | The lock elapsed time |
<!-- endsemconv -->

<!-- semconv metric.db.lock.time(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `blocker_sess_id` | string | The blocker session identifier. | `4657` | Recommended |
| `blocking_sess_id` | string | The blocking session identifier. | `2354` | Recommended |
| `lock_id` | string | The lock ID. | `223` | Required |
| `locked_obj_name` | string | The locked object name. | `table1`; `table2` | Recommended |
<!-- endsemconv -->

## Resource Usage

### Metric: `db.disk.usage`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.disk.usage(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.disk.usage` | UpDownCounter | `By` | The size (in bytes) of the used disk space. |
<!-- endsemconv -->

<!-- semconv metric.db.disk.usage(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `path` | string | The disk path. | `/root/dameng/data/` | Recommended |
<!-- endsemconv -->

### Metric: `db.disk.utilization`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.disk.utilization(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.disk.utilization` | Gauge | `1` | The percentage of used disk space |
<!-- endsemconv -->

<!-- semconv metric.db.disk.utilization(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `path` | string | The disk path. | `/root/dameng/data/` | Recommended |
<!-- endsemconv -->

### Metric: `db.cpu.utilization`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.cpu.utilization(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.cpu.utilization` | Gauge | `1` | The percentage of used CPU. |
<!-- endsemconv -->

### Metric: `db.mem.utilization`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.mem.utilization(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.mem.utilization` | Gauge | `1` | The percentage of used memory |
<!-- endsemconv -->

### Metric: `db.tablespace.size`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.tablespace.size(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.tablespace.size` | UpDownCounter | `By` | The size (in bytes) of the tablespace. |
<!-- endsemconv -->

<!-- semconv metric.db.tablespace.size(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `tablespace_name` | string | The identifier of the tablespace. | `User` | Required |
<!-- endsemconv -->

### Metric: `db.tablespace.used`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.tablespace.used(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.tablespace.used` | UpDownCounter | `By` | The used size (in bytes) of the tablespace. |
<!-- endsemconv -->

<!-- semconv metric.db.tablespace.used(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `tablespace_name` | string | The identifier of the tablespace. | `User` | Required |
<!-- endsemconv -->

### Metric: `db.tablespace.utilization`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.tablespace.utilization(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.tablespace.utilization` | Gauge | `1` | The used percentage of the tablespace. |
<!-- endsemconv -->

<!-- semconv metric.db.tablespace.utilization(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `tablespace_name` | string | The identifier of the tablespace. | `User` | Required |
<!-- endsemconv -->

### Metric: `db.tablespace.max`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.tablespace.max(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.tablespace.max` | UpDownCounter | `By` | The max size (in bytes) of the tablespace. |
<!-- endsemconv -->

<!-- semconv metric.db.tablespace.max(full) -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `tablespace_name` | string | The identifier of the tablespace. | `User` | Required |
<!-- endsemconv -->

## Maintenance

### Metric: `db.backup.cycle`

This metric is [recommended][MetricRecommended].

<!-- semconv metric.db.backup.cycle(metric_table) -->
| Name     | Instrument Type | Unit (UCUM) | Description    |
| -------- | --------------- | ----------- | -------------- |
| `db.backup.cycle` | Gauge | `s` | Backup cycle. |
<!-- endsemconv -->

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