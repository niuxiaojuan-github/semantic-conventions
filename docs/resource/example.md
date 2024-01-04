<!-- semconv example -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| [`service.name`](README.md) | string | Logical name of the service. [1] | `shoppingcart` | Required |
| `test.attr` | int | A brief description. [2] | `134217728` | Recommended |

**[1]:** MUST be the same for all instances of horizontally scaled services. If the value was not specified, SDKs MUST fallback to `unknown_service:` concatenated with [`process.executable.name`](process.md#process), e.g. `unknown_service:bash`. If `process.executable.name` is not available, the value MUST be set to `unknown_service`.

**[2]:** It's a note
<!-- endsemconv -->