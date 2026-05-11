# Collector
## Definition
- A standalone vendor-agnostic service that receive, process and export telemetry data.
![](/image/Pasted%20image%2020260511140727.png)
## Architecture
- Receivers: ingest telemetry
- Processors
- Exporters: send data to backends as long as Otel compatible
- Extension: support collector itself, enabling status monitoring and troubleshooting
	- health check
	- z-pages
## Benefits
- Decouples code from backend
- Centralizes configuration
- Allows horizontal scaling
- Enforces rule such as filtering sensitive data
## Consideration
- Resource allocation plan for CPU and memory usage
- Reliability through replicas behind a load balancer
- Monitor via health-check and z-pages endpoints
## Distributions
### Core
- Minimal core keeps upgrades predictable.
- Receivers:
	- OTLP
	- nop
- Exporters:
	- OTLP
	- nop
	- debug
- Processors:
	- batch
	- memory_limiter
- Extensions:
	- health_check
	- pprof
	- zpages
### Community contributed
- Add many community and vendor specific components.
- Not default for production.
- Validate before use.
- Some capabilities are guarded by feature gates.
### Comparison

| Core            | Contrib          |
| --------------- | ---------------- |
| Minimal set     | Broad coverage   |
| Slower change   | Faster change    |
| Smaller surface | More integration |
| Easier to audit | Varied Stability |
### Usage for production
- Use components from contribution but not all.
- Build your own with validated stable components.
- Use vendor distribution for vendor-specific components.
## Configuration:
- Required:
	- Receivers
	- Exporters
	- Service
- Optional:
	- Processors
	- Connectors: connect pipelines, act as both exporter and receiver. Enable cross-pipeline flows without extra load