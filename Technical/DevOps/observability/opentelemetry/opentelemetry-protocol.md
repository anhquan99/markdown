# OpenTelemetry Protocol (OTLP)
## Definition
- OTLP is a wire protocol in OpenTelemetry for sending signals between apps, agent, collectors, and backends.
## How it works
- OTLP defines schema/data model via Protocol Buffers (Protobuf) messages.
- OTLP decides how messages are sent (transport, compression, errors/responses).
## Data model structure
```
|-resource------------------------------------------|
| service.name, host.name, deployment.environment   |
||-----scope----------------------------------------|
|| library name and version                         |
|||----signal data----------------------------------|
|||| span, metrics, logs                            |
----------------------------------------------------|
```
### Resource
- Defines telemetry source via resource attributes (process, pod, namespace, deployment).
- It is immutable.
- The metadata of telemetry resource.
### Scope
- A logical unit within application code which emitted telemetry is associated.
- Defined by a name and version pair when obtaining tracer, meter, or logger instances.
- It can represent a module, package, or class.
- Each span, metric, or log record is associated with its scope.
- Enables slicing telemetry data by scope in observability backends.
- Helps identify performance issues and library version usage.
### Signal data
- Contains the measurements themselves.
- Each record carries ID, timestamps, attributes, and events.
## Transportation
### OTLP/gRPC
![](/image/Pasted%20image%2020260507115151.png)
- Signal is encoded into Protobuf binary format.
- Multiplexing: multiple streams over single connection.
- Bidirectional streaming support.
- Built-in flow control and header compression (HPACK).
- Default port: 4317.
- Required HTTP/2.
- The routing happens through gRPC service definitions in the protobuf schema.
### OTLP/HTTP
![[/Excalidraw/opentelemetry-http.excalidraw]]
- Use specific endpoint to routing.
- Default port: 4318.
### Json encoding
- Messages are encoded as JSON and the `Content-Type` is changed to `application/json`.
- Useful for debugging.
## Transport flow
![[/Excalidraw/opentelemetry-transport-flow.excalidraw]]