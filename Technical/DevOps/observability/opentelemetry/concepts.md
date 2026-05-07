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
### Semantic conventions
- Defines keys and values which describe commonly observed concepts, and operations used by applications.
- It ensures consistent naming across languages and instrumentation libraries.
- Why semantic conventions matters?
	- Solve the inconsistency problem
	- Power correlation, dashboards, alerting, and automation
	- Core enabler of vendor-neutral observability
#### Guidelines
- Reuse existing conventions whenever meaning align.
- Using domains rather than organization.
- Use dot `.` for hierarchy.
- Use underscore `_` for multi-word.
- Use widely known abbreviation (IP, DB, HTTP, ...).
- Add domain to make abbreviation clear (ie: `container.oci.version`, ...).
- Avoid ambiguity.
- Don't reuse the same name across type.
- Be explicit with new names.
## Component
### Foundation: definitions and standards
- Specification
- OpenTelemetry Protocol (OTLP)
- Semantic convention
### Data generation: APIs and SDKs
- APIs: how you instrument your code to produce metrics, traces, and logs
- SDKs: implement the API and provide exporters, config, and utilities
- Instrumentation libraries: pre-built integrations for common libraries and frameworks
- Auto instrumentation agents: generate telemetry without code changes
- Contrib: community maintained instrumentation and plugins
- Distribution: a wrapper around an upstream OpenTelemetry repository with some custominzations (**not a folk**).
### Data processing: collector and tools
- OpenTelemetry collector: receives, processes, and exports telemetry data in addition to handling transformation, filtering, batching, ...
- Supporting tools:
	- OpenTelemetry Operator (k8s)
	- ...
## Architecture
![](/Image/Pasted%20image%2020260506221653.png)