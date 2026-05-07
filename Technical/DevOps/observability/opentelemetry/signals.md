# Signals
## Traces
- Request flow across services:
	- Spans
	- Parent-child relationships
	- Timing data
## Metrics
- Numerical measurements:
	- Counters
	- Gauges
	- Histograms
## Logs
- Event records:
	- Timestamps
	- Severity levels
	- Structured data
## Baggage
- Contextual information that resides next to context.
- It is a key-value store, which can propagated alongside context while span attribute only reside within a span.
- Baggage means you can pass data across services and processes, making it available to add to traces, metrics, or logs in those services.
- Baggage is often used in tracing to propagate additional data across services.
### Usage
- Baggage is best used to include information typically available only at the start of a request further downstream. This can include things like Account Identification, User IDs, Product IDs, and origin IPs, for example.
### Security consideration
- Sensitive data can be shared with unintended resources. This caused by the automatic instrumentation includes baggage in most of the service's network requests.
- If the request is sent via HTTP, it is visible for anyone.
## Profiles