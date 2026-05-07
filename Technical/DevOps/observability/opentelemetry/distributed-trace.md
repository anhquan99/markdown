# Distributed Trace
## Definition
- A distributed trace shows the full journey of a request across services, systems and processes.
- Provides critical visibility into system behavior, latency bottlenecks, and failure points.
## Trace
- A trace is the path of a request through the application.
- A trace is made up of spans where each span represents a single operation or unit of work.
## Span
- A span is the fundamental single unit of operation of work in a distributed trace.
- Spans are organized hierarchically:
	- The parent span start the operation with parent span ID is null.
	- The child spans represent sub-task.
	- The sibling spans share the same parent.
### Status
- OK: success
- Error: failure
- Unset: no status set by default

