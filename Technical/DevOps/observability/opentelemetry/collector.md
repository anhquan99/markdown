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
## Debug
- The Otel collector automatically emits internal logs to `stderr`.
- Default log level is INFO.
- Metrics port: 8888
- Health check port: 13133
### `zPages`
- A simple way to expose live diagnostics.
- Lightweight, no backend-needed runtime diagnostics.
- Port: 55679
#### Endpoints
- `servicez` shows high-level collector information.
- `pipelinez` reveals how the collector has assembled all pipelines, which includes receivers, processors, and exporters.
- `extensionz` lists which extensions are currently enabled.
- `featurez` exposes all feature gates and their states.
- `tracez` provides live spans and latency samples.
### `pprof`
- Built-in Go profiler that helps us understand how the collector behaves under load.
- Not for daily use, used for deep performance debugging by advanced operators.
- Port: 1777
## Scaling collector
### Categories
- Stateless
- Scraping
- Stateful
- Load balancing
- Sharding
- Target Allocator
### Metrics signal scaling

| Metric                                                             | Meaning                                          | Action/alert                                  |
| ------------------------------------------------------------------ | ------------------------------------------------ | --------------------------------------------- |
| `otelcol_processor_refesed_span`                                   | Memory limiter blocked spans                     | Frequent refusals → scale up collector        |
| `otelcol_exporter_queue_capacity`<br>`otelcol_exporter_queue_size` | Exporter queue utilization                       | If size > 70% capacity → scale/tune queues    |
| `otelcol_exporter_enqueue_failed_spans`                            | Data dropped due to full exporter queue          | Investigate queue limits → adjust or scale    |
| `otelcol_loadbalancer_backned_latency`                             | Latency from load-balancing exporter to backends | High latency → backend bottleneck / scale out |
