# APIs And SDKs
## APIs
- The APIs have a minimal implementation, which mean they have basic no-op (no operations) to ensure APIs work even if the SDKs are not installed.
- Each language have their own specific API built on a language-agnostic core.
- APIs handle the "what" - defining how to create and manage spans, metrics, and logs.
### Characteristics
- Self-sufficient dependency:
	- Application can compile and run successfully
	- Avoid runtime errors
	- Application or 3rd libs can depend on the API without requiring the SDK
- No telemetry output by default: run as no-op mode when the SDK is not installed
- Minimal default implementation
## SDKs
- The SDKs are the implementation of APIs, where the APIs are the contract of the application and the SDKs.
- SDKs handle the "how" - managing context propagation, sampling, batching, and exporting data to backends.
### Roles interaction with SDK
#### Instrumentation authors - API
- Traces/spans/metrics/logs
- Events
- Attributes
#### Application owners - Setup
- Builders
- Env vars
- Configs
#### Plugin authors
- Exporters
- Processors
- Samplers
## Separation of concern advantages
- Instrument code consistently across different languages and environments.
- Swap or update SDK without changing your instrumentation logic.
## Client design principles
![](/image/Pasted%20image%2020260507082629.png)
### Client definition
- A client in this context refer to the language-specific library that installed in your application.
### Requirements
- The API must be well-defined and clearly decoupled from the implementation, allows end users to consume API only without also consuming the implementation.
- 3rd party libraries and frameworks that add instrumentation only depend on the API of OpenTelemetry client to enable clean separation. API and SDK libs must be provided as independent artifacts.
- Developers should be free to use or not use any OpenTelemetry implementation at all, even though the applications are already instrumented.
- The SDK must be clearly separated into wire protocol-independent parts that implement common logic.
- SDK should include these exporters:
	- logs, metrics, trace
	    - OTLP (OpenTelemetry Protocol).
	    - Standard output (or logging) to use for debugging and testing as well as an input for the various log proxy tools.
	    - In-memory (mock) exporter that accumulates telemetry data in the local memory and allows to inspect it (useful for e.g. unit tests).
	- metrics
	    - Prometheus.
	- trace
	    - Zipkin.