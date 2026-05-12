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
### Receivers
#### Ports
- `grpc`: 4317
- `http`: 4318
- `thrift`: 55680
#### Popular receivers
- OTLP
- Prometheus
- Hostmetric: collects metrics from the host system
- Kubelet status receiver: pulls metrics from kubelet API
- K8s cluster receiver: collects cluster-level metrics
- K8s object receiver: collects k8s objects as logs
- Filelog receiver: tails log file from system
- Journald receiver
- Syslog receiver: receives syslog messages over network
- SNMP receiver: pull metrics f rom SNMP-enabled devices
- Jaeger
- Zipkin
- OpenCensus
### Processors
- Processing telemetry as it flows through the service.
- Where data can be enriched, modified, or filtered - making the data smarter.
#### Popular processors
- Memory limit processor: prevents out of memory situations on collector
- Batch processor
- Resource detection processor: automatically detects environment metadata
- Resource processor: modifies resource attribute directly
- K8s attributes processor
- Attributes processor: modifies span, log, or metric attributes
- Transform processor: uses OTTL for powerful transformation
- Filter processor
- Probabilistic sampler processor: samples telemetry based on probability
- Tail sampling processor: samples traces after all span received
- Delta to cumulative processor: converts delta metrics to cumulative
- Cumulative to delta processor: converts monotonic cumulative metrics to delta
- Span processor
#### Order of processor matter
- Memory first: prevent crashes before processing
- Transform early: modify data before batching
- Batch last: group final processed data
### Exporters
#### Patterns
- Fan-out: list multiple exporters in the same pipeline to duplicate data
- Route by attribute: use a routing processor to send subsets to different exporters.
- Cross-pipeline forwarding: use a connector when telemetry moves to another pipeline or changes. signals
### Connectors
- Connectors join two pipelines, acting as both exporter and receiver. A connector consumes data as an exporter at the end of one pipeline and emits data as a receiver at the beginning of another pipeline. The data consumed and emitted may be of the same type or of different data types.
![](/image/Pasted%20image%2020260512201429.png)
#### Use cases
- Reuse telemetry: single ingestion for multiple pipelines
- Transform signals: derive metrics from traces
- Route/replicate: OTTL rules for environment or tenant branches
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
## Collector as an agent
- An agent is a lightweight process running on a VM.
- Agent run on many hosts across the environment.
- On k8s it is pod.
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
- Avoid scaling when backend or network bottleneck exist.
### Categories
- Stateless
  ![](/image/Pasted%20image%2020260511225200.png)
- Scraping
  ![](/image/Pasted%20image%2020260511225537.png)
- Stateful
- Load balancing
- Sharding
- Target Allocator
  ![](/image/Pasted%20image%2020260511230554.png)
### Metrics signal scaling

| Metric                                                             | Meaning                                          | Action/alert                                   |
| ------------------------------------------------------------------ | ------------------------------------------------ | ---------------------------------------------- |
| `otelcol_processor_refesed_span`                                   | Memory limiter blocked spans                     | Frequent refusals → scale up collector         |
| `otelcol_exporter_queue_capacity`<br>`otelcol_exporter_queue_size` | Exporter queue utilization                       | If size > 70% capacity → scale/tune queues     |
| `otelcol_exporter_enqueue_failed_spans`                            | Data dropped due to full exporter queue          | Investigate queue limits → adjust or scale     |
| `otelcol_loadbalancer_backned_latency`                             | Latency from load-balancing exporter to backends | High latency -> backend bottleneck / scale out |
