# Kubernetes Deployment
## Observability in Kubernetes
- Due to the ephemeral pods, dynamic scaling, complex networking, the observability is needed for k8s environment.
- Need metadata like pod name, namespace, labels, and node information.
## Capabilities of Otel operator
- Ports and networking: the operator automatically create k8s services for exposed port.
- Monitoring and endpoints: operator discovers and configures monitoring endpoints.
- Upgrade and lifecycle: operator also manages automatic upgrades or disables them per instance.
## Auto-instrumentation with Otel operator
- Injects instrumentation libraries, sets configuration, and ensures all workloads send telemetry consistently to the Otel collector.
### Instrumentation custom resource (CR)
- Tells the operator how to setup auto-instrumentation across workloads.
- Holds common configuration like exporter endpoints, propagators, and sampling rules.
- Automatically used by annotated applications without extra code changes.
- Resource kind: Instrumentation.
### Annotating workloads
- **"true"**: use the default instrumentation in the namespace.
- **"name"**: use a specific instrumentation CR in the same namespace.
- **"ns/name"**: reference an instrumentation in another namespace.
- **"false"**: opt-out; patch existing deployments with kubectl patch as shown.
```yaml
apiVersion: v1
kind: Namespace
metadata:
	name: apps
	annotation:
		instrumentation.opentelemetry.io/inject-python: 'true'
```
## Collector deployment patterns
### DaemonSet
- Run 1 collector pod per node for complete node coverage.
- Collects node-local telemetry:
	- Container logs
	- Node, pod and container metrics
	- Host-level metrics
- Receives workload telemetry locally.
- Enriches telemetry with k8s metadata.
- Buffers and retries locally, ensuring resilience if the gateway/exporter is down.
- Typically forwards telemetry to the central deployment/gateway collector for processing and export.
### Gateway
- Gateway collector serves as the central point for aggregating and exporting cluster telemetry.
- Runs as centralized service (deployment with 1-3 replicas, behind a service dns).
- Act as a gateway endpoint for DeamonSet collectors and sometimes directly from pods.
- Performs aggregation, batching, transformations, sampling, and retries before export.
- Provides load balancing and scaling for ingestion across replicas.
### Sidecar
- Connect directly with the pod, isolate scope per-pod.
### StatefulSet
- Useful for static scraping (Target Allocator).
-  Used for tail-based sampling.
