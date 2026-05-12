# Schema
## Problem
- Telemetry sources (like your instrumented applications) and observability backends are traditionally tightly coupled to the specific shape and naming of the data being transferred.
- If OpenTelemetry updates a **"semantic convention"** (for example, deciding to rename a span attribute from `deployment.environment` to `environment`), it immediately breaks any dashboards or backends that are hardcoded to look for the old name. This tight coupling makes it incredibly difficult to evolve telemetry standards over time.
## Definition
- Unlike a traditional database schema that enforces strict data types and validation, an OTel Telemetry Schema acts as a **versioned translation guide**. It explicitly defines the transformation rules required to convert telemetry data from an older version of a semantic convention to a newer one (or vice versa).
## Use cases
- **Collector-Assisted Transformation:** This is highly useful in infrastructure/DevOps pipelines. If your observability backend is legacy or not schema-aware, you can configure the OpenTelemetry Collector with a **"Schema Translate Processor"**. The Collector reads the incoming schema URL, applies the necessary transformations to convert the data to your backend's expected version, and then forwards it.
- **Schema-Aware Backends:** A modern observability backend receives a metric tagged with schema `1.2.0` but its internal database is structured for `1.1.0`. The backend fetches the schema URL, reads the translation instructions, modifies the attributes accordingly, and stores the data seamlessly.
## Configuration
- Used with the `schema_url`.
```yaml
# Defines the file format. MUST be set to 1.1.0.
file_format: 1.1.0

# The Schema URL that this file is published at.
schema_url: /schemas/1.1.0

# Definitions for each schema version in this family.
versions:
  1.1.0:
    all:
      changes:
        - rename_attributes:
            k8s.cluster.name: kubernetes.cluster.name
            k8s.namespace.name: kubernetes.namespace.name
            k8s.node.name: kubernetes.node.name
            k8s.pod.name: kubernetes.pod.name
            k8s.container.name: kubernetes.container.name
            k8s.deployment.name: kubernetes.deployment.name
            # ... other k8s renames

    resources:
      changes:
        - rename_attributes:
            telemetry.auto.version: telemetry.auto_instr.version

    spans:
      changes:
        - rename_attributes:
            attribute_map:
              peer.service: peer.service.name
            apply_to_spans:
              - "HTTP GET"

    span_events:
      changes:
        - rename_events:
            name_map: {stacktrace: stack_trace}
        - rename_attributes:
            attribute_map:
              peer.service: peer.service.name
            apply_to_events:
              - exception.stack_trace

    metrics:
      changes:
        - rename_metrics:
            container.cpu.usage.total: cpu.usage.total
            container.memory.usage.max: memory.usage.max
        - rename_attributes:
            attribute_map:
              status: state
            apply_to_metrics:
              - system.cpu.utilization
              - system.memory.usage
        - split:
            apply_to_metric: system.paging.operations
            by_attribute: direction
            metrics_from_attributes:
              system.paging.operations.in: in
              system.paging.operations.out: out

    logs:
      changes:
        - rename_attributes:
            attribute_map:
              process.executable_name: process.executable.name

  1.0.0:
    # First version of this schema family.
```