# Instrumentation
## Definition
- For a system to be observable, it must be instrumented.
- Code from the system's component must emit signals.
## Type
- Code-based/manual instrumentation
- Library-based instrumentation
- Zero-code/auto-instrumentation
## Code-based
- Manual instrumentation is the process where developers explicitly add telemetry calls (using OpenTelemetry API) within their application's source code.
- Provides fine-grained control over what operations are traced, what metadata is captured, and when telemetry signals are created.
### Steps
1. Import the OpenTelemetry API and SDK
2. Configure the OpenTelemetry API
3. Configure the OpenTelemetry SDK
4. Create telemetry data
5. Export data
### No-Op benefits
- Instruments code safely - no need to setup a backend right away.
- App or library is now telemetry-ready, even if the runtime doesn't configure tracing.
- You can plug in a TracerProvider later, and everything will "just work".
- Ensures that libraries using the OpenTelemetry API work safely even without SDK.
### Tracing API workflow
1. Requesting a Tracer
2. Create a Span
3. Sampling Decision
4. Span Processor
5. Applying SpanLimits (ie: Attribute limits)
6. Exporting the Span
7. Trace visualization
### Component of tracing API

| Component     | Role                                              |
| ------------- | ------------------------------------------------- |
| TraceProvider | Entry point provides tracers                      |
| Tracer        | Create spans, the actual trace logic              |
| Span          | Represents a single timed operation or event      |
| SpanExporter  | Encodes and send span data to a telemetry backend |
