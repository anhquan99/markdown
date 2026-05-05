# Concepts
## Definition
- OpenTelemetry is an open-source observability framework providing standard APIs, SDKs, and tools to instrument, collect, and export metrics, traces, and logs for any system for language.
### Key characteristics
- Vendor-neutral and open source
- Language-agnostic and portable
- Works with and backed, open source or commercial
- Not a backend itself; no storage or visualization
- Designed for flexibility across modern infrastructure
### How?
- **Telemetry should be easy:** fast time-to-value, smart defaults, and a great developer experience.
- **Telemetry should be universal:** unified protocols and conventions across all signals and languages.
- **Telemetry should be vendor-neutral:** no lock-in; freedom to choose and switch observability backends.
- **Telemetry should be loosely coupled:** composable architecture; use only what you need, when you need it.
- **Telemetry should be built-in:** instrumentation should be native to the stack, not an afterthought.
### Engineering values
- **Compatibility:** follow standards, enable interoperability.
- **Stability:** maintain API stability and backward compatibility.
- **Resilience:** handle failure gracefully, keep collecting.
- **Performance:** Telemetry should not slow your apps down.
## Specification
- Defines APIs, SDKs, signal types, data models, and protocols.
- Ensures consistent implementation across languages and vendors.
### Structure of the specification
- API and SDK: define how signals (traces, metrics, logs) are captured.
- Semantic conventions
- OTLP protocol: define how telemetry is transported or the formats.
- Context propagation
- Instrumentation guidelines: provides best practices for consistent implementation.