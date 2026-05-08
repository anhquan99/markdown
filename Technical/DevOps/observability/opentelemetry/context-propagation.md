# Context Propagation
- Signals can be correlated with each other regardless of where they are generated.
- Context propagation allows traces to build causal information about a system across services that are arbitrarily distributed across process and network boundaries.
- Context propagation is made up from context and propagation mechanism.
## Context
- A information object that carried with the signal to correlate one signal with another.
### Context manager
- Keeps track of active spans.
- Maintains the current context (trace, span, baggage).
- Ensure the right span is active during execution.
- Automatically handles entering/existing contexts.
- Essential for async and multi-threaded apps.
## Propagation
- The mechanism that moves context between services and processes.
- It serializes or deserialize the context object and provides the relevant information to be propagated from one service to another.
- Usually handled by instrumentation libraries.
- The default propagator uses the headers specified by the **W3C TraceContext** specification. Contains:
	- Trace ID
	- Span ID
	- Flags
### Propagator API components
- TextMapPropagator: used for serializing (inject) and deserializing (extract)
- Carrier: the transport that holds headers
- Setter/Getter: function used to write to or read from the carrier
- Inject: write the context into the outgoing carrier (ex: HTTP headers)
- Extract: reads context from the incoming carrier to continue the trace
- Global propagator: configured once to define the propagation strategy (ex: W3C, B3, Composite).
### Composite propagators
- Allow OpenTelemetry to inject and extract context using multiple propagation formats at once.
- Usage:
	- Services using different standards
	- Support multiple vendor integrations
	- Migrating from B3 to W3C and need backward compatibility