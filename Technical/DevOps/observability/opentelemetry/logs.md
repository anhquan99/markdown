# Logs

## Definitions

- A **log** is a timestamped text record, either structured (recommended) or unstructured, with optional metadata:
  - Level
  - Source
  - Context
- Of all telemetry signals, logs have the biggest legacy. Most programming languages have built-in logging capabilities or well-known, widely used logging libraries.

## Otel log integration

![](/image/Pasted%20image%2020260510192453.png)

## Logs formats

### Structured

- JSON / Common Log Format (CLF) / Extended Log Format (ELF)
- Consistent
- Machine-readable

### Unstructured

- Free text
- Human friendly
- Hard to parse

### Semi-structured

- Key-value
- Partial structure
- Custom parsing needed

## Components

### Log appender

- Connects your existing logging library to Otel without changing your code.

### Logger provider

- Initialized once per application lifecycle.
- Configures resource metadata and exporters.
- Part of logs bridge for library authors.

### Logger

- A logger is what your code calls to generate log entries.

### Log record exporter

- Handles the delivery process with:
  - Batching
  - Retry
  - Error handling
- Pluggable exporters let you swap backends without the code changes.

### Log record

- A log record is unit of log in Otel.
- It captures:
  - Timestamp
  - Severity
  - Body
  - TraceId and SpanId
  - Attributes
  - Resource
  - Instrumentation scope

## Logs data model

- Unified schema for log records.
- Establishes a shared definition of what a log record is.
- Defines the essential data that should be captured and transferred.
- Ensures logs are stored and processed consistently.
- Enables uniform interpretation across logging systems and tools.

### Events

- Are structured logs records that adhere to a template defined by semantic conventions
- Structure:
  - Timestamp
  - Severity
  - Body
  - Resource identifiers

### Core timestamp fields

- Use 64-bit nanoseconds since the Unix epoch to ensure precision.
- Timestamp: source event time.
- ObservedTimestamp: collect receipt time.

### Severity number mapping table

- `SeverityNumber=0` MAY be used to represent an unspecified value.

| SeverityNumber range | Range name | Meaning                                                                                 |
| -------------------- | ---------- | --------------------------------------------------------------------------------------- |
| 1-4                  | TRACE      | A fine-grained debugging event. Typically disabled in default configurations.           |
| 5-8                  | DEBUG      | A debugging event.                                                                      |
| 9-12                 | INFO       | An informational event. Indicates that an event happened.                               |
| 13-16                | WARN       | A warning event. Not an error but is likely more important than an informational event. |
| 17-20                | ERROR      | An error event. Something went wrong.                                                   |
| 21-24                | FATAL      | A fatal error such as application or system crash.                                      |

## Logs API

### Components

- LoggerProvider: initializes provider
- Logger: emits logs
- LogRecord: structure for each event
- isEnabled check: optimizes performance

## Logs SDK

- Implement Logs API
- Required across all supported languages
- Mostly stable, with some parts still developing

### Shutdown and ForceFlush

- `ForceFlush()`: export pending log records.
- `Shutdown()`: stop new loggers and clean up.

### LogRecordProcessor architecture

- LogRecord Emission emits records to:
  - Simple processor: send logs immediately
  - Batching processor: buffers and exports in intervals
  - Custom processor: enriches, filters, or routes logs
