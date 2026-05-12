# OpenTelemetry Transformation Language (OTTL)
## Defintion
- OTTL is a small, domain-specific programming language intended to process data with OpenTelemetry-native concepts and constructs.
- OTTL lets you write clear, simple transformation rules the processor can run.
## Use cases
- Remove sensitive data
- Add or rename fields
- Filter out unwanted data
- Combine multiple fields
## Component use OTTL
### Transform processor
- Set value
- Rename a key
- Convert a metric type
### Filter processor
- Keep or drop
- Which data move?
- How does data look?
## Handle error modes
### Ignore
- The processor ignores error returned by conditions, logs them, and proceeds to the next condition.
- Recommended mode.
### Silent
- Like ignore but does not log.
### Propagte
- The processor returns the error up the pipeline, resulting in the payload being dropped from the collector.