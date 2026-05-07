# Distributed Trace
## Definition
- A distributed trace shows the full journey of a request across services, systems and processes.
- Provides critical visibility into system behavior, latency bottlenecks, and failure points.
## Trace
- A trace is the path of a request through the application.
- A trace is made up of spans where each span represents a single operation or unit of work.
### Trace provider
- A Tracer Provider is a factory for `Tracer`s. In most applications, a Tracer Provider is initialized once and its lifecycle matches the application’s lifecycle. Tracer Provider initialization also includes Resource and Exporter initialization. It is typically the first step in tracing with OpenTelemetry. In some language SDKs, a global Tracer Provider is already initialized for you.
### Tracer
- Creates spans with information about the operation.
### Trace exporters
- Send traces to a consumer.
## Context propagation
- The [context propagation](context-propagation.md) is the core concept that enables Distributed Tracing. With Context Propagation, Spans can be correlated with each other and assembled into a trace, regardless of where Spans are generated.
## Span
- A span is the fundamental single unit of operation of work in a distributed trace.
- Spans are organized hierarchically:
	- The parent span start the operation with `parent_id` is null.
	- The child spans represent sub-task.
	- The sibling spans share the same parent.
### Information in a span
- ID
- Name
- Parent span ID
- Start and end timestamps
- Span context
- Attributes
- Span events
- Span links
- Span status
### Span name
- It should be meaningful: `{verb} {object}`.
### Span context
- An immutable object on every span that contains the following:
	- The Trace ID representing the trace that the span is a part of
	- The span’s Span ID:
		- 8 bytes (64 bits)
		- Typically represented as a 16 character hexadecimal string
	- Trace Flags:
		- A binary encoding containing information about the trace
		- Control tracing behavior, mainly related to: sampling, future use case.
		- 8 bits (1 byte)
		- Represented as 2 hexadecimal characters
		- Mandatory
	- Trace State, a list of key-value pairs that can carry vendor-specific trace information
### Attributes
- Key-value pairs that contain metadata that used to annotate a span to carry information about the operation it is tracking.
- Enhance business and technical context and make traces searchable, filterable, and meaningful.
- Follow SDK rules and use semantic conventions where possible.
- If the timestamp isn’t meaningful, attach the data as span attributes.
#### Span resource
- A resource is a collection of attributes that identify the source of telemetry data.
- Resources are attached during initialization (ex: TracerProvider or MeterProvider).

### Span events
- A span event can be viewed as a structured log message (or annotation) on a span, typically used to denote a meaningful, singular point in time during the span's duration.
- If the timestamp in which the operation completes is meaningful or relevant, attach the data to a span event.
### Span links
- Helps user associate one span with one or more spans, implying a casual relationship but not parent-child.
- Examples: batch job, queue message trigger multiple actions.
- Spans run in:
	- Parallelly
	- Asynchronously
	- Independently
### Span status
- OK: success
- Error: failure
- Unset: no status set by default
### Span kind
- Classifies a span's role in a distributed interaction. It clarifies the direction of the call and its communication style.
- Types:
	- Client: synchronous outgoing remote call, the synchronous does not refer to async/await but to the fact that it is not queued for later processing.
	- Server: synchronous incoming remote call or remote procedure call.
	- Internal: operations which do not cross a process boundary.
	- Producer: asynchronously operation such as queue.
	- Consumer: operation process request from producer.
```json
{
  "name": "/v1/sys/health",
  "context": {
    "trace_id": "7bba9f33312b3dbb8b2c2c62bb7abe2d",
    "span_id": "086e83747d0e381e"
  },
  "parent_id": "",
  "start_time": "2021-10-22 16:04:01.209458162 +0000 UTC",
  "end_time": "2021-10-22 16:04:01.209514132 +0000 UTC",
  "status_code": "STATUS_CODE_OK",
  "status_message": "",
  "attributes": {
    "net.transport": "IP.TCP",
    "net.peer.ip": "172.17.0.1",
    "net.peer.port": "51820",
    "net.host.ip": "10.177.2.152",
    "net.host.port": "26040",
    "http.method": "GET",
    "http.target": "/v1/sys/health",
    "http.server_name": "mortar-gateway",
    "http.route": "/v1/sys/health",
    "http.user_agent": "Consul Health Check",
    "http.scheme": "http",
    "http.host": "10.177.2.152:26040",
    "http.flavor": "1.1"
  },
  "events": [
    {
      "name": "",
      "message": "OK",
      "timestamp": "2021-10-22 16:04:01.209512872 +0000 UTC"
    }
  ]
}

```