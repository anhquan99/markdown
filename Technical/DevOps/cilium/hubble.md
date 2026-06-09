# Hubble
## Definition
- Hubble is the Cilium observability for k8s networking.
- It uses eBPF to capture and analyze network flows in real time, giving operators and developers rich visibility into service-to-service traffic, HTTP requests, Kafka usage, DNS behavior, and policy-enforced denials.
## Usage
- Hubble answers for critical operation question such as:
	- Which services communicate with each other?
	- How frequently do they communicate?
	- What does the service dependency graph look like?
	- Which HTTP endpoints are being called and with what response rates?
	- Which Kafka topics are services producing to or consuming from?
- Quickly narrow root causes:
	- Is the failure DNS-related or a service misconfiguration?
	- Is the issue at the application layer (L7/HTTP) or the network/transport layer (L4/TCP)?
	- Are packets being dropped or connections reset?
	- Which policies are blocking traffic?
## Components
| Component     | Role                                                                        | Typical Deployment                             |
| ------------- | --------------------------------------------------------------------------- | ---------------------------------------------- |
| Hubble server | Collects flows and visibility data using eBPF on each node                  | Runs inside the Cilium agent (no per-node pod) |
| Hubble relay  | Aggregates flows from all Hubble servers and provides a single access point | Deployment (pod) in cluster                    |
| Hubble CLI    | Local client for querying flows and streaming events                        | Installed on developer/operator workstation    |
| Hubble UI     | Web-based visualization for interactive filtering and dependency graphs     | Deployment (pod) in cluster                    |
## Configuration
- Manifest file:
```yaml
hubble:
  # Enable Hubble (true by default).
  enabled: true

  # Enable Hubble Relay (aggregates flows from Hubble servers)
  relay:
    enabled: true

  # Whether to enable the Hubble UI.
  ui:
    enabled: true
```
- Need to restart Cilium after update configuration.
### Enable Prometheus and Grafana metrics
```yaml
# cilium-values.yaml
prometheus:
  enabled: true

operator:
  prometheus:
    enabled: true

hubble:
  # Ensure Hubble is enabled separately if you want flow telemetry
  enabled: true
  metrics:
    enabled:
      - "dns:query;ignoreAAAA"
      - drop
      - tcp
      - flow
      - icmp
      - http
    enableOpenMetrics: true
```

|Helm key|Purpose|Notes|
|---|---|---|
|`prometheus.enabled: true`|Enables ServiceMonitor and related objects created by the Cilium chart so Prometheus Operator can discover Cilium components.|Required when using Prometheus Operator / kube-prometheus-stack.|
|`operator.prometheus.enabled: true`|Exposes Prometheus metrics for the Cilium Operator.|Creates a Service and ServiceMonitor for the operator.|
|`hubble.enabled: true`|Turns on Hubble to collect flow telemetry.|Only needed if you want flow-level visibility.|
|`hubble.metrics.enabled`|List of Hubble metric families to export.|`"dns:query;ignoreAAAA"` is a single metric spec and must be quoted due to the colon/semicolon.|
|`hubble.metrics.enableOpenMetrics: true`|Export metrics using OpenMetrics format so Prometheus can scrape them.|Needed for correct scraping and metric exposition.|