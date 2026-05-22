# Instrumentation

## Definition

- For a system to be observable, it must be instrumented.
- Code from the system's component must emit signals.

## Type

- Code-based/manual instrumentation
- Library-based instrumentation
- Zero-code/auto-instrumentation

| Aspect                  | Zero-Code             | Library-Based                  | Code-Based                  |
| ----------------------- | --------------------- | ------------------------------ | --------------------------- |
| Code changes required   | None                  | Minor imports                  | Full-developer control      |
| Coverage                | Common libraries      | Supported libraries            | Anything in code            |
| Custom logic visibility | No                    | Limited                        | Full                        |
| Setup time              | Fast                  | Moderate                       | Slower                      |
| Best for                | Ops, fast on-boarding | Maintainer using common stacks | Developer need deep control |

## Components

- Exporter: send telemetry data to observability backends and destinations.
- Processor: transform, filter, and enrich telemetry data as it flows through the pipeline.

## Code-based instrumentation

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

## Library-based instrumentation

- OTel provides instrumentation libraries for many libraries, which is typically done through library hooks or monkey-patching library code.
- Monkey-patching is a technique used to dynamically modify or extend the behavior of code at runtime without changing the original source code.

### How library instrument

1. Method interception
2. Automatic span creation
3. Data captured
4. Hooks into library behavior
5. Add attributes using semantic conventions

### When to use instrumentation libraries

- When you want to instrument frameworks you often use and share that setup across teams.
- When you're extending Otel to support a custom or in-house library.
- When you want a consistent observability layer across all services and tech stack.
- Refer to the instrumentation libraries, when you're extending Otel to support your own custom or internal libraries.

### Note

- You have to configure exporter or processor.
- It depends on the SDK.

## Zero-code/auto-instrumentation

- Add observability to one or more applications without having to edit the source by adding the API and SDK as an agent or agent-like installation.
  ![](/image/Pasted%20image%2020260509071956.png)

### Technique

- Monkey-patching (Python, JavaScript)
- Bytecode manipulation (.NET, Java): modify the compiled code at runtime or load time to inject additional behavior
- eBPF

### Configuration options

- Environment variables
- System properties
- Startup arguments

### Extensions

- Extensions allow you to enhance or customize OpenTelemetry Agent behavior without modifying the agent or creating a separate build.

#### Capabilities

- Add custom span, processor, exporters, samplers, and propagators.
- Inject new instrumentation modules.
- Modify or filter span attributes.
- Override or disable existing instrumentation.

## Span Processor

- Hooks into `onStart()` and `onEnd()` of spans.
- Only runs if the span is recording.
- Processes the spans before they are exported.
- Types:
  - `SimpleSpanProcessor`: collect each span to the exporter as soon as it ends. Used for testing.
  - `BatchSpanProcessor`: collects spans and sends them in batches on a schedule. Used for production environment. Used by default with default batch size is 512.

## Span Exporter

- Responsible for delivery span to the destination.
- Types:
  - `ConsoleExporter`
  - `OTLPExporter`
  - `JaegerExpoter`
  - `ZipkinExporter`
- Key behaviors:
  - `export()`: sends a batch of spans to a destination
  - `shutdown()`: cleans up and stops exporting
  - `forceFlush()`: immediately tries to export any pending spans

## Span Sampling

- If the large majority of your requests are successful and finish with acceptable latency and no errors, you do not need 100% of your traces to meaningfully observe your applications and systems. You just need the right sampling.

### Terminology

- **Sampled**: A trace or span is processed and exported. Because it is chosen by the sampler as a representative of the population, it is considered “sampled”.
- **Not sampled**: A trace or span is not processed or exported. Because it is not chosen by the sampler, it is considered “not sampled”.

### Benefits

- Reduce cost of observability without losing visibility.
- More accurate based on the representativeness principle.

### When to sample

- You generate 1000 or more traces per second.
- Most of your trace data represents healthy traffic with little variation in data.
- You have some common criteria, like errors or high latency, that usually means something is wrong.
- You have domain-specific criteria you can use to determine relevant data beyond errors and latency.
- You can describe some common rules that determine if data should be sampled or dropped.
- You have a way to tell your services apart, so that high- and low-volume services are sampled differently.
- You have the ability to route unsampled data (for “just in case” scenarios) to low-cost storage systems.

### When to not sample

- You generate very little data (tens of small traces per second or lower).
- You only use observability data in aggregate, and can thus pre-aggregate data.
- You are bound by circumstances such as regulation that prohibit dropping data (and cannot route unsampled data to low-cost storage).

### Sampling technique

#### Head sampling

- Make a sampling decision as early as possible. A decision to sample or drop a span or trace is not made by inspecting the trace as a whole.
- Pros:
  - Easy to understand
  - Easy to configure
  - Efficient
  - Can be done at any point in the trace collection pipeline
- Cons: can't make decision based on data in the entire trace.

#### Tail sampling

- The decision to sample a trace takes place after the trace completes.
  ![](/image/Pasted%20image%2020260512214413.png)
- Pros: allow much higher degree of sophistication in how you sample data based on criterias or requirements.
- Cons:
  1.  Tail sampling can be difficult to implement. Depending on the kind of sampling techniques available to you, it is not always a “set and forget” kind of thing. As your systems change, so too will your sampling strategies. For a large and sophisticated distributed system, rules that implement sampling strategies can also be large and sophisticated.
  2.  Tail sampling can be difficult to operate. The component(s) that implement tail sampling must be stateful systems that can accept and store a large amount of data. Depending on traffic patterns, this can require dozens or even hundreds of compute nodes that all utilize resources differently. Furthermore, a tail sampler might need to “fall back” to less computationally intensive sampling techniques if it is unable to keep up with the volume of data it is receiving. Because of these factors, it is critical to monitor tail-sampling components to ensure that they have the resources they need to make the correct sampling decisions.
  3.  Tail samplers often end up as vendor-specific technology today. If you’re using a paid vendor for Observability, the most effective tail sampling options available to you might be limited to what the vendor offers.
- Trade-offs:
  - Memory pressure
  - Latency: delay to decide
  - Correctness: all span same collector
  - Timeout: late traces dropped
  - Scaling: hashing/topology needed

### Strategies

- `AlwaysOnSampler`: records all spans
- `AlwaysOffSampler`: records no spans
- `TraceIdReatioBaedSampler`: records a percentage of spans
- `ParentBasedSampler`: follows the sampling decision of the parent span

#### Combine head and tail

- Optimize pipeline efficiency without losing critical insights.
- Use head sampler filters early control cost and load, tail sampler captures only the traces that matter.

### Configuration

- Environment variable: `OTEL_TRACES_SAMPLER, OTEL_TRACES_SAMPLER_ARG`
